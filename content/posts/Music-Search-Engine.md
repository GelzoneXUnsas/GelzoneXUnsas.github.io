---
title: "Music Search Engine"
date: 2022-06-14T01:18:59-08:00
draft: true
author: "Lucas Li"
tags: ["Projects"]
featured_image: 'https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Songs%20Table.png?raw=true'
description: "audio visualizers created with OpenGL"
toc: true
---
A [music search engine](https://github.com/CSC-365-Project/Music-Search-Engine) that can not only search songs by filtering, but also store them as favorite for future lookup.<!--more-->

[![Visualizer Preview](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Home.png?raw=true)](https://drive.google.com/file/d/1z5xM933W21LwqYWO9PwkqsRJHWjmdIlC/view?usp=sharing)

  _Click on the image above for Demo_

## Overview
#### UML Diagram
  * A brief walk-through to our database structure

![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/uml.png?raw=true)
_DB Conceptual UML_
![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Songs%20Table.png?raw=true)
_Sample Data_
#### User Stories
  * As user, I want to be able to search music by name, artist, and genre.<br />
  ![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Search%20By%20Song%20Name.png?raw=true)
    ![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Search%20By%20Artist.png?raw=true)
    ![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Search%20By%20Genre.png?raw=true)

  * As user, I want a favorite list so I can keep track of musics I like.<br />
      ![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Favorite.png?raw=true)

  * As user, I want to see song information.<br />
![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Info%20List.png?raw=true)

  * As user, I want to see the newly released songs.<br />
![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Newly%20Released%20Songs.png?raw=true)

  * As user, I want to see the most popular songs.<br />
![](https://github.com/CSC-365-Project/Music-Search-Engine/blob/main/img/Top%2010%20of%20Month.png?raw=true)

  
#### Highlights
  * Complex data structure: A more complex structure allowed us to call queries with more freedom and creativity. <br />
  * Real-world data: By using the Spotify API, we were able to insert songs with real-world information into our database. <br />
  * Real-time playback: By using the Spotify API, our application is able to play the selected song directly using embedded URLs or redirect to the song’s webpage (if the song doesn’t support preview listen) <br />

### Contact
Lucas Li - yli76@calpoly.edu<br />
