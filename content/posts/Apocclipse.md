---
title: "Apocclipse"
date: 2023-02-21T01:18:59-08:00
draft: false
author: ["Lucas Li", "Brett Hickman"]
tags: ["Games"]
featured_image: '/images/games/Apocclipse_cover.png'
description: "2D side-scrolling runner game"
toc: true
---

Apocclipse is a fast-paced post-apocalyptic 2D side-scrolling runner game. Players take on the role of Brontë, an experienced adventurer who has arrived on a war-torn planet to save it from destruction. <!--more--> We designed this game as a prototype for an in-coming game we are developing to test out some of the game assets and skeleton character animation.

---

![Game Preview](/images/games/Apocclipse_2.png)

* [Play Online](https://gelzonexunsas.itch.io/apocclipse)

---
### Gameplay
To collect items and gain points, Brontë must navigate through dangerous terrain and avoid a variety of obstacles as she progresses through the game. The game's core mechanic revolves around collecting items, while also avoid hazards.

### Game Design
#### Background
The game takes place in a distant galaxy, where a group of vigilantes is on a mission to save their dying home planet. They come across the war-torn planet that Brontë now finds herself on along the way. The plot of the game revolves around the group's efforts to save the planet from destruction, as well as Brontë’s role as their leader and savior.

#### Character
The character of Brontë is one of the distinguishing features. Brontë is a girl who was born deaf and uses a hearing aid to interact with the world around her. She, on the other hand, frequently pretends not to hear people and instead focuses on her love of drums.


### Technical Details
#### Character Movement
Brontë's actions involve animation, which are controlled by **animation states**.

![UML](/images/games/Apocclipse_uml.png)

In Unity, animation states are represented by **Animator Controllers**, which are used to manage the transition between different animations. Each animation state in an Animator Controller is defined by a specific set of animations and conditions that determine when and how the animations are triggered.

In our game, we created an **Animator Controller** with separate animation states for a character's running, jumping, and attacking etc animations. Each of these states could be triggered by different events or conditions in your game, such as pressing a button or colliding with an object. Furthermore, the arrows that connect each state in a Unity Animator Controller provide a visual representation of the complex network of transitions that govern the behavior of the Animator Controller.

#### Collision Detection
In Unity, collision detection is a crucial aspect of game design, as it enables objects to interact with each other and respond to various events and triggers. One common approach to collision detection involves using colliders, which are specialized components that define the shape and boundaries of an object. However, in addition to using colliders for collectables and obstacles, it's also possible to use multiple colliders for a single object in order to achieve more nuanced collision detection.

![UML](/images/games/Apocclipse_col.png)

The first collider is a box collider that is used to check for collisions with the ground. This collider would define the shape and size of Brontë's feet, and would be positioned at the bottom of Brontë's sprite. By using this collider, we can ensure that Brontë stays grounded and doesn't fall through the floor or other objects in the game world.

The second collider might be a capsule collider, which is used to trigger different animations and events when Brontë collides with certain objects. This collider positioned around Brontë's body, and would be configured to trigger the OnTriggerEnter2D() function when Brontë collides with specific objects or areas of the game world. For example, if Brontë collides with an obstacle, the OnTriggerEnter2D() function might trigger a different animation in which Brontë takes damage.

#### Dialogue System
One other feature about the game is the dialogue system. I implemented this feature not only to support character development, but also for future game testing purposes. The dialogue is triggered in the beginning of the game, and also when the player hits the 1 minute playtime mark. 

![UML](/images/games/Apocclipse_1.png)

To polish the design, I added auto-type lines and voice overlay to the system.

```c#
    void StartDialogue()
    {
        index = 0;
        StartCoroutine(TypeLine(true));
    }

    IEnumerator TypeLine(bool playAudio)
    {
        if (playAudio) audioSource.Play();
        foreach (char c in lines[index].ToCharArray())
        {
            textComponment.text += c;
            yield return new WaitForSeconds(textSpeed);
        }
        if (playAudio) audioSource.Pause();
    }
```

#### Sound
Sound is implemented both in terms of background music and SFXs.

- BGM: The in game background music is my [guitar arrangement](https://gelzonexunsas.github.io/posts/weight-of-the-world/) from the Nier’s OST.
- SFX: The SFXs are being activated when the character collides with coins and obstacles. The sound effects are not copyrighted.

#### Post-Processing
The Unity Post Processing Layer and Volume allow for advanced and customizable effects such as color grading and ambient occlusion to be applied to a scene. By utilizing the Unity Post Processing Layer and Volume, I incorporated effects such as color grading, ambient occlusion, and bloom effect to enhance the visual appeal of the game scene.