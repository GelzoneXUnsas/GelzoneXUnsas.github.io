---
title: "Inverse Kinematics"
date: 2022-06-07T01:18:59-08:00
draft: false
author: ["Lucas Li"]
tags: ["Projects"]
featured_image: "/images/others/FABRIK.gif"
description: "experiment with FABRIK using OpenGL"
toc: true
---

The [FABRIK](https://github.com/GelzoneXUnsas/FABRIK) (Forward And Backward Reaching Inverse Kinematics) project aimed at rendering of articulated arm structures using OpenGL.<!--more-->

![](/images/others/FABRIK.gif)

FABRIK is renowned for its simplicity and effectiveness in solving inverse kinematics problems for articulated structures. This algorithm enables the generation of dynamic arm movements.

## Overview

The project is a software development project that uses modern technologies and frameworks to create a graphics-intensive application.

- C++11 as the main language
- Modern OpenGL/GLSL as the graphics API
- GLEW for cross-platform initialization of OpenGL
- GLFW for windowing
- Eigen for linear algebra

## FABRIK
![](https://www.researchgate.net/profile/Minh-Man-Nguyen-2/publication/316945552/figure/fig11/AS:614364419739651@1523487300115/FABRIK-algorithm-with-forward-and-backward-procedures.png)

Stage (a): Initial setup with the robotic arm in a particular configuration. **P1**, **P2**, **P3**, and **P4** represent the joint positions, and d1, d2, d3 represent the link lengths.

Stage (b): Backward Reaching - The algorithm start updating the joints from the end-effector (**P4**) to the base (**P1**). **P4'** is adjusted to reach the target, and the other joints are updated accordingly.

Stage (c): Backward Reaching - The adjustment is propagated from **P4'** to **P1'**, refining the joint positions.

Stage (d): Forward Reaching - The algorithm resets the base joint (**P1''**) to its original position and updates the joints from the base to the end-effector. **P1''** is adjusted to reach the target, and the other joints are updated accordingly.

Stage (f): Forward Reaching - The adjustment is propagated from **P1''** to **P4''**, refining the joint positions.

## Implementation

The FABRIK algorithm implementation is primarily done on CPU, responsible for calculating the positions of the arm segments in each frame. Here's a breakdown of the implementation:

#### Interpolation

Firstly, these functions implement cosine interpolation, a smoothing function used in the FABRIK algorithm to interpolate between two values.

```c++
template <class T>
T cos_interp(float t, T a, T b){
    float ct = 1.0f - (cos(t * 3.1415926) + 1.0f) / 2.0f;
    return a * (1 - ct) + b * ct;
}

float cos_interp_float(float t){
    return 1.0f - (cos(t * 3.1415926) + 1.0f) / 2.0f;
}
```

#### Backward Reaching Inverse Kinematics Function (BRIK)

This function represents the backward reaching step of the FABRIK algorithm.

```c++
void BRIK(vector<vec3> &line)
{
    // Calculate the sum of distances
    float sum = accumulate(distances.begin(), distances.end(), 0.0f);

    // Check if the sum is less than the distance to the target point
    if (sum < glm::distance(line[0], objPoint)){
        printf("can't reach\n");
        return;
    }

    // Set the end effector position to the target point
    line[line.size() - 1] = objPoint;

    // Perform the Backward Reaching step
    for (int i = (int)line.size() - 2; i >= 0; i--)
    {
        glm::vec3 currentDir = glm::normalize(line[i] - line[i + 1]);
        float targetDist = distances[i];
        line[i] = line[i + 1] + currentDir * targetDist;
    }

    // Check if the end effector is within the distance threshold
    if (glm::distance(line[0], startPoint) <= distanceThreshold){
        done = true;
    }
}
```

#### Forward Reaching Inverse Kinematics Function (FRIK)

Next, we implement with a similar approach for the forward reaching step:

```c++
void FRIK(vector<vec3> &line)
{
    // Set the base position to the starting point
    line[0] = startPoint;

    // Perform the Forward Reaching step
    for (int i = 0; i < (int)line.size() - 2; i++)
    {
        glm::vec3 currentDir = glm::normalize(line[i + 1] - line[i]);
        float targetDist = distances[i];
        line[i + 1] = line[i] + currentDir * targetDist;
    }

    // Check if the end effector is within the distance threshold
    if (glm::distance(line[line.size() - 1], objPoint) <= distanceThreshold){
        done = true;
    }
}
```

#### Render
Lastly, renders the interpolated points and the lines representing the arm structure. Within the rendering function, BRIK and FRIK functions are called to update the arm segment positions based on the target point. Cosine interpolation is applied to smoothly transition between the current and target arm configurations. The updated positions are then used to render the arm segments in the scene.

```c++
float wt = 1.0f;

ct += frametime;

//-----------------------------------------------
//draw the points
prog->bind();

// Update arm segment positions using BRIK and FRIK functions
BRIK(actLine);
FRIK(actLine);

// Cosine interpolation to smoothly transition between current and target arm configurations
for (int i = 0; i < line.size(); i++)
{
    // Interpolate between current and target points
    line[i] = line[i] * (1 - ct/wt) + actLine[i] * ct/wt;
}

// If interpolation time exceeds the waiting time (wt), reset to target configuration
if (ct > wt)
{
    line = actLine;
    ct = 0;
}

// Render arm segments in the scene based on the updated positions
for (int i = 0; i < line.size(); i++)
{
    glm::mat4 Trans = glm::translate(glm::mat4(1.0f), line[i]);
    glm::mat4 Scale = glm::scale(glm::mat4(1.0f), glm::vec3(0.1f, 0.1f, 0.1f));
    
    // Increase the scale for the first segment (root)
    if (i == 0)
    {
        Scale = glm::scale(glm::mat4(1.0f), glm::vec3(0.2f, 0.2f, 0.2f));
    }
    
    // Model matrix (M) combining translation and scaling
    M = Trans * Scale;

    // Set uniform matrices and camera position for rendering
    glUniformMatrix4fv(prog->getUniform("P"), 1, GL_FALSE, &P[0][0]);
    glUniformMatrix4fv(prog->getUniform("V"), 1, GL_FALSE, &V[0][0]);
    glUniformMatrix4fv(prog->getUniform("M"), 1, GL_FALSE, &M[0][0]);
    glUniform3fv(prog->getUniform("campos"), 1, &mycam.pos[0]);

    // Draw the arm segment shape
    shape->draw(prog);
}

prog->unbind();

// Draw the lines representing the arm structure using a separate linerender object
// Re-initialize line data in the linerender
linerender.re_init_line(line);

// Set line color
glm::vec3 linecolor = glm::vec3(1, 1, 1);

// Draw the lines in the scene using the linerender object
linerender.draw(P, V, linecolor);

```

## Results

![](/images/others/FABRIK.gif)

