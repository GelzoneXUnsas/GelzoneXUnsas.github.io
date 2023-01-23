---
title: "Personal Homepage"
date: 2021-6-2T01:18:59-08:00
draft: false
author: ["Lucas Li", "Kyle Burton", "Moses Epps", "Taylor Morgan", "Joseph Simopoulos"]
tags: ["Projects"]
featured_image: '/images/others/DB.jpg'
description: "The only website you might need"
toc: true
---

We propose a [personal homepage](http://polypage.herokuapp.com/) webapp. It’s similar to a website builder, but it provides fundamental tools like bookmarks, to-do lists, and more. <!--more-->

### Overview
Between multiple devices and browsers, it’s difficult to create a unified experience when browsing the web. An iPhone using Safari can’t sync its bookmarks with Chrome on Windows 10, and manually doing so can be frustrating to users. By setting this user page as their home page, users can access the tools they need on any device as soon as they open a new tab. The stakeholders include the developers and anyone who browses the web on one or more devices.

* **Tile**: A versatile item on a user page. Every tile has a type, data pertaining to that type, size and position data, and background color.

### Design
* We are using a “MERN” stack; this stands for MongoDB, Express.js, React.js, and Node.js. Both the frontend and backend servers run on Node.js. The frontend delivers a React web app, and the backend uses Express.js to provide a REST API. The frontend makes REST API calls to the backend, which performs operations on our cloud-hosted MongoDB database, and sends responses back to the frontend.

* We are using three main external software interfaces.
    - Google Authentication: help ease logging into our site. More specifically, passport-google-oauth2 v2.0.0 has been employed to ensure security and ease of entrance once someone has already signed up. We have implemented sessions using Passport so that the user’s data will be cached and remembered in the current browser instance. Passport is solely used for logging and user authentication. The authentication state is passed down through the user state to each child component, along with other helpful data.

    - Canvas API: get student data, like courses, course grades, calendar data, etc. to create tiles to place around the page so the student has easy access to their frequented information. Instructure has been courteous enough to make their version impossible to find. They do have changelogs though, and we are using the most recent version. We are only going to be getting data from Canvas, not posting any. As of now, we are getting the user profile on Canvas and their Courses, but we have the intention to get a higher variety of data in the future. The data is component-specific. Once pulled, it will exist in its intended tile and only there, for now. So not much data sharing between software complements will be had.

    - Twitch’s API: embed Twitch streams in the Twitch Tile.

    - Draggable.io: A Drag n’ drop javascript library to move the tiles around in edit mode. The library itself is fairly antiquated, but it is much more lightweight than other react drag and drop libraries out there.

#### Data Schemas

* User

    - Every user has a JSON document containing their basic personal information, as well as page customization data. This also contains an array called tiles, which is a one-to-many relationship to the Tile schema.

```Shell
{
  name: {
      type: String,
      required: true,
      trim: true,
  },
  email: {
      type: String,
      required: true,
      trim: true,
  },
  tiles: [TileSchema],
  backgroundColor: {
      type: String,
      required: true,
      default: “#ffffff”,
  },
  backgroundImage: {
      type: String,
      required: false,
      default: "",
  },
}
```

* Tile

    - Every tile specifies its type, position, color, and size. It additionally contains a data object, and a list of List Items, which is a one-to-many relationship. These fields vary from tile to tile, but they contain all the information that any one tile may need. For example, a todo list tile will have a list containing zero or more tasks.

```Shell
{
  tileType: {
      type: String,
      required: true,
      trim: true,
  },
  width: {
      type: Number,
      required: true,
  },
  x: {
      type: Number,
      require: true
  },
  y: {
      type: Number,
      required: true
  },
  data: {
      type: Object,
      required: false
  },
  color: {
      type: Object,
      require: false,
  },
  list: {
      type: [ListItemSchema],
      default: undefined,
      required: false
  }
}
```

* List Item

    - List items are generic objects contained within the list field on Tiles, and include varying data depending on which tile they’re for.

```Shell
{
    type: Object,
    required: false
},
{
    strict: false
}

```

### Tests

* Unit Tests - Junit
    - In UserServices.js, we have 93.37% statement coverage, 78.68% branch coverage, 100% function coverage, and 93.15% line coverage. However, looking closer at the uncovered lines, this is perfectly fine. Every group of uncovered lines comes in a pair and corresponds to a catch block that logs an error. For these methods, we cannot produce any input that produces an error and enters the catch block. Therefore, all these uncovered lines are, in fact, unreachable. That said, we choose to leave them in place as a safety measure.

