---
title: "Experience"
date: 2022-07-10T01:18:59-08:00
draft: false
author: ["Lucas Li", "Alex Hartford", "Brett Hickman"]
tags: ["Games","Projects"]
featured_image: 'https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/f2.png?raw=true'
description: "audio visualizers created with OpenGL"
toc: true
---

[Experience](https://github.com/CPE-476/Experience) is a small expressive game about the process of trying to improve at something.
The game involves walking through various environments which evoke feelings like those experienced when learning something new. <!--more-->

---

{{< youtube 9Me2BgbvU00 >}}

---
### Gameplay
The game takes place from a first-person perspective. The gameplay is as simple as walking and observing. The player can interact with their environment, reading notes strewn about the environment and talking with animals.
|
|:-:|:-:|
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/note1.jpeg?raw=true)  |  ![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/note2.jpeg?raw=true)

### Environment
The environments of the game are threefold. To travel between the environments, the player can simply walk into the fog at the edge of each environment whenever they choose.

#### Forest - A new beginning!
At first, the player will find themselves in a quiet wood. This area is intended to evoke feelings of a fresh start. Secrets are found around every corner, and the world is new and interesting.
|Third Person View|First Person View|
|:-:|:-:|
![Third Person View](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/f1.png?raw=true)  |  ![First Person View](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/f2.png?raw=true)


#### Desert - Arduous Journey
Next, the player will find themselves in a desert wasteland, with long stretches of empty land and steep sand dunes. Bloom and Blur effects make the journey feel arduous. Deep in the desert you may find secrets, but they are few and far between, and this area evokes the feeling of being overwhelmed by your task.
|Third Person View|First Person View|
|:-:|:-:|
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/d2.png?raw=true) | ![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/d1.png?raw=true)

#### Street - Completion and Loneliness
Finally, the player will find themselves on a dark street in the middle of the night. No stars in the sky, just loneliness. streetlamps light the path. You have completed your journey, and overcome much, and have seen the fleeting beauty of the journey you've traveled, but now you find yourself alone. Only you know what you have learned.
|Third Person View|First Person View|
|:-:|:-:|
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/s2.png?raw=true) | ![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/s1.png?raw=true)

Nothing else to do, then, than to start a new journey!

#### Credit - With Secret Ending
Collect all the notes and messages to find out!
|
|:-:|:-:|
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/credit.png?raw=true)  |  ![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/credit_ori.png?raw=true)
### Technical Details
#### Instanced Rendering
```c++
for(int i = 0; i < entry.model->meshes.size(); i++)
{
    glBindVertexArray(entry.model->meshes[i].VAO);
    glDrawElementsInstanced(GL_TRIANGLES,
    static_cast<unsigned int>(entry.model->meshes[i].indices.size()),
        GL_UNSIGNED_INT, 0,
        modelMatrices.size());
        glBindVertexArray(0);
}
```
Models and particles are rendered using Instancing, increasing the maximum render cap by a sizable margin.

#### Shadow Mapping
```c++
void render()
{
    shader.use();
    glUniformMatrix4fv(lightSpaceMatrixLocation, 1, GL_FALSE, glm::value_ptr(lightSpaceMatrix));
    glViewport(0, 0, SHADOW_WIDTH, SHADOW_HEIGHT);
    glBindFramebuffer(GL_FRAMEBUFFER, depthMapFBO);
    glClear(GL_DEPTH_BUFFER_BIT);
    RenderScene(shader);
    glBindFramebuffer(GL_FRAMEBUFFER, 0);
}
```

Our game makes a first render pass to a depth buffer, which is then used later in the pipeline to calculate dynamic shadows.

![Shadow](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/shadow.png?raw=true)

#### High Dynamic Range
High Dynamic Range, in conjunction with Bloom/Blur, creates a very vibrant and lively scene. We calculate excess color above the standard white maximum, and we perform gamma correction to ensure that the scene fits within a certain exposure.

#### Bloom and Blur
The game also uses a Bloom effect to simulate bright lights. We find bright segments (above a certain threshold), and we render these to their own Framebuffer. We then perform a Gaussian Blur on that framebuffer, which we merge with the original image to get our bloomed result.
|
:-------------------------:|:-------------------------:
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/before_bloomblur.png?raw=true)  |  ![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/after_bloomblur.png?raw=true)

#### View Frustum Culling
```c++
bool ViewFrustCull(vec3 center, float radius)
{
    float dist;
    for(int i=0; i < 6; i++){
        dist = DistToPlane(planes[i].x, planes[i].y, planes[i].z, planes[i].w, center);
        if(dist < 0 && fabs(dist) > radius){
            return true;
        }
     }
     return false;
}
```
|
:-------------------------:|:-------------------------:
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/before_cull.png?raw=true)  |  ![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/after_cull.png?raw=true)

* Notice that the drawn objects decrease from 14616 to 3123

The game needs to be as performant as possible, especially considering the amount of models that we render. For this to be feasible, we implemented View Frustum Culling. It calculates the area that can be viewed, and performs spatial queries to determine if a given object should be drawn.

#### Distance Fog
Our game includes distance fog to simulate depth more accurately. It can be tweaked in the editor.

#### Level Editor
We achieved a rich, detailed set of environments by constructing a level editor. We save files to an ASCII format and load them into our game. It allows you to tweak every aspect of the levels, from particles to distance fog. 
&nbsp;

![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/sound_editor.png?raw=true)
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/light_editor.png?raw=true)
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/select.png?raw=true)
_visualize seletion_
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/collision.png?raw=true)
_visualize collision_
![](https://github.com/CPE-476/Experience/blob/main/Final/HTML/src/view.png?raw=true)
_visualize view frustum_
## Getting Started
#### Built With
The CMake is not tested on Windows or Linux.
It uses find-package to search for libraries, which I believe searches the source tree as well as some places on your computer, but definitely some expertise will be required if encountering any bugs.

I would recommend installing each library using a package manager like Brew if you can, and otherwise putting them in a usual spot on your system where CMake is likely to find them.

#### References
LearnOpenGL: The best resource - CGTrader - Sketchfab

#### Dependencies
GLFW - GLM - Assimp - Freetype

#### Included
GLAD - ImGui - tinyobjloader - miniaudio - stbimage

#### Contact
Lucas Li - yli76@calpoly.edu

Alex Hartford - alexanderhartford@gmail.com

Brett Hickman - bahickma@calpoly.edu
