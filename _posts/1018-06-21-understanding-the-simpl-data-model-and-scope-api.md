---
layout: post
title: "Understanding the Simpl Data Model and Scope API"
date: 2018-06-21 14:38:00 -0500
categories: News
author: Jane Eisenstein
excerpt: |
    A high-level explanation of the Simpl data model and Scope API
---

Background

The Simpl framework was developed by the Alfred West Jr. Learning Lab at the Wharton School of the University of Pennsylvania. The Learning Lab’s mission is to improve the way Wharton students learn by engaging them with cutting edge digital learning tools (usually gamified simulations) developed in partnership with Wharton faculty.

At Wharton, games are run as part of a class or other event. A group of students plays a game by entering decisions for one or more simulated time periods over minutes, hours or days of real time. The students then participate in a debrief session with their teacher during which they cover what can be learned from the game results.

In some games, players are grouped into teams to either collaborate or compete with other team members. These are multi-player games. In other games, player decisions and results are independent of each other. These are single-player games.

Model Classes

The Simpl data model was designed to support developing such games with as much flexibility as possible. To this end, the model classes all have a data property for storing JSON data unique to a specific game.

A Game encapsulates all the functionality of a simulation. Each Game defines simulation model logic and provides a web-based user interface. 

A Run is an instance of a Game created to be used in a class or other event. The state and data in one Run has no bearing on other Runs. 

A Simpl User is Django user identified by email address with some additional fields. Users can participate in several games by being assigned to Runs of the Games.

A RunUser represents the one to one relationship between a game Run and a User. Each RunUser represents either a leader or a player in the run. Each Run may have one or more leader RunUsers and one or more player RunUsers.

A Game can define one or more Run Phases that determine the features available as a Run moves from one phase to the next. Simple Games may need only one Phase (e.g. Play), while more complex Games may require several (e.g. Setup, Players Prepare, Play and Debrief).

A Game can define one or more player Roles. A player’s role might determine the background information they see and the types of decisions they can make. Games in which all players have exactly the same capabilities do not need to define player Roles.

A World groups RunUsers into a team whose combined decisions are used to step the Game’s simulation model. Each World's decisions and results are independent of other World's decisions and results. 

A Decision is a set of inputs for stepping the simulation model. One or more Decisions may be needed to step the simulation model. Decisions may be assigned to specific player Roles.

A Result is a set of outputs from stepping the simulation model. Results may be assigned to specific player Roles.

A Period represents inputs and outputs for a step of the simulation model.

A Scenario encapsulates a set of Decisions and Results possibly over several Periods for a RunUser or for a World.

Simpl Scope API 

Simpl applications need to restrict the amount of game data loaded into the browser. This is done by filtering out data unrelated to the currently logged in user. When a user logs into the game frontend, a query is run that determines their RunUsers within the game. The query results are used to load appropriate game data into the browser by the simpl-react Javascript package.

By default, leaders have access to all data for their Runs. Players in single-player games typically only have access to data associated with their Scenarios. Players in multi-player games typically only access data associated with their World within a Run. However, the API also supports multi-player game users having private Scenarios used to test out strategies prior to committing final decisions.  

The Simpl Scope API provided by the simpl-modelservice Python package supports loading only data from Runs with which the user has a RunUser relationship. The relationships of the Scope classes form a directed acyclic graph (see below) that can be traversed using the parent/child access functions of the API.
