---
title: "Rootscaped"
date: 2023-02-05T01:18:59-08:00
draft: false
author: ["Lucas Li", "Alex Hartford", "Brett Hickman", "Ryoma Sugawara", "Mitchell Kanazawa"]
tags: ["Games","Projects"]
featured_image: "/images/games/rootscapedcover.png"
description: "Global Game Jam 2023"
toc: true
---

The Theme for Global Game Jam 2023 was Roots. I made a game about root within 48 hours with a few of my colleagues called [Rootscaped](https://github.com/GGJ2023/Rootscape). <!--more-->
Grow your tree's roots down to search for water! Every time you collect water, your roots split. Watch out for bugs and rock which will stop your root's growth! Try to grow the largest tree and root system you can! 

---

### About

![Game Preview](https://img.itch.zone/aW1nLzExMjU0ODY3LmdpZg==/315x250%23c/GOBe3F.gif)

<!-- {{< itch "https://itch.io/embed-upload/7300158?color=743f39">}} -->

* [Play Online](https://kanaz312.itch.io/rootscaped)

Audio will play on launch. We recommend you play in full screen! Press space to start once the game has loaded. Game is not optimized for mobile and requires a keyboard.

---
### Controls
* Spacebar: Start game
* Up Arrow Key: Speed up
* Left / Right Arrow Keys: Move roots left/right

### Technical Details
The overall development involved some of these components, such as root mechanic/movement, camera system, sound system, enemy spawner/AI, and UI. Within them, here are a list of the things I did as part of the development:

#### Root Trail Rendering
As part of the main gameplay mechanic, the root and its splitted clones are grown using Unity trail rendering. The width of the root is programmed using a function and a hydration material is applied in addition. The root is controlled using WASD by a Rigidbody2D and a Box Collider 2D on the front end of the root for collision with bugs, water, and other entity components.

![Game Preview](https://img.itch.zone/aW1hZ2UvMTkxMzcwMS8xMTI0NjcwNi5wbmc=/794x1000/GJEZ9d.png)

#### UI/Scene Transation
I minimized the game into 2 scenes for performance and simplicity within 48 hours. 
- Start Scene
![Game Preview](/images/games/rootscapedcover.png)
The start scene serves as the start, and end of the game. The game started by showing a small sprout coming out of the soil, and as the game continues, the sprout grows into a tree. I programmed the title fade out, camera transformation, and the initial design of the scene.

- Game Scene
![Game Preview](https://img.itch.zone/aW1hZ2UvMTkxMzcwMS8xMTI0NjcwNS5wbmc=/794x1000/0UH%2B0E.png)
The game scene starts from the coming out of the root from the top of the screen. Once the game ends, I programmed the UI to be simple and easy to understand: press space to continue back to the top to show the grown tree, and press space again to restart the game.

#### Background Music Management
The background music is also a big part of the game play. Other than making and programming the pieces that play for the whole game using a simple audio source plugin, some of the BGM are layered and need to be activated and deactivated throughout the game.

After making and mixing the BGM with Brett, we splitted the music track by track for further development. I developed a BGM manager for emotional music progression and layering. The BGM manager contains a list of Sound objects, which are self-defined using a name, audio clip, volume, and an audio source component. 

```c#
[System.Serializable]
public class Sound
{
    public string name;

    public AudioClip clip;

    [Range(0f, 1f)]
    public float volume;

    [HideInInspector]
    public AudioSource source;
}
```

The BGM manager keeps track of these components. At the same time, its support fades in/out of layers depending on whether an event is triggered. Once the maximum number of layers is reached, the program will randomly fade out a layer and fade in another one for a different variety.

```c#
[System.Serializable]
public class BGMManager : MonoBehaviour
{
    public Sound[] sounds;

    ...

    void Update()
    {
        if (Globals.numberOfSplits == 3)
        {
            FindObjectOfType<BGMManager>().Play("topMelody");
        }
        else if (Globals.numberOfSplits == 8)
        {
            FindObjectOfType<BGMManager>().Play("topMelodyDelay");
        }
        else if (Globals.numberOfSplits == 12)
        {
            FindObjectOfType<BGMManager>().Play("Chords");
        }
        else if (Globals.numberOfSplits == 16)
        {
            FindObjectOfType<BGMManager>().Play("Shakes");
        }
        else if (Globals.numberOfSplits >= 18)
        {
            if (Globals.numberOfSplits % 5 == 0)
            {
                int rand1 = UnityEngine.Random.Range(0, alternative.Length);
                int rand2 = UnityEngine.Random.Range(0, alternative.Length);
                FindObjectOfType<BGMManager>().Stop(alternative[rand1]);
                FindObjectOfType<BGMManager>().Play(alternative[rand2]);
            }
        }
    }
}
```


#### SFX
In a similar structure, the SFX management system also has a list of Sound objects. But instead of playing everything in the beginning, mute everything, and looping each one, I used the audio source plugin to play the sound when an event happens, such as getting water, encountering rocks, being eaten by bugs, and the digging sound.

```c#
[System.Serializable]
public class SFXManager : MonoBehaviour
{
    public Sound[] sounds;

    // Start is called before the first frame update
    void Awake()
    {
        foreach (Sound s in sounds)
        {
            s.source = gameObject.AddComponent<AudioSource>();
            s.source.clip = s.clip;
            s.source.volume = s.volume;
            s.source.loop = false;
        }
    }

    public void Play(string name)
    {
        Sound s = Array.Find(sounds, sound => sound.name == name);
        s.source.Play();
    }
}
```

### Last Words

Once again, this game won't be possible to be done within 48 hours without help. So big thanks to everyone who participated! Made for [Global Game Jam 2023](https://globalgamejam.org/2023/games/rootscape-0) via the Cal Poly SLO Game Development Club Site.

