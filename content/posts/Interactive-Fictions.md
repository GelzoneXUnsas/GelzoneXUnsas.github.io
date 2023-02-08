---
title: "Interactive Fictions"
date: 2023-01-15T01:18:59-08:00
draft: false
author: ["Lucas Li"]
tags: ["Games","Projects"]
featured_image: "/images/games/if.png"
description: "Text Adventure Games Made by Inform & Twine"
toc: true
---

Some interactive fiction games I made. The first game is parse-based, while the second is choice-based game.  <!--more-->

* Jack's World Adventure
* Post-Apocalyptic Prodigies

Interactive fiction (IF) refers to a genre of computer games or stories in which the player makes choices that affect the outcome of the story. The player interacts with the game by entering text commands to advance the story, make choices, and solve puzzles. 

---

### Jack's World Adventure

![Game Preview](/images/games/if.png)

* [Play Online](https://gelzonexunsas.itch.io/jacks-world-adventure)

---
#### Story Description
Once upon a time, there was a young adventurer named Jack. He had always dreamed of exploring the world and discovering hidden treasures. One day, he set out on a journey to fulfill his dream. During his journey, visiting many more places and met many more people. He faced many challenges and overcame many obstacles, but through it all, he never gave up on his dream. He had many adventures, but he always remembered the jungle, the lost city, the mountain and the village as the most exciting and rewarding experiences of his journey.

#### Technical Details
##### Rooms
There are a total of 8 different rooms in the game. Some of them are connected and can be navigated (eg. Town, Shop), and others can only be accessed through teleporting when the game progresses (eg. Mountain, Valley).

![Game Preview](/images/games/if_map.png)

##### Health
Health is an important aspect in the game. The player’s health has been initialized to 3 in the beginning of the game. The health can be lost by walking into the wrong/dangerous paths, or enemy attacks, or be gained by consuming health potion that is selled in the shop. Player dies when health is down to 0, the game ends.

##### NPCs
- Shopkeeper: Shopkeeper is in the Weapon Shop, which sells weapon, armor, and
health potion.
- Old man: The wise old man who had lived in the jungle for many years. The old man told him stories of a lost city deep in the jungle north from here.
- Guard: A cannibal is guarding the entrance to the lost city.
- Yeti: A wild monster that lives in the Himalayas mountain.

##### Objects
Some of the objects are selled in the shop (eg. sword, shirt, potion), others are gained throughout the gameplay (eg. flower)

#### Mechanics
- Inventory

The player can view their inventory by typing “i/inventory”, they can further examine how much money they have through “x/examine gold.”
- Currency

Gold is the currency in the game that can be used in the shop. The player has 50 Golds in the beginning of the game. The gold will be decreased after purchases.
- Shop

The shop is located in the west of the Town. The player is able to buy weapons, armor, and health potion in the shop.
- Player Status

Player status (eg. health) can be changed after being attacked by an enemy, or consume the health potion.
The player inventory is being updated throughout the gameplay.
Furthermore, the player status can be changed by wearing armor (reduce enemy
attack damage), or using the long sword instead of the dagger (increase self attack damage).

#### Advance Inform Technics
- Bracket
```Racket
Instead examining player:
    say "You have [health of player] health points left."
```
- Conditionals within Bracket
```Racket
Gold is a thing. 
The printed name is “[if cash is 1]a[otherwise]some[end if] Gold[if cash is greater than 1]s[end if]”.
```
- Number
```Racket
cash is a number that varies. cash is 50.
```
- Understand
```Racket
Understand the command "talk" as something new. 
Understand “talk to [someone]” as talking to. 
Understand “talk to [something]” as talking to.
Talking to is an action applying to one visible thing.
```

---

### Post-Apocalyptic Prodigies

![Game Preview](/images/games/twine.png)
[Play Online](https://gelzonexunsas.itch.io/post-apocalyptic-prodigies)

---

#### Tropes
- Post-apocalyptic setting
- Team of vigilantes
- Exploration of a lost civilization
- Use of advanced technology as weapons

#### Story Description
In a distant galaxy, a group of vigilantes set out on a mission to save their dying home planet. As they ventured through the vast expanse of space, they came across a seemingly doomed planet, ravaged by war and destruction. But as they explored the post-apocalyptic cityscapes, they discovered something that would change the course of their mission forever. They stumbled upon a civilization that had created musical instruments unlike any they had ever seen before. These instruments possessed the power to produce sounds that could kill, and the vigilantes knew that with these instruments, they could save their people and create a new home for their overpopulated planet. This is the story of their journey and the discovery of the instruments of sound.

#### Character Design
**Jack the Protagonist**
- Physical description: Jack is a tall, lean man with short brown hair and a scruffy beard. He has deep-set blue eyes and a scar above his left eyebrow.
- Personality Traits: Jack is a leader, he is confident and charismatic. He is also determined, and will stop at nothing to achieve his goals.
- Primary Actions: Jack is the leader of a team of vigilantes who are on a mission to save their overpopulated home planet. He is responsible for gathering information on the doomed planet and analyzing the inventions of the once-civilized society.
- Purposes/Goals: Jack's primary goal is to save his overpopulated home planet by using the instruments from the doomed planet.
- Relation to other characters: Jack's relationship with the Antagonist is a little complicated, they had the same goal but they did not agree with the way of achieving it. Jack is also in a good relationship with his ally, they have a good working relationship, and they trust each other's judgment.

**Ryan the Antagonist**
- Physical description: Ryan is a tall, well-built man with short black hair and a clean-shaven face. He has cold, piercing green eyes and a scar above his right eyebrow.
- Personality Traits: Ryan is a thoughtful man. He is also arrogant sometimes, thinking that he is always right and everyone else is wrong.
- Primary Actions: Ryan is a businessman who is also a member of a rival faction on Jack's home planet.
- Purposes/Goals: Ryan argued that the instruments were not meant to be used as weapons, and that it was wrong to exploit the planet's resources for your own gain.
- Relation to other characters: Ryan is the Antagonist to Jack, he always dislikes how Jack makes irrational moves.

**Sarah the Ally**
- Physical description: Sarah is a short, slender woman with long, curly red hair. She has warm, brown eyes and a friendly smile.
- Personality Traits: Sarah is a skilled engineer and inventor. She is intelligent, curious, and always eager to learn.
- Primary Actions: Sarah is a member of Jack's team of vigilantes and is responsible for analyzing and understanding the inventions of the once-civilized society on the doomed planet.
- Purposes/Goals: Sarah's primary goal is to help Jack and his team save their overpopulated home planet by using the instruments from the doomed planet.
- Relation to other characters: Sarah has a good working relationship with Jack, the protagonist. She trusts his leadership and is always willing to follow his direction. Sarah's relationship with Ryan, the antagonist, is strained. She is aware of his ruthless tactics and does not agree with his methods.