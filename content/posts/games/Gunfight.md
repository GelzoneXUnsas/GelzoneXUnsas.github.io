---
title: "Gunfight"
date: 2023-03-24T01:18:59-08:00
draft: false
author: ["Lucas Li", "Brett Hickman", "Annie Fanelli", "Marisa Lin"]
tags: ["Games","featured"]
featured_image: '/images/games/gunfight_cover.png'
description: "top-down multi-player shooter game"
toc: true
---

A **Top-Down 2D shooter** where two teams fight each other to advance in a tournament-style **online** game. Each player spawns with the same weapon and whichever team is last standing wins that round. Games go on for a best of 5 rounds to see who advances. <!--more--> There is also a free-for-all game mode where random weapons spawn around the map and you have to right to survive.

![](/images/games/gunfight_cover2.png)

## Related Work

We really took inspiration from three games and combined them into one.

- Impact Point: The top-down weapons and shooting mechanics in this game were notable. The team was impressed with how the game handled weapon management and thought it would be a wonderful addition to our own game.

![](https://cdn.cloudflare.steamstatic.com/steam/apps/1680550/ss_91cb913bb860621463af17dba6715719ae2201e6.1920x1080.jpg?t=1659315003)

- Hotline Miami: Hotline Miami's fast-paced, furious gameplay impressed the team. We believed that the high-octane action would bring a lot of interest to our own game.

![](https://cdn.mos.cms.futurecdn.net/2e63efbb22da9065c0074c9d351ffff7.jpg)

- Call of Duty (game mode): We particularly enjoyed the Call of Duty 2v2 tournament gaming mode. This game option allowed for heated competition amongst players, and the team thought it would be a perfect addition to our own game.

![](https://i.ytimg.com/vi/bbyvta_9dRQ/maxresdefault.jpg)

--------------------------------

Our game is different because the gunfight tournament gameplay has never been done in 2D before. Basically, the only thing we are taking from the impact point is the movement and gunplay. Also, our art style is more cohesive and uses a **70’s vector art** style.

![](https://t3.ftcdn.net/jpg/04/22/63/26/360_F_422632672_uN0E03LPQ5zFwMPuGR6d4v0T3XX4m3sW.jpg)

## Design

The gameplay experience is trying to convey the intense and gritty nature of a life of competing for resources. It is meant to immerse the player in a world of conflict and danger, where every decision could mean the difference between life and death. The fast-paced and intense gameplay is meant to convey the urgency of the situation and the need to act quickly and decisively.

### Controls

In this game, the player has a variety of inputs available to them in order to navigate and interact with the game world. The primary means of movement is through the use of the **WASD** keys, which allow the player to move their character forward, backward, left, and right. This movement is crucial for exploring the game's world, dodging enemy attacks, and positioning oneself for strategic advantage.

![](/images/games/gunfight_control.png)

In addition to movement, the player also has the ability to use the mouse to **aim** and **fire** their weapon. The mouse allows for precise aiming, and the player can use it to **rotate** their character in any direction they choose. This is especially important during combat, where the player must be able to react quickly to enemy movements and accurately target their attacks. Furthermore, the player can use a variety of other inputs to interact with the game world. For example, they may need to **right-click** to access weapons or equipment.

### GameFeel

One of the main technical challenges in game development is creating a sense of "game feel." This refers to the tactile and responsive nature of the game's mechanics, which can greatly affect the player's experience. By optimizing collision, transforms, animation, and post-processing rendering, we can create a game that feels **responsive**, **immersive**, and **satisfying** to play.

## Implementation

This game's technological implementation entails the use of multiple technologies and tools in **Unity** to produce a polished and engaging experience for the player:

1. Steamworks & Mirror

Steamworks and Mirror API are two essential technologies used, allowing for smooth **multiplayer** functionality via the **Steam** platform. This enables users to easily connect and play with others online, boosting the game's replayability and social elements significantly.

```c#
public override void OnServerAddPlayer(NetworkConnectionToClient conn)
    {
        if(SceneManager.GetActiveScene().name == "Lobby")
        {
            PlayerObjectController GamePlayerInstance = Instantiate(GamePlayerPrefab);

            GamePlayerInstance.ConnectionID = conn.connectionId;
            GamePlayerInstance.PlayerIdNumber = GamePlayers.Count + 1;
            GamePlayerInstance.PlayerSteamID = (ulong)SteamMatchmaking.
                            GetLobbyMemberByIndex((CSteamID)SteamLobby.Instance.CurrentLobbyID, 
                                                    GamePlayers.Count);

            NetworkServer.AddPlayerForConnection(conn, GamePlayerInstance.gameObject);
        }

    }
```
--------------------
![](/images/games/gunfight_lobby.png)

2. Aseprite

Another useful tool is the Aseprite, which generates the game's **map** and **assets**. This allows for efficient world creation in the game, as well as easy modification and iteration during development.

![](/images/games/gunfight_sprite.png)

3. Universal Render Pipeline

**Post-processing** using Universal Render Pipeline (URP) is used in the game to enhance its visual effects, creating a more immersive and engaging experience for the player. This includes effects such as motion blur, depth of field, and color grading, which can greatly enhance the game's overall aesthetic.

- map
|
|:-:|:-:|
![BEFORE](/images/games/gunfight_beforePE1.png)  |  ![AFTER](/images/games/gunfight_afterPE1.png)
- In-Game
|
|:-:|:-:|
![BEFORE](/images/games/gunfight_beforePE2.png)  |  ![AFTER](/images/games/gunfight_afterPE2.png)

4. Raycast

The game employs raycast shooting for **weapon hit detection**, which enables for precise and accurate detection of hits and misses. This is critical for establishing a gratifying combat experience and giving players a sense of control over their attacks.

```c#
if (PlayerModel.GetComponent<PlayerInfo>().nAmmo > 0)
{
    Vector2 mousePos = cam.ScreenToWorldPoint(Input.mousePosition);
    Vector2 direction = (mousePos - (Vector2) shootPoint).normalized;
    RaycastHit2D hit =
        Physics2D
            .Raycast(shootPoint,
            PlayerModel.transform.up,
            PlayerModel.GetComponent<PlayerInfo>().range);
    ...
}
```

5. Game Juice Techniques

To create a sense of **impact** and **excitement** during gameplay, the game employs a range of game juice techniques such as particle systems, camera shakes, SFXs, and BGMs. This contributes to a more immersive and engaging experience for the player, as well as considerably improving the game's overall visual and feel.

## Analysis / Verification

The goals for this game that we wanted to accomplish were to create two game modes, team and free for all, implement different weapons, and have shooting mechanics. We were not able to implement the team game mode but we did implement a free for all game mode with rounds, multiple weapons to use in the game, and the shooting mechanics for those weapons. In the free for all game mode, weapons are randomly spawned throughout the map and the players fight to survive. From the [game survey](https://docs.google.com/spreadsheets/d/1eVURxzKDa9ueqSXDtCbsYZvHK9GhQxBN5HRb4JTkN6g/edit?usp=sharing) we collected, playtesters were pleased with this game mode and the overall quality of the game. They were pleased with the overall art style, the intuitiveness of the controls, and the fairness of the game. The playtesters also enjoyed the multiplayer aspect of the game, where they could play with their friends.

## Future Work

1. Game Mode

One of the important features is to have different game modes, such that the user can decide to play 1v1 or 2v2. Having a UI to set teams is one of the priorities to focus on next. In addition, maps for different game modes are also next to invest on. To determine which team wins, a team needs to be the first one to win five rounds.

2. Polish

Polish will contribute to a great game feel and bring the immersiveness of the game. For example, the swinging animation of a knife will show the player that one is trying to behave like a human. Background music is also a great source to spread emotion to the player.

3. Equipments

There are a total of five weapons implemented and spawned in the game: AK47, pistol, sniper, and uzi. In the future, grenades and armor will be implemented. Grenade’s ability is to make the target not visible for a while. Armor will take some damage from the player.

4. Multiplayers Toolkit

As Gunfight is a multiplayer game, a few things are great to have. This includes proximity chat, where teammates can talk to each other during the game and talk to other teams after the game. On the other hand, a match system will match fandom people to play.
