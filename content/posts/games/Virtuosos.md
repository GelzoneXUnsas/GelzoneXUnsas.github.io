---
title: "Virtuosos"
date: 2023-06-18T01:18:59-08:00
draft: true
author: ["Lucas Li", "Alex Hartford", "Brett Hickman", "Coby Chuang", "William Yi", "Kaylen Wan", "Tiffany Vu", "Kevin Yan", "Nancy Chen"]
tags: ["Games", "featured"]
featured_image: '/images/games/DV_cover.png'
description: "Captivating Rhythm JRPG"
toc: true
---

Recently, I have been pondering a question: how can I enhance the immersion and emotional impact of a musical composition? After much consideration, I came to the conclusion that rather than creating just a standalone piece, it would be better to develop a **rhythm game**. <!--more-->

During my time in university, I developed a deep appreciation for games as the "ninth art." Through interactive design, games provided me with a sense of immersion and satisfaction. I wanted to share this experience with fellow music enthusiasts, allowing them to freely perform and enjoy music within the world of a game.

> *Learn and master mesmerizing rhythms and melodies as you embark on a musical journey of self-discovery.*

![Game Preview](/images/games/DV_gameplay_1.png)

- [Play Demo](https://gelzonexunsas.itch.io/virtuosos)

---

### Inspiration

As the **director**, **project manager**, and **developer** of「**Virtuosos**」, I made an unique blend of rhythm and JRPG elements, incorporating influences from several my beloved games and anime:

* **Muse Dash**
  * One of the key inspirations for the gameplay mechanics in「Virtuosos」comes from the popular game [Muse Dash](https://musedash.peropero.net/#/). 
  ![](https://cdn.akamai.steamstatic.com/steam/apps/774171/ss_a8d9434bb3ecdfd3100f3d24f4ba7b6ebe36427b.1920x1080.jpg?t=1686650225)
  * We were drawn to the classic horizontal screen layout of "Muse Dash" and how it enhances the rhythmic gameplay. This influence led us to adopt a similar layout, allowing players to engage with the game's musical elements in an exciting way.

* **NieR: Automata**
  * When it comes to world-building and storytelling, I found inspiration in the exceptional game [NieR: Automata](https://nierautomata.square-enix-games.com/en-us/home/).
  ![](https://cdn.80.lv/api/upload/content/b2/5d2d0d61276c9.jpg)
  * The dystopian themes and intricate narrative of "NieR: Automata" resonated with us deeply, and we aimed to infuse a similar sense of depth and intrigue into the world of 「Virtuosos」. As players progress through the game's various levels and encounters, they will uncover a rich and immersive story that will keep them engaged and eager to explore further.

* **Made in Abyss & Girls' Last Tour**
  * In terms of art direction, we took inspiration from two distinct anime series. [Made in Abyss](http://miabyss.com/) provided a visual inspiration with its detailed and atmospheric environments, while [Girls' Last Tour](http://girls-last-tour.com/) influenced us with its minimalist yet evocative background art style. 
    |
    |:-:|:-:|
    ![Made in Abyss](https://blog.sakugabooru.com/wp-content/uploads/2017/09/abyss10.jpg)  |  ![Girls' Last Tour](https://i0.wp.com/wrongeverytime.com/wp-content/uploads/2019/07/chrome_2019-07-17_15-02-51.jpg)
  * By combining elements from these two anime, we aimed to create a visually stunning world within "Virtuosos" that would draw players in and enhance their overall gaming experience.

---

### Gameplay
As a JRPG rhythm game, 「Virtuosos」 combines the style of Japanese role-playing games with elements of rhythm games. Within different levels of the game, players will discover the background stories of various characters and their environments, gradually exploring new gameplay mechanics. And of course, they will be able to indulge in the joy of music!

---

#### Music Performance
The heart of 「Virtuosos」 lies in its immersive music performance gameplay. Players step into the role of a talented musician and embark on a quest to master rhythms and melodies. Through carefully timed inputs and precise execution, players must perform musical sequences to progress through levels and overcome challenges.

![](/images/games/DV_gameplay_2.png)

#### Visual Novel
On the other side, players are invited to embark on an immersive journey through a rich and captivating story. Dialogues and Narrtives will provide deeper insights into the world of Virtuosos, allowing players to develop connections with the characters and become fully immersed in the narrative. 

![](/images/games/DV_d2.png)

---

### Technical Details
In 「Virtuosos」, I took on the role of not only **project manager** but also the **primary developer**, ensuring that every aspect of the game meets the highest standards of quality. In order to improve maintainability, collaboration, and performance, we implemented modular and structured code in the development of 「Virtuosos」 to lay the foundation for a high-quality and enjoyable gaming experience:

#### Sound Management
* Music

All the original sound tracks in game are handled by the **Conductor** class. The main functionality of the class is to keep track of the current song position, and schedule the next piece of the music when needed. Here is a simplified version of the class, showing some of the constants values needed per song:
```c#
public class Conductor : MonoBehaviour
{
    // constants info for a song
    // for example, 4/4 Time with a resolution of 16th notes
    private const int SPOTS_PER_BEAT = 4;
    private const int BEATS_PER_BAR = 4;

    // other constants info for a song
    public float bpm;
    public float beatLength;
    public float spotLength;

    // use to keep track of song position
    public int spotNumber;
    public int beatNumber;
    public int barNumber;

    // number of beats until the first beat of the beatmap
    public int beats_till_first_note;
    // constant offset for a song if not on beat
    public double offset;
    // player set offset based on monitor/speaker used
    public float latency_offset;

    // times for play scheduled
    private double startTime;
    private double nextStartTime;
    private double lastStartTime;

    // load drummer section as an example
    public bool playDrumsNextBar = false;

    void Start()
    {
        // initialize all the constants
        initConstants();
    }

    public double GetSongPosition()
    {
        // Returns the unequivocal position in the current song.
        return AudioSettings.dspTime - startTime 
            - (beats_till_first_note * beatLength) 
            - offset + latency_offset;
    }
}
```

Notice that the **GetSongPosition()** function uses **dspTime** instead of normal Unity time. This is because When dealing with audio playback, scheduling events, or syncing audio with other gameplay elements, the audio system in Unity operates on a separate timeline, and **dspTime** provides a more accurate representation of time in the audio engine.

The following code is also part of the **Conductor** class, showing how we manage to schedule the next piece of the music when needed. Whether the player want to stay in the current sound track, or continue with the drummer portion of the level:

```c#
public void Update()
{
            // if this is level 1, having 2 of the same BGM loaded
            if(PlayerPrefs.GetInt("level_number") == 1) 
            {
                // if the first BGM is playing
                if(current_background == 1) {
                    // scheduled the second BGM to play at the next in-coming bar
                    background2.PlayScheduled(nextStartTime);
                    current_background = 2;
                }
                // if the second BGM is playing
                else {
                    // scheduled the first BGM to play at the next in-coming bar
                    background.PlayScheduled(nextStartTime);
                    current_background = 1;
                }
            }
            // Check if the drummer section want to start
            if(playDrumsNextBar) {
                // scheduled the drummer song to play at the next in-coming bar
                playDrumsNextBar = false;
                drums.PlayScheduled(nextStartTime);
            }

            // update the current timers for play scheduled
            lastStartTime = nextStartTime;
            nextStartTime = nextStartTime + backgroundDuration;
}
```
* SFX
Sound effects are a lot easier to implement. Since sound are mostly happening in real-time during gameplay triggered by user inputs, they can be simply played or stoped by calling. Using the one of the cutscene sound effects as an example:

```c#
public class CutSceneSFX : MonoBehaviour
{
    public AudioSource fire;

    public void Playfire()
    {
        fire.Play();
    }

    public void Stopfire()
    {
        fire.Stop();
    }

}
```

---

#### Beatmap System
If you're unfamiliar with rhythm games, a **beatmap** is a custom-designed level or sequence of notes that players must follow and synchronize with the game's music. 

To create the beatmap for each level, one approach we considered is storing all the necessary information in a text file.
```txt
----x-----------    // ex.
---------x--x---    // play track 1 & 3 at beat 2 spot 1
----x-----------    // play track 2 & 4 at beat 3 spot 2
---------x------    // play track 2 at beat 4 spot 1
```
> how to support different note types?

> how to hear what's it sounds like?

> how to keep track of song position?

While we were familiar with reading and writing text files, we realized they had certain **disadvantages**: they were difficult to **auralize**, **debug**, while trying to add or change inforamtion. As a solution, I explored the idea of using **MIDI** files instead. Although the primary purpose of using MIDI files wouldn't be for playing sound, we could utilize them to spawn notes in the game.

* **MIDI**

Here is my design with the use of MIDI file in the current game with the [Melanchall](https://melanchall.github.io/drywetmidi/api/Melanchall.DryWetMidi.Common.html) API:
  * NoteElement - *one single note in the music*
```c#
struct NoteElement
// each note in the game has following components:
{
    // The note number (pitch), use for instruments & tracks:
    /* 
        DRUM: 56 (G), 57 (A)
        GUITAR: 58 (A#), 59 (B), 60 (C)
        PIANO: 61 (C#), 62(D), 63 (D#), 64 (E)
    */
    public byte number;

    // position in time of the note as an instance of time span
    public ushort[] pos;

    // in units of 16th note length of 120
    // for a quarter note, length = 4
    public ushort length;

    // The note velocity, use for type:
    public byte velocity;
}
```
  > Every note consists of a number, position, length, and velocity. In 「Virtuosos」, a number is used for specifying what type of instrument and which track the note is playing from. 
  ```txt
     -------------                                > piano (64)
     -------------                > guitar (60)   > piano (63)
     -------------   > drum (57)  > guitar (59)   > piano (62)
     -------------   > drum (56)  > guitar (58)   > piano (61)
  ```
  > For example, note number 57 will be a drummer note playing on the second track from the bottom; note number 63 will be a pianist note playing on the third track from the bottom.
  > The velocity is used to describe the note type.
  ```txt
    1: drummer switch
    2: guitarist switch
    3: pianist switch
    64: Hit note
    72: Hold note
    80: obstacles
    88: collectible
    96: ...
  ```
  > For example, a velocity of 72 will be a hold note.

* SpotElement - *one single spot in the music*
> A spot is the smallest unit, a 16th note in our current design, that the player can hit and trigger detection on while playing:
```c#
public struct SpotElement
{
    public NoteElement four;
    public NoteElement three;
    public NoteElement two;
    public NoteElement one;
}
```
> Since we have a maximum of 4 tracks (ex. pianist), there are a total 4 NoteElements per spot. If we are in the drummer section, the top two tracks will be NULL.

* SpotTrack - *the beatmap*
```c#
SpotTrack = new SpotElement[totalBars * BEATS_PER_BAR * SPOTS_PER_BEAT];
```
> Lastly, we can construct an array of SpotElements for the beatmap, with the length being the total bars in the MIDI * the total number of spots initialized by the Conductor.

* **Spawning**

Base on the beatmap structure above, we can simply spawn a note in the **SpawnMaster** class on a given track base on its **type**, **length**, and **position**. 
> For example, to spawn a note on the first track:
```c#
if (current_spot.one.velocity == 64)
    // spawn hit note
    spawner1.Spawn(NOTE, 0, position);
else if (current_spot.one.velocity == 72)
    // spawn hold note
    spawner1.Spawn(HOLD, current_spot.one.length, position);
else if (current_spot.one.velocity == 80)
    // spawn obstacle
    spawner1.Spawn(OBST, 0, position);
else if (current_spot.one.velocity == 88)
    // spawn screw (collectible)
    spawner1.Spawn(COLL, 0, position);
```
Updating note position can be divided into 2 pieces: move towards, and bounce off. First, we move the note towards the player using **linear interplations**:

![](/images/games/DV_interp_1.png)

> The start point of the interplation is at the track spawner (out of the viewport of the camera), and the end point is at the note trigger in front of the player, with the interplation factor based on current song position and current spot position.

Next, if the player hit the note at the correct time, the note changes its movement and bounce off. In this case, we change the movement calculation to use **cosine interplations**:

![](/images/games/DV_interp_2.png)

> The start point of the interplation is at the the note trigger, and the end point is at the track spawner, with the interplation factor based on the following equation:
```c#
float cos_interp(float a, float b, float t)
    {
        float ct = -(1.0f 
                    - Mathf.Cos((1.0f - Mathf.Clamp(t, 0, 1)) 
                    * Mathf.PI * 0.5f));
        return a * (1 - ct) + b * ct;
    }
```
> using mostly standard cosine interplations with directional modification.

---

#### Visual Integration

In「Virtuosos」, my focus on visual integration is to showcase the game's art to its maximum potential, aiming for a natural and dynamic presentation.

* **Map**

At the beginning of the development process, one of my first tasks was to create the game's map. While utilizing a free [Unity asset](https://assetstore.unity.com/packages/2d/environments/free-asset-2d-handcrafted-art-117049) at that time, my focus was on crafting an infinite terrain by seamlessly looping the map and dynamically removing previously traversed sections. This approach allowed for a continuous and immersive gameplay experience.

Afterward, I collaborated closely with the talented artists to craft the final map for our game. 

![](/images/games/DV_far_background.jpg)

Together, we meticulously divided the map into distinct layers, carefully separating elements such as sprites, particles, and lights. This approach ensured that each layer contributed to the overall visual richness and depth, resulting in a captivating and visually immersive gaming environment.

![](/images/games/DV_map.gif)

* **Character**

Personally speaking, the integration of characters was challenging and engaging, requiring collaboration with various team members including concept artists, lead artists, and the writer. Together, we embarked on an extensive creative process, aligning the character design with the game's story, art style, and gameplay mechanics. This intricate task demanded careful consideration of every detail, resulting in the development of compelling and well-integrated characters.

![](/images/games/DV_drummer_con.jpg)

> Drummer Concept Art

The **PlayerController** class takes the reins in controlling the characters within「Virtuosos」. Serving as a central hub, it manages essential player information such as health, position, and velocity. Additionally, the PlayerController facilitates **character switching** and handles the intricacies of **animation**, which will be discuss next.

---

#### 2D Animation
By the end of my second quarter working on the game, I came to the realization regarding the significance of high-quality animations. 

![](/images/games/DV_preview.gif)

Among them, attack animations emerged as a key element that significantly enhanced the game feel, particularly in the context of a rhythm-based game like「Virtuosos」.

* **Skeletal Animation**

In「Virtuosos」, all the playable characters come to life through the magic of skeletal animation, offering us customization and reusability. When we initially dove into 2D animation in Unity, we sought inspiration and reference from other games' animations. However, we put our unique spin on things when it came to the frame ratio for attack animations.

![](https://ultimateframedata.com/hitboxes/link/LinkJab3.gif)

Now, here's where we did things a bit differently compared to some other games out there (let's take Link as an example). [Attack animations](https://www.youtube.com/watch?v=LewXWM7HDd8) often have those stand-by, lead-in, attack, and follow-through phases. However, in our game, I made a bold decision to shake things up. I completely ditched the lead-in phase to give our rhythm game a distinctive and unparalleled game feel.

![](/images/games/DV_anim.gif)

By doing so, we created a sense of immediacy and responsiveness that perfectly aligns with the rhythm-based mechanics. It's all about hitting those beats and maintaining that flow without any **unnecessary delays**.

* **Sprite Animation**

![](/images/games/DV_enemy3-01.png)

When it came to the other in-game elements like notes and obstacles, we opted for sprite animation. Sprite animation provided us with a simpler and more efficient solution. 

![](/images/games/DV_sprite_anim.gif)

It was a breeze to implement, and whenever edits or adjustments were needed, we could tackle them with lightning speed. After all, when it comes to making quick changes or improvements, efficiency is key.

---

#### Detection Recognition
* **Note Detection**

In「Virtuosos」, every note is detected by the **Hit Category** and its **threshold**.

One might already know this if they played the game, there are three different Hit Category:

```c#
public enum HitCategory
{
    MISS,
    GOOD,
    PERFECT
}
```

The range of each one is tied to the **threshold system**. Accuracy is defined by a floating number, with smaller thresholds indicating more demanding timing. A "good" action has a threshold of **1.0**, offering a modest window, while achieving "perfect" requires remarkable precision with a threshold of only **0.3**.

Using the following case as an example:

![](/images/games/DV_note_detect.png)

> A actual hit always happened in between 2 spot

Here's a simplified version of the note detection code:

```c#
// check the nearest spot
if (since_last_spot < till_next_spot)
    offset_from_spot = (float) since_last_spot;
else
    offset_from_spot = (float) till_next_spot;

// calculate hit category based on threshold
if (offset_from_spot < perfect * conductor.spotLength)
{
    hit_category = HitCategory.PERFECT;
}
else if (offset_from_spot < good * conductor.spotLength)
{
    hit_category = HitCategory.GOOD;
}
else
{
    hit_category = HitCategory.MISS;
}
```

This simplified implementation provides an overview of the note detection code. However, in our game, the logic becomes more complex due to additional considerations, such as guarding against **multiple presses** and handling **overlapping thresholds**.

* **Input Detection**

To address the challenges of supporting multiple input systems during development, we transitioned from the chaotic input detection approach to a more organized solution. Instead of relying solely on the standard Unity Input API, we adopted the use of [Action Map](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.0/manual/Actions.html).

![](/images/games/DV_input_detect.png)

This change allowed us to streamline input detection and better manage the complexities associated with various input methods. 

Refering to a low track input dectection:

> Input API
```c#
if (checkHit(KeyCode.Joystick1Button0, 
            KeyCode.Joystick1Button2, 
            KeyCode.Joystick1Button1, 
            /* potentially needed for other 
                controller or platform support,
                but is overlapping with other
                tracks.
            KeyCode.Joystick1Button7,
            KeyCode.Joystick1Button6,
            KeyCode.Joystick1Button4,
            */
            InputSystem.GetButton("leftShoulder"),
            KeyCode.K, 2, low))
    hasBeenPressed[2] = true;
```

> Action Map API
```c#
public InputActionReference lowControls;

if (checkHit(lowControls, 2, low))
    hasBeenPressed[2] = true;
```

The implementation of the Action Map not only facilitated support for multiple controllers and platforms, but it also offered increased flexibility for making simultaneous changes when necessary.

* **Latency Detection**

Detecting and adjusting for latency is of utmost importance in「Virtuosos」. Players rely on precise timing to hit notes, and even the slightest delay can throw off their rhythm and disrupt the gameplay flow. To address the variability in latency between different monitors and speakers, we have implemented a user-friendly interface that allows players to manually adjust the latency while listening to the background music from the start page.

![](/images/games/DV_calibrate.png)

By providing this interface, players can fine-tune the synchronization between the visual cues and the corresponding audio feedback according to their specific setup. Each time the user presses the interaction keys (such as space or a button on the controller), the game system calculates the latency by recording the current offset from the correct beat using the Unity audio engine. The system then displays the average offset of the **10 most recent** presses from the user.

---

#### Level Design
* **Modular Level**
* **Global Spine**

---

#### Others
* **Dialogue & Narrative**
* **Particle System**
* **Player Stats**
* **Player Setting**