---
title: "Inverse Kinematics"
date: 2022-06-07T01:18:59-08:00
draft: false
author: ["Lucas Li"]
tags: ["Projects"]
featured_image: '/images/others/galaxy_cover.png'
description: "experiment with FABRIK using OpenGL"
toc: true
---

The purpose of this [Galaxy](https://github.com/GelzoneXUnsas/Galaxy-Instanced-Rendering) project is to create an OpenGL software that uses **instanced rendering** to draw a galaxy full of stars. The project will be developed to create a visually attractive picture of a galaxy while also serving as a learning tool for visualizing mass particles using instanced rendering.<!--more--> 

![](/images/others/galaxy_demo.gif)

To generate a 3D environment, the program will make use of the OpenGL graphics library. The stars in the galaxy will be displayed using instanced rendering, which is a technique for rendering numerous instances of the same object with varied transformation parameters quickly. The program will accomplish this by utilizing a particle system that generates a vast number of star instances in real-time. 

## Overview
The project is a software development project that uses modern technologies and frameworks to create a graphics-intensive application.

- C++11 as the main language
- Modern OpenGL/GLSL as the graphics API
- GLEW for cross-platform initialization of OpenGL
- GLFW for windowing
- Eigen for linear algebra

## Assets
Besides using a sphere.OBJ file for the skybox of the Galaxy, the following textures are needed to produce a realistic look of the Galaxy:

- Star
![](https://lh3.googleusercontent.com/inmSN6TuSMJfM5ga9-iNLLfP00_1IvIvvN8IRBiIFUTMUOlZrATy0SHozd8ZePcNvaYUDnZ25SjdBh7w0k0WLE3flR6Cm3FBnBODfAJw)
- Gas
![](https://lh3.googleusercontent.com/AYoALTHYbZDHlj6rjiALeYOjXgCQ4TP4zGzFI3lc0UI1Vl7Q8LSpFOnVe27UdOM971XBT5A6CPXMsn9YVZB4p0H9iEilO2kBFDMhmsk)
- Galaxy
![](https://lh3.googleusercontent.com/GFazJN56HF9vB2t0OSd1KRHxc-whFOcWIqbD9vsH--62-Rc9NEP5wzLmq2BdHx3RKzI4kzj3sd2v402ISkOctbP15hH5lxqhUD1GSi4)

## Implementation
Instead of creating individual star objects and rendering them separately, we can use instanced rendering to render multiple instances of the **same** star with different transformation parameters.

### CPU
The CPU is in charge of activities such as data management, and logic in this project. To do this, we first create a **VAO** (Vertex Array Object) and a **VBO** (Vertex Buffer Object) to hold the star mesh data. We also create another **VBO** to hold the transformation data for each instance of the star.

```c++
//generate the VAO
glGenVertexArrays(1, &VertexArrayID);
glBindVertexArray(VertexArrayID);
//generate vertex buffer to hand off to OGL
glGenBuffers(1, &VertexBufferID);
//set the current state to focus on our vertex buffer
glBindBuffer(GL_ARRAY_BUFFER, VertexBufferID);
...
//generate vertex buffer to hand off to OGL
glGenBuffers(1, &InstanceBuffer);
//set the current state to focus on our vertex buffer
glBindBuffer(GL_ARRAY_BUFFER, InstanceBuffer);
```

Instanced rendering needs an **object** (containing the billboard) and an **instance buffer** (containing all positions). Using the star.png as the object, we'll also need a position buffer for it. Here we created a position buffer contains 10,000 positions, having a spherical (pill-shape) distribution of positions, with exponentially more stars in the center than at the borders:

```c++
// create a new dynamic array of 10,000
glm::vec4 *positions = new glm::vec4[10000];

//A loop is run for 10,000 iterations to generate a random position for each instance.
for (int i = 0; i < 10000; i++){
    // Randomize radius
    float r = frand()* 200.0f;
    // Randomize angle x
    float ax = frand() * 2 * PI;
    // Randomize angle y
    float ay = frand() * PI;
    // rotations around the x-axis and y-axis, respectively
    mat3 Rx = rotate(mat4(1), ax, vec3(1.0f,0,0));
    mat3 Ry = rotate(mat4(1), ay, vec3(0,1.0f,0));
    vec3 v = vec3 (0,0,r);
    v = Rx * v;
    v = Ry * v;
    // position vector is divided by 2.5 to flatten the distribution of the instances
    // make the overall shape of the distribution more like a disk than a sphere.
    v.y = v.y / 2.5;
    positions[i] = vec4(v,0);
}
```

Next, we can copy to a new OpenGL buffer object, and make it **instanced**:
```c++
//actually memcopy the data - only do this once
glBufferData(GL_ARRAY_BUFFER, 10000 * sizeof(glm::vec4), positions, GL_STATIC_DRAW);
int position_loc = glGetAttribLocation(prog->pid, "InstancePos");
// Set up the vertex attribute
glVertexAttribPointer(position_loc , 4, GL_FLOAT, GL_FALSE,  sizeof(vec4),  (void *)(sizeof(vec4))); 
glEnableVertexAttribArray(position_loc);
// Make it instanced
glVertexAttribDivisor(position_loc, 1);
```

The same thing will be duplicated for the gas object as well. After creating a **VAO** and **VBOs** to hold the gas mesh data, we can use glDrawArraysInstanced() to render multiple instances of the those mesh (eg. star), with the transformation data provided by the instanceVBO:
```c++
glBindVertexArray(VertexArrayID);
glDrawElementsInstanced(GL_TRIANGLES, 6, GL_UNSIGNED_SHORT, (void*)0,10000);
```
This will render the star mesh 10,000 times, with the transformation data provided by the **instanceVBO** for 10,000 instances.

### GPU
The GPU, on the other hand, is in charge of handling graphics-related computations such as rendering, lighting, and texture mapping. In this program, the shaders are fairly simple:

Using the star shaders as an example. To make the inside stars rotates faster than the outside's, we make the rotation matrix manually in the **vertex shader**. Since the rotation of star should be dependent on the radius (length(position)) of the star, we'll need to get the distance from the star to the center first:

```c++
// distance to the center as rate
float rate = distance(InstancePos, vec4(0,0,0,0)) * 0.01;
```

Then, the rotation matrix are be calculated using the following equations with the rate being a multiplier:

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/a6821937d5031de282a190f75312353c970aa2df)

Refering to **Ry(θ)** formula, we can come up with the rotation matrix around y-axis:

```c++
mat4  rotation = mat4(
    vec4( cos(rot_angle * rate),0.0, sin(rot_angle * rate),0.0),
    vec4( 0.0,1.0,0.0,0.0),
    vec4( -sin(rot_angle * rate),0.0,cos(rot_angle * rate),0.0),
    vec4( 0.0,0.0,0.0,1.0));
```

Lastly, we calculated the position from the instanced position buffer with the rotation:

```c++
// assign instanced position to each star
vec4 pos =  M * vec4(vertPos,1.0) + InstancePos;
// apply rotation
pos = rotation * pos;
// send out position information for rendering
gl_Position = P * V * pos;
```

Diffuse light is also added to the star in the **fragment shader**, feel free to refer to the [Earth Lighting](https://gelzonexunsas.github.io/posts/projects/earth_lighting/) project for further explanation and implementation.

## Results

![](/images/others/galaxy_cover.png)

The galaxy project used [OpenGL](https://www.opengl.org/) and instanced rendering to successfully render a large number of particles. By allowing the GPU to reuse the same vertex data for many instances of an object rather than providing a separate copy of the data for each object, instanced rendering proved to be a valuable technique for drawing large numbers of particles effectively. Instanced rendering substantially improves galaxy rendering efficiency, allowing the program to render **10,000** stars with ease. Without instanced rendering, the program would have to send the GPU 10,000 separate sets of vertex data, which would be wasteful and result in a considerable performance reduction.
