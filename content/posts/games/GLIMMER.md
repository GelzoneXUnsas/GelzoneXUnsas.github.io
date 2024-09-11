---
title: "GLIMMER"
date: 2024-06-05T01:18:59-08:00
draft: false
author: ["Lucas Li", "Justin Scabarozi", "Angela Chen", "Jeremiah Lee", "Brett Hickman"]
tags: ["Games", "featured"]
featured_image: "/images/games/glimmer_cover.png"
description: "Alternate Reality Game for iLRN 2024"
toc: true
---

[GLIMMER](https://github.com/iLRN-Glimmer/Glimmer) is an Alternate Reality Game(ARG) walking simulator created for the [iLRN 2024](https://www.immersivelrn.org/ilrn2024/home/) conference in Scotland. This game allows players to explore immersive learning experiences showcased by the network community. <!--more--> Players will navigate through city ruins, searching for interactive objects that lead them to different immersive showcases such as Computer Workshop Sim, Ocean-Based Ecosystems, AI Art Gallery, RPG Rhythm Game, and more!

GLIMMER offers a meditative journey where players walk through beautifully crafted digital landscapes. The game focuses on atmosphere and ambiance, offering subtle interactivity and exploration. Designed for web browsers, the game is accessible from any desktop with no need for installation.

---

### About

![Game Preview](/images/games/glimmer_screenshot_1.png)

* [Play Online](https://gelzonexunsas.itch.io/glimmer)

Please note the following before playing:

- This is a PC game that runs on a **browser** and does not support mobile devices.
- The first loading of the game can take up to **several minutes**, but subsequent loadings will be much faster.
- The game **stores player progress** using cache. Do not clear your cache if you wish to save your progress.
- It is highly suggested to turn on the **volume** while playing for the best experience.
- If you get lost, utilize the map (press **M**), help (press **Tab**), and in-game sound effects as indications.

### Assets
- [Stylized City Ruins Pack](https://assetstore.unity.com/packages/3d/environments/urban/stylized-city-ruins-pack-post-apocalypse-205517)
- [Low Poly Ultimate Pack](https://assetstore.unity.com/packages/3d/props/low-poly-ultimate-pack-54733)
- [Painted UI](https://assetstore.unity.com/packages/2d/gui/painted-ui-61967)

---

### Controls
- **WASD**: Move player
- **Mouse**: Look around/interact
- **Spacebar**: Interact with objects
- **M Key**: Toggle Map
- **Tab**: Open Help

### Technical Details

The development of GLIMMER involved several key components, from boundary-based dissolving effects to a dynamic lighting system, custom collectibles, and UI design. Below are some of the technical highlights:

#### Boundary-Based Dissolve

One of the primary mechanics in GLIMMER is the **boundary-based dissolve effect**, where objects dissolve into view as players approach them. The logic for this feature is handled by a coroutine that calculates the distance between the player and objects, gradually adjusting the dissolve strength of the material shader.

```csharp
public IEnumerator ShowBoundary()
{
    float distanceToPlayer = Vector3.Distance(playerCollider.ClosestPoint(transform.position), objectCollider.ClosestPoint(player.transform.position));
    float dissolveStrength = Mathf.Clamp01(distanceToPlayer / dissolveDistance);
    dissolveStrength = Mathf.Clamp(dissolveStrength, 0.4f, 0.75f); 
    mat.SetFloat("_DissolveStrength", dissolveStrength);
    yield return null;
}
```

This effect contributes to the atmospheric immersion, enhancing the sense of exploration by hiding or revealing elements based on player proximity.

#### Collectibles System

![](/images/games/glimmer_screenshot_3.png)

GLIMMER includes an extensive **collectibles system**, where players can interact with and unlock different objects in the environment, such as notes, images, sounds, and videos. Each collectible follows a base class structure that allows for easy addition of new item types.

```csharp
public abstract class Collectible : MonoBehaviour
{
    [SerializeField] protected string Title;
    [SerializeField] protected string Body;
    public abstract void OpenWindow(GameObject canvas);
}
```

Specific collectibles like **Node**, **Picture**, **Video**, and **Sound** all extend this class, providing unique interactions depending on the object type. For example, the `Node` class shows detailed descriptions and questions when activated, while the `Picture` class displays imagery.

#### Dynamic Lighting System

GLIMMER's world is brought to life with a **dynamic lighting system**, where light intensity is controlled based on the player's distance to light sources. The system ensures that lights gradually dim or brighten as the player moves closer or further away, enhancing the game's immersive feel.

```csharp
public class DistanceBasedLightIntensity : MonoBehaviour
{
    public float minDistance = 1f;
    public float maxDistance = 10f;
    void Update()
    {
        float distance = Vector3.Distance(transform.position, targetObject.position);
        distance = Mathf.Clamp(distance, minDistance, maxDistance);
        float intensity = Mathf.Lerp(minIntensity, maxIntensity, Mathf.InverseLerp(minDistance, maxDistance, distance));
        targetLight.intensity = intensity;
    }
}
```

#### UI and Map System
|
|:-:|:-:|
![](/images/games/glimmer_screenshot_4.png)  |  ![](/images/games/glimmer_screenshot_2.png)

To aid player navigation, GLIMMER features an **interactive map** and a **help panel**. The map shows collectible locations and is dynamically updated based on the player's progress. The UI also allows for smooth transitions between panels, using the `LeanTween` library for animation effects.

```csharp
public class OpenMap : MonoBehaviour
{
    public GameObject panel; 
    void Update()
    {
        if (Input.GetKeyDown(KeyCode.M))
        {
            panel.SetActive(!panel.activeSelf);
        }
    }
}
```

#### WebGL Integration

The game is designed to be fully playable in **WebGL**, ensuring that no downloads are necessary, making it easily accessible for desktop browsers. Special attention was given to optimizing the game’s assets and code for WebGL compatibility, including video players and custom interactions.

---

### Last Words

The creation of GLIMMER has been a rewarding journey, and none of this would have been possible without the combined efforts of the team. The game is a testament to our commitment to creating immersive experiences and our love for experimental gameplay. We hope you enjoy exploring the world of GLIMMER!

For more information or to play, visit the game on [itch.io](https://gelzonexunsas.itch.io/glimmer).

---

