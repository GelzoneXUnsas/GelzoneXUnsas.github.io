---
title: "2D Spaceship Shooter"
date: 2020-11-10T01:18:59-08:00
draft: false
author: ["Lucas Li"]
tags: ["Games"]
featured_image: '/images/games/plane_shooter_cover.jpg'
description: "A plane shooter game created with Java"
toc: true
---

One of my first games made during college. I had the opportunity to work on a 2D plane shooter game using Java with PApplet. It was an incredibly rewarding experience to work with my peers and the professor to bring this project to life. <!--more-->

---

![Game Preview](/images/games/plane_shooter.gif)

---
### Gameplay
The game takes place from a third-person perspective. The player's goal is to use the curser keys to shoot all the enemy spaceships without getting hit by their ammo or the astroids.

### Technical Details
#### Pathing Strategy

A pathing strategy refers to the method used by a computer program to determine the best path for an object to follow in a virtual environment. 

```Java
interface PathingStrategy
{
   /*
    * Returns a prefix of a path from the start point to a point within reach
    * of the end point.  This path is only valid ("clear") when returned, but
    * may be invalidated by movement of other entities.
    *
    * The prefix includes neither the start point nor the end point.
    */
   List<Point> computePath(Point start, Point end,
      Predicate<Point> canPassThrough,
      BiPredicate<Point, Point> withinReach,
      Function<Point, Stream<Point>> potentialNeighbors);




   static final Function<Point, Stream<Point>> CARDINAL_NEIGHBORS =
        point ->
            Stream.<Point>builder()
                .add(new Point(point.x, point.y - 1))
                .add...
                .build();

   static final Function<Point, Stream<Point>> DIAGONAL_NEIGHBORS =
        point ->
            Stream.<Point>builder()
                    .add(new Point(point.x - 1, point.y - 1))
                    .add...
                    .build();



   static final Function<Point, Stream<Point>> DIAGONAL_CARDINAL_NEIGHBORS =
        point ->
            Stream.<Point>builder()
                    .add(new Point(point.x - 1, point.y - 1))
                    .add...
                    .build();
}
```

In this game,  2 kinds of enemy ships that move to the hero ship are using A* and DFS strategies.

![Enemy Preview](/images/games/E1.png) ![Enemy Preview](/images/games/E2.png)

* A* (A-star) is an algorithm used for pathfinding and graph traversal. It is an extension of the popular Dijkstra's algorithm, which is used to find the shortest path between two nodes in a graph. A* adds a heuristic component to Dijkstra's algorithm, which allows it to find the shortest path more efficiently.

* DFS (Depth First Search) is a type of algorithm used for traversing and searching a graph or a tree data structure. I decide to implement this strategy only for testing purposes for **Object-Oriented Design**, and **Factory Design Pattern**.

#### Object-Oriented Design

![Enemy Preview](/images/games/OO_design.jpg)

I implement OO design based on the concept of objects and their interactions with each other. Abstract classes and interfaces are created for more connected and streamlined code, allowing for less repetition.

One of the key principles of OO design is encapsulation, which means that the internal state of an object is hidden from the outside world and can only be accessed or modified through the object's methods. This helps to maintain the integrity of the data and provides a way to change the implementation without affecting the rest of the code.

#### Factory Design Pattern

![Enemy Preview](/images/games/Factory_Design.jpg)

The Factory Design Pattern is an object-oriented design pattern that is used to create objects without specifying the exact class of object that will be created. Instead, a factory class is responsible for creating objects of a specific type, based on information or input provided to the factory.

```Java
public class PathingFactory {
    public PathingStrategy createStrategy(String key){
        PathingStrategy p = null;
        if(key.equals("enemyshipPurple")){
            p = new AStarPathingStrategy();
        }
        else if(key.equals("enemyshipGreen")){
            p = new DFSStrategy();
        }
        else if(key.equals("missile")){
            p = new SingleStepPathingStrategy();
        }
        return p;
    }
}

```

As an example, using a pathing strategies factory, I'm able to assign different **PathingStrategies** to each entities in the game.

* ![Enemy Preview](/images/games/E2.png) --> A Star
* ![Enemy Preview](/images/games/E1.png) --> DFS Pathing
* ![Ammo Preview](/images/games/MB2.png) --> Single Pathing

The Factory Design Pattern is a powerful tool for creating objects in a flexible, maintainable, and extensible way, while adhering to important principles of object-oriented design.