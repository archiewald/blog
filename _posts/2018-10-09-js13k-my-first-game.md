---
layout: post
title: "1ppm#8 my first js game!"
date: 2018-10-09 18:00:00 +0100
categories: 1ppm
comments: true
---

### [github repo](https://github.com/archiewald/kick-it-js), [the game](http://js13kgames.com/entries/kickit)

After summer holidays I am back motivated to write new posts about projects I do. This time I made my first game and it was hell lot of fun!

I participated in [js13kGames challenge](https://js13kgames.com/). The goal was to sent a working game being a single `.html` file of 13kb.

## Motivation 🚀

I had a pleasure to meet the organizer of the event, [Andrzej Mazur](https://twitter.com/end3r) on a Front Club meetup in Warsaw. He talked about VR games in browser and mentioned this will be a new category to participate in the contest he organizes, [js13kGames challenge](https://js13kgames.com/). I thought it would be a nice challenge to pick up but I had vacations in plans which interfered with time for game coding to meet the deadline...

On the vacation time luckily I read a book which inspired me to go for it.

<div class="img-block">
    <img src="{{ "/assets/2018-09-26/hackers.jpg" | absolute_url }}" alt="hackers">  
</div>

[The Hackers](https://www.goodreads.com/book/show/56829.Hackers) book heavily mentions the video games beginnings with few chapters devoted for history of [Sierra Online video games publisher](https://en.wikipedia.org/wiki/Sierra_Entertainment). It was so fascinating to read about firsts game developers, each being a one-man army, discovering the potential of personal computers like Apple II. That time Sierra company was rather a hub for creative people developing games on their own then a game factory.

Sure, professional game dev industry changed and big budget titles production is nothing like this. But retro/indie games got a strong place on the market. They often run on mobile phones or a web browser. Games can be produced in small teams again. I find it a reminiscent of this crazy Sierra Online beginnings, and within the js13kGames challenge I felt I could be a part of it 💪.

## Tools 🛠

The challenge of making a game using just 13kb of data really limited the possibility to use external libraries/frameworks. And it was an intention from the challenge organizer of course. Anyway, some resources are provided on the js13k website, mostly developed by the previous years competitors themselves. I used:

- [Kontra.js](https://straker.github.io/kontra/) implements basic game requirements like a main loop, keyboard/mouse handling, sprites etc.
- [js13k Webpack Starter](https://github.com/sz-piotr/js13k-webpack-starter) minifies the js and css modular code into single `.html` file

## Code 🤓

I tried to organize my code in objects and it was a great fun itself. Creating a ball, a player, a pitch etc with their properties and then animating them gave me the feel of creator.

```javascript
const player1 = new Player({
  startingPosition: {
    x: 100,
    y: BOARD_SIZE.height / 2 - BALL_RADIUS
  },
  color: "blue",
  speed: 3,
  keys: {
    up: "w",
    down: "s",
    left: "a",
    right: "d"
  },
  radius: PLAYERS_RADIUS
});

const player2 = new Player({
  startingPosition: {
    x: BOARD_SIZE.width - 100,
    y: BOARD_SIZE.height / 2 - BALL_RADIUS
  },
  color: "red",
  speed: 3,
  keys: {
    up: "up",
    down: "down",
    left: "left",
    right: "right"
  },
  radius: PLAYERS_RADIUS
});

const ball = new Ball({
  startingPosition: {
    x: BOARD_SIZE.width / 2 - BALL_RADIUS,
    y: BOARD_SIZE.height / 2 - BALL_RADIUS
  },
  radius: BALL_RADIUS
});

const goals = [
  new Goal({
    position: { x: 0, y: BOARD_SIZE.height / 2 - GOAL_HEIGHT / 2 },
    width: GOAL_WIDTH,
    height: GOAL_HEIGHT,
    color: "blue"
  }),
  new Goal({
    position: {
      x: BOARD_SIZE.width - GOAL_WIDTH,
      y: BOARD_SIZE.height / 2 - GOAL_HEIGHT / 2
    },
    width: GOAL_WIDTH,
    height: GOAL_HEIGHT,
    color: "red"
  })
];
```

And then after initializing all the object there is a main loop of the game. Quite readable I think. The steps are like:

1. Handle movement of the players - check the key input and if nothing blocks them, then move.
1. Check if ball collides with player or board - then calculate the resulting velocity and move.
1. Check if any of the player scored the goal - if so render the score, restart positions, stop the ball etc.

```javascript
const loop = kontra.gameLoop({
  update: () => {
    player1.handleControl([player2.getPosition()], BOARD_SIZE);
    player2.handleControl([player1.getPosition()], BOARD_SIZE);

    ball.handlePlayersCollision([player1, player2]);
    ball.handleBoardCollision(BOARD_SIZE);
    ball.handleMotion();

    const ballPosition = ball.getPosition();

    if (player1scores(ballPosition)) {
      player1.points += 1;
      restartPositions([player1, player2, ball]);
      showPoints();
      ball.stop();
      if (player1.points >= POINTS_TO_WIN) {
        clearPoints();
      }
    }

    //⬆ ️... same goes for the 2nd player TODO: refactor 😅

    sprites.forEach(sprite => sprite.update());
  },

  render: () => {
    sprites.forEach(sprite => sprite.render());
  }
});

loop.start();
```

As you can see writing such simple games in js isn't that hard. You will use lot of vanillaJS to provide games logic which is great to polish your skills.
