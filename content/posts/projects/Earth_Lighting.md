---
title: "Earth Lighting"
date: 2022-03-28T01:18:59-08:00
draft: false
author: ["Lucas Li"]
tags: ["Projects"]
featured_image: '/images/others/earth_lighting.png'
description: "experiment with specular & diffuse lighting using OpenGL"
toc: true
---

The program is intended to generate a realistic picture of the earth and **simulate light reflection** angles using a directional light source. The program employs advanced graphics techniques to precisely show how light interacts with the earth's surface, resulting in a realistic and dynamic visual experience.<!--more--> 

![](/images/others/earth_lighting.png)

The project's goal is to exhibit OpenGL's capabilities and the impact of various lighting approaches on the overall appearance of a 3D model. The program allows users to investigate the complexity of light and its impact on visual perception by experimenting with specular and diffuse lighting.

## Overview
The project is a software development project that uses modern technologies and frameworks to create a graphics-intensive application.

- C++11 as the main language
- Modern OpenGL/GLSL as the graphics API
- GLEW for cross-platform initialization of OpenGL
- GLFW for windowing
- Eigen for linear algebra

## Assets
Besides using a sphere.OBJ file for the base of the Earth, the following textures from this free [educational website](https://www.solarsystemscope.com/textures/) are needed to produce a realistic look of the Earth:

- Earth Day Map
![](https://www.solarsystemscope.com/textures/download/2k_earth_daymap.jpg)
- Earth Clouds
![](https://www.solarsystemscope.com/textures/download/2k_earth_clouds.jpg)
- Earth Specular Map
![](/images/others/2k_earth_specular_map.png)

## Implementation
The project's implementation is divided into two major components: the CPU and the GPU. The CPU is in charge of the program's non-graphics-related computations, while the GPU is in charge of the graphics-related computations.

### CPU
The CPU is in charge of activities such as data management, and logic in this project. For example, here is a snap of the program that load the object file and related textures:

```c#
// Loading sphere.
shape = make_shared<Shape>();
shape->loadMesh(resourceDirectory + "/sphere.obj");
shape->resize();
shape->init();
```

```c#
//Loading the Earth texture into spot 0.
string str = resourceDirectory + "/2k_earth_daymap.jpg";
strcpy(filepath, str.c_str());
unsigned char* data = stbi_load(filepath, &width, &height, &channels, 4);
glGenTextures(1, &Texture);
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, Texture);

//Loading the specular light texture into spot 1.
str = resourceDirectory + "/2k_earth_specular_map.jpg";
strcpy(filepath, str.c_str());
data = stbi_load(filepath, &width, &height, &channels, 4);
glGenTextures(1, &Texture2);
glActiveTexture(GL_TEXTURE1);
glBindTexture(GL_TEXTURE_2D, Texture2);

//Loading the cloud light texture into spot 2.
str = resourceDirectory + "/2k_earth_clouds.jpg";
strcpy(filepath, str.c_str());
data = stbi_load(filepath, &width, &height, &channels, 4);
glGenTextures(1, &Texture3);
glActiveTexture(GL_TEXTURE2);
glBindTexture(GL_TEXTURE_2D, Texture3);
```

Lastly, we want to send all these information to the GPU side for calculation:

```c#
//set the 3 textures to the correct samplers in the fragment shader:
GLuint Tex1Location = glGetUniformLocation(prog->pid, "tex");//tex, tex2... sampler in the fragment shader
GLuint Tex2Location = glGetUniformLocation(prog->pid, "tex2");
GLuint Tex3Location = glGetUniformLocation(prog->pid, "tex3");
// Then bind the uniform samplers to texture units:
glUseProgram(prog->pid);
glUniform1i(Tex1Location, 0);
glUniform1i(Tex2Location, 1);
glUniform1i(Tex3Location, 2);
```

### GPU
The GPU, on the other hand, is in charge of handling graphics-related computations such as rendering, lighting, and texture mapping. For example, the GPU is in charge of calculating light reflection angles on a 3D model as well as adding shading and texturing effects.

Everything mentioned above is being done in the fragment shader, starting by normalizing the normal vectors calculated by the vertex shader:
```c#
// normalize normal
vec3 n = normalize(vertex_normal);
```
Then, we will need a directional light to show the effects of the reflections:
```c#
// init directional light position
vec3 lp=vec3(100,100,100);
// calculate the light direction 
// from each point on the Earth to the directional light position
// Then normalize the light direction
vec3 ld = normalize(lp - vertex_pos);
```
Next, we want to find the diffuse reflection of the point on Earth. The diagram labels the pieces needed to calculate diffuse reflection. We need to calculate the angle between the vertex’s normal vector and a vector pointing at the light source from the vertex. This angle is labeled “theta” in the diagram.

![](/images/others/diffuse_angle.png)

Using the diagram, we can get the angle using the dot product of the two vectors:
```c#
// angle theta 
float diffuse = dot(ld,n);
// clamp the angle for smoother result
diffuse = clamp(diffuse,0.15,1);
```
In the next step, we read the textures per point for all the three texture files:
```c#
// earth texture
vec3 earth = texture(tex, vertex_tex).rgb;
// specular light texture
vec3 spec_light = texture(tex2, vertex_tex).rgb;
// cloud texture
vec3 cloud = texture(tex3, vertex_tex).rgb;
```
To calculate the reflection vector, we need to set up several intermediate vectors.
![](/images/others/reflection_vectors2.png)
Since the specular light is based on the camera angle and position, we need to calculate the normalized direction vector from the vertex to the camera position:
```c#
// camera direction
vec3 cd = normalize(vertex_pos - campos);
```
Then we calculate the normalized half-vector, which represents the halfway point between the viewer's line of sight and the light source. This vector is used to calculate the intensity of the specular light:
```c#
// half vector
vec3 h = normalize(cd+ld);
```
The dot product between the surface normal and the half-vector represents the angle at which light reflects off the surface:
```c#
// specular angle
float spec = dot(n, h);
```
We get the specular light by ensuring that the specular light value is within a valid range (between 0 and 1), and using a power function to control the intensity of the specular light. In this case, the value is raised to the 20th power, resulting in a highly reflective surface (ocean).
```c#
spec = clamp(spec,0,1);
spec = pow(spec,20);
```
Lastly, we can update and merge the texture color with diffuse, and specular light:
```c#
color = diffuse * earth + diffuse * cloud;
color += vec3(1,1,1) * spec * spec_light * 0.6;
```

## Results
The preceding code generated the earth model that demonstrates the importance of lighting in generating a realistic and visually pleasing 3D scene. The earth model's left screenshot displays the planet with **no** diffuse or specular light, resulting in a flat and unattractive surface. The code has calculated **both** the diffuse and specular light in the right screenshot, resulting in a more bright and dynamic image. Because the water is a highly reflective surface, the specular light only affects it. The planet's lands have a faint diffuse shade rather than a specular highlight. 
|
|:-:|:-:|
![BEFORE](/images/others/earth_no_lighting.png)  |  ![AFTER](/images/others/earth_lighting.png)

------------

Overall, this 3D earth model demonstrates how proper lighting can greatly enhance the visual quality of a 3D environment, making it look more realistic and captivating.