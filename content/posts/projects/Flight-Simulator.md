
---
title: "Flight Simulator"
date: 2023-04-24T01:18:59-08:00
draft: false
author: ["Lucas Li"]
tags: ["Projects"]
featured_image: '/images/others/plane_cover.png'
description: "quaternions interpolation and animation using OpenGL"
toc: true
---

The [Flight Simulator](https://github.com/GelzoneXUnsas/Flight-Simulator/) program involves creating an action-packed scene with flying planes, dynamic camera movements, and procedural generated terrain. <!--more--> 

![](/images/others/plane_demo.gif)

The project's goal is to exhibit [OpenGL](https://www.opengl.org/)'s math library [GLM](https://www.opengl.org/sdk/libs/GLM/) to practice the uses of quaternions and interpolations. The key focus is on setting up curved flight paths using quaternions & splines, ensuring accurate plane rotations, and incorporating various maneuvers like barrel rolls and screws.

## Overview

The project is a software development project that uses modern technologies and frameworks to create a graphics-intensive application.

- C++11 as the main language
- Modern OpenGL/GLSL as the graphics API
- GLEW for cross-platform initialization of OpenGL
- GLFW for windowing
- Eigen for linear algebra

## Assets
Besides using a plane.OBJ file, the following height map is used as the base of the terrain:

- Height map
![](/images/others/plane_height.jpg)

## Implementation

The main goal of this project is to use **Quaternions** and **Interpolations** to create pathways for the planes and camera. I've also included some intriguing features like Procedural Generated Terrain and Terrain Deformation that syncs with the beat of the background music.

### Quaternions & Interpolations

![](/images/others/interp_visual.gif)

The motion of the planes can be categorized into two key aspects: position and rotation.

#### Position

The position of each plane is initially set using an array of points (forming in blue), which are then utilized to create a spline (forming in purple). 

```c++
line_1.push_back(vec3(3.0f, 26.0f, 10.0f));
line_1.push_back(vec3(3.0f, 24.0f, 0.0f));
line_1.push_back(vec3(3.0f, 20.0f, -20.0f));
line_1.push_back(vec3(7.0f, 24.0f, -35.0f));
```

While the planes can move more smoothly along the splines, the resulting motion may still appear jagged without interpolation between the points. Here is a simplified version of the position interpolation using linear interpolation between consecutive points on a path:

Since our planes always are between 2 points in the array, we need to figure out the current 2 points that the planes are in at current frame time. 

> Calculate the distances between consecutive points and the total distance traveled is always better then using the number of points directly to avoid undesired change in speed due to the difference in distances between points:

```c++
for (int i = 0; i < points.size() - 1; i++) {
    distances[i] = glm::distance(points[i], points[i+1]);
    total_distance += distances[i];
}
```

> Then, we can calculate the constant speed of the plane:

```c++
float speed = total_distance/total_time;
```

> To find the 2 points with the corresponding interpolation factor, here is my approach:

```c++
// 1. Calculate the current distance from the start point (points[0])
float distance_from_start = curTime * speed;
// 2. Find the current index (cur_index) of the segment on the path:
int cur_index = 0;       
while (cur_index < points.size() - 2 && 
        distance_from_start > distances[cur_index])
{
    /* keep decrement the distance from the passed points to get to
       the distance from the current point to the next point */
    distance_from_start -= distances[cur_index];
    cur_index ++;
}
cur_point = cur_index;

// 3. Calculate the interpolation factor (interp_factor) based on the distance variable:
float interp_factor = distance_from_start / distances[cur_index];
```

> Lastly, interpolate the position using **mix()** (interpolation function in GLM): 

```c++
vec3 p1 = points[cur_index];
vec3 p2 = points[cur_index+1];
return mix(p1,p2,interp_factor);
```

#### Rotation

![](https://www.researchgate.net/publication/333185260/figure/fig7/AS:759745858256896@1558148936720/Roll-Pitch-and-Yaw-Positive-Rotation-Angles-in-the-Aircraft-Frame.jpg)

The plane's rotation is partially controlled (**yaw** & **pitch**) by the path it takes, as the forward vector aligns with the direction to the next point. However, the planes' **roll** is controlled programmatically in order to perform certain actions and replicate accurate rotation motions.

Similar to the positions, the rotations are also being stored in an array, but only the z axis (roll) rotation in degrees:

```c++
z_rot_1.push_back(0.0f);
z_rot_1.push_back(20.0f);
z_rot_1.push_back(40.0f);
z_rot_1.push_back(20.0f);
```

> For interpolation, we calculated the current index and next index based on the ratio of the current time to the total time:

```c++
// ratio calculation
float ratio = curTime/total_time;
// find the current index and next index
int i1 = (int) (ratio * (z_rot_1.size() - 1));
int i2 = i1 + 1;
// edge case handling
if (i2 >= z_rot_1.size()) i2 = static_cast<int>(z_rot_1.size()) - 1;
// find the rotations based on indices
float ez1 = z_rot_1[i1];
float ez2 = z_rot_1[i2];
```

> Next, we find the interpolation factor using a similar approach, but with fixed unit:

```c++
float unit_time = total_time/(z_rot_1.size()-1);
float cur_ratio = curTime - (unit_time * i1);
float rot_factor = cos_interp_float(cur_ratio / unit_time);
```

> Instead of using linear interpolation, I wrote my own cosine interpolation for the rotation to achieve better animation between 2 rotation degrees.

```c++
float cos_interp_float(float t)
{
    return 1.0f - (cos(t * 3.1415926)+ 1.0f) / 2.0f;
}
```

As a side note, there are many different ways to interpolate between animations, and some of the approaches are non-linear.

![](https://codeplea.com/public/content/splines_interp.png)

The curve starts slowly, accelerates in the middle, and then slows down again as it approaches the end point. This curve is created using the cosine function, which provides a smooth transition with a gradual change in rate.

> To calculate the yaw & pitch of the planes, we need to find the forward vector using cosine interpolation again:

```c++
// determine the three points needed for the rotation interpolation
vec3 p0 = points[cur_index-1];
vec3 p1 = points[cur_index];
vec3 p2 = points[cur_index+1];
// set the up vector to be align with the y axis for now
vec3 up = vec3(0,1,0);
// find the unit vectors for forward directions
vec3 direction0 = normalize(p1-p0);
vec3 direction = normalize(p2-p1);
// interpolate between 2 vectors to find the forward vector
vec3 forward = cos_interp(rot_factor, direction0, direction);
// calculate the right vector
vec3 ex = cross(forward, up);
// recalculate the up vector
vec3 new_up = cross(forward, ex);
```

The up vector is first initialized to (0, 1, 0) in the following code, reflecting the positive y-axis direction. However, as the item rotates along its journey, the forward direction changes, resulting in a change in the orientation of the object's local coordinate system. As a result, in order to keep the rotation matrix orthogonal and orthonormal, the up vector must be computed based on the new forward direction.

> Form the rotation matrix:

```c++
mat3 rotation_matrix(ex, new_up, forward);
```

> Since the rotation between states can be dramatic for a plane, using quaternions interpolation can avoid [Gimbal lock](https://www.youtube.com/watch?v=zc8b2Jo7mno) for unexpected behaviors:

```c++
// Create a 4x4 matrix for quaternion convertion
glm::mat4 matrix;
matrix[0] = glm::vec4(rotation_matrix[0], 0.0f);
matrix[1] = glm::vec4(rotation_matrix[1], 0.0f);
matrix[2] = glm::vec4(rotation_matrix[2], 0.0f);
matrix[3] = glm::vec4(0.0f, 0.0f, 0.0f, 1.0f);
// convert matrix to quaternion
quat q = quat_cast(matrix);
// interpolate the roll of the plane then apply to the forward vector
q = angleAxis(radians(mix(ey1,ey2,rot_factor)), forward) * q;
// convert back to 4x4 matrix for GLM function calls
rot = mat4_cast(q);
```

### Procedural Generation



## Results
The preceding code generated the earth model that demonstrates the importance of lighting in generating a realistic and visually pleasing 3D scene. The earth model's left screenshot displays the planet with **no** diffuse or specular light, resulting in a flat and unattractive surface. The code has calculated **both** the diffuse and specular light in the right screenshot, resulting in a more bright and dynamic image. Because the water is a highly reflective surface, the specular light only affects it. The planet's lands have a faint diffuse shade rather than a specular highlight. 
|
|:-:|:-:|
![BEFORE](/images/others/earth_no_lighting.png)  |  ![AFTER](/images/others/earth_lighting.png)

------------

Overall, this 3D earth model demonstrates how proper lighting can greatly enhance the visual quality of a 3D environment, making it look more realistic and captivating.