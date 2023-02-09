---
title: "Blitz Pong"
date: 2023-01-20T01:18:59-08:00
draft: false
author: ["Lucas Li"]
tags: ["Games","Projects"]
featured_image: "/images/games/bgcover.png"
description: "2D Unity Pong game"
toc: true
---

A 2D Unity project using the Unity physics engine and post-processing rendering pipeline. Basic pong game mechanic, try to get goals and prevent your opponent from getting goals.<!--more-->


---

### Blitz Pong

![Game Preview](/images/games/bgcover.png)

* [Play Online](https://gelzonexunsas.itch.io/blitz-pong)

---
#### Controls
- Selection: mouse

- PVP

Player 1: 
w-up, s-down

Player 2: 
up arrow-up
down arrow-down

- PVE

w-up, s-down

### Technical Details
#### Ball Movement
In a Pong video game, the ball moves continuously in a straight line across the screen, bouncing off the paddles and walls. To achieve this in Unity, I create a ball game object and use a rigidbody component with a collider to detect when it collides with the paddles and walls. Then, apply a force to the ball's rigidbody in the direction it should move, and use the collider and physics engine to calculate the ball's new direction when it bounces off objects. Additionally, I wrote a script to control the ball's speed by time, and initial velocity.

```c#
    public IEnumerator Launch()
    {
        Restart();
        hitCounter = 0;
        yield return new WaitForSeconds(1);

        Move(new Vector2(Random.Range(-1.0f, 1.0f), Random.Range(-1.0f, 1.0f)));
    }

    public void Move(Vector2 direction)
    {
        float bSpeed;
        direction = direction.normalized;
        bSpeed = startSpeed + hitCounter * extraSpeed;

        rb.velocity = direction * bSpeed;
    }
```

#### Trail Rendering
I also used the Trail Renderer component to add trail path to the ball. By using this component, I adjusted the trail's length, width, color, etc to make the trail follow the ball's movement. In the script controlling the ball's movement, add a line to activate the trail renderer when the game starts and another line to deactivate it when the game ends.

![Game Preview](/images/games/bggameplay.png)

#### Post-Processing
One thing new about this project to me was using the unity build-in post-processing layer/volume. Using the Post Processing Volume and Post Processing Layer in Unity have been an exciting experience. The Post Processing Volume allows me to apply a set of post-processing effects to a specific area of each scene, while the Post Processing Layer component allows me to prioritize the order in which these effects are applied. 
You may have noticed the immediate impact of these effects on the overall look and feel of your scene, adding depth and visual interest to your environment. The variety of effects available, such as color grading, bloom, and vignette, allow you to create a unique and custom look for your game or application.

|Before|After|
|:-:|:-:|
![](/images/games/bgbefore.png) | ![](/images/games/bgafter.png)
