---
layout: post
title:  "1ppm#2 simple battleship game in MVC model"
date:   2018-03-03 13:00:00 +0100
categories: 1ppm
comments: true
---

See the project [github repo](https://github.com/archiewald/warsawjs-16).

As a second project within 1 Project per Month challenge I decided to be bit lazy and present a simple game made on WarsawJS event.

# About WarsawJS

WarsawJS is a group of geeks doing meetups mainly (not only) in Warsaw. You can check out the meetups speeches recorded [here](https://warsawjs.com/), follow on [facebook](https://www.facebook.com/warsawjs/) and on twitter [@WarsawJS](https://twitter.com/warsawjs).

A great thing is they also do workshops! It is a great opportunity for newbie developers to learn from JS passionates. So I attended one, [WarsawsJS Workshop #16](https://www.meetup.com/pl-PL/WarsawJS/events/246649523/) on 21th of January.

We were devided in 3 groups depending on our programming experience and started to code with some help of the tutors.

![Warsaw js shot]({{ "/assets/2018-03-03/1.jpg" | absolute_url }}) _Me getting some attention from workshops tutors_ :)

Worth to say that after the event I spent some time to play around and add ships marking functionality. I totally recommend anyone who participate in such event to do so, it gave me much better understanding on the topic of MVC and what we actually did before :)

# About the project

The goal was to make a simple battleship game in pure javascript using Model View Controller (MVC) pattern. We don't use any framework or webpack so there is just single .js file.MVC is about separating application in 3 parts, each having specified tasks to do:

* __Model__ here we include application logic, we define our data and how certain events affects it.

* __View__ it defines user interface. We can initialise it here and describe necessery methods to it can change.

* __Controller__ here we define how our model will be affected by input from user.

* __Observer__ you can call it an extra 4th component in this pattern. Here we define how events taking place in model will affect our view.

You can cartainly find some broader and more detailed definition in the web :). See below implementation of this pattern in the code of battleship game and the game itself :)

<iframe src="https://archiewald.github.io/warsawjs-16/" height='550px' width='480px'></iframe>

# View

What happens here? You can see that we define `ViewComponent` abstract class so both `GameCell` and `GameBoard` classes can share `getElement()`. 

{% highlight js %}
class ViewComponent {
    constructor() {
        if (new.target === ViewComponent) {
            // check if constructor was not used directly
            throw new Error('Abstract class!'); 
        }  
    }
    getElement() {
        return this._element;
    }
}
{% endhighlight %}

In `GameCell` and `GameBoard` we use constructors to draw board in initial state and `setState()` and other methods which defines how the view will change - basically we just switch CSS classes.

{% highlight js %}
class GameCell extends ViewComponent {
    constructor(handleCellClick, row, column) {
        // constructor code runs on the creation of object
        super(); // run cunstructor of parent class
        this._state = 'unknown'; // underscore means 'private', not accessible outside of class
        this._element = document.createElement('td');
        const self = this;
        this._element.addEventListener('click', function() {
            handleCellClick(row,column);
        });
    }

    setState(state) {
        if (state !== 'unknown' && state !== 'miss' && state !== 'hit' && state !== 'mark'){
            throw new Error('Invalid state!')
        }
        this._state = state;
        this._element.className = 'cell_' + state;
    }
}

class GameBoard extends ViewComponent {
    constructor(handleCellClick) {
        const _boardSize = 10;
        super();
        this._state = 'unknown';
        this._element = document.createElement('table');
        const self = this;
        this.cells = {};
        for (let i = 0; i < _boardSize; i++){
            const row = document.createElement('tr');
            for (let j = 0; j < _boardSize; j++) {
                const key = 'x'+ i + 'y' + j;
                this.cells[key] = new GameCell(handleCellClick, i, j);
                row.appendChild(this.cells[key].getElement());
            }
            this._element.appendChild(row);
        }

    }

    setStateAt(row, column, state) {
        const key = 'x' + row + 'y' + column;
        this.cells[key].setState(state);
    }

    cleanBoard() {
        Object.keys(this.cells).forEach(key => {
            this.cells[key].setState('unknown');
        })
    }

{% endhighlight %}

# Controller

Since the only kind of input user can do is clicking on cells `GameController` class is rather simple. Depending on game mode we will launch `markShip()` or `fireAt()` methods on our model.

{%  highlight js %}
class GameController {
    constructor(model) {
        this._model = model;
    }
    handleCellClick(row, column) {
        console.log('handle click ' + row + ' ' + column);
        if (this._model.getMode() === 'markShips'){
            this._model.markShip(row, column);
        } else  this._model.fireAt(row, column);
    }
}
{% endhighlight %}

# Model

And here we go to the `GameModel` class where the game logic is implemented. As you can see for each action such as `fireAt()`, besides updating the model, we register an observer or more so then whatever occured in the model can be rendered in the view.

{%  highlight js %}
class GameModel {
    constructor() {
        const _boardSize = 10;
        this._cells = {};
        this._observers = [];
        this._score = 0;
        this._markedShips = 0;
        this._shipsNumber = 12;
        this._mode = 'markShips';
        for (let i = 0; i < _boardSize; i++){
            for (let j = 0; j < _boardSize; j++) {
                const key = 'x'+ i + 'y' + j;
                this._cells[key] = {
                    hasShip: false,
                    firedAt: false
                }
            }
        }
    }

    getMode() {
        return this._mode;
    }

    getShipsNumber() {
        return this._shipsNumber;
    }

    markShip(row, column) {
        const coordinatesKey = 'x'+ row + 'y' + column;
        const targetCell = this._cells[coordinatesKey]; 
        if (targetCell.hasShip){
            console.log('This one is already marked')
            return;
        }
        targetCell.hasShip = true;
        this._markedShips += 1;
        const markedShips = this._markedShips;
        console.log('You marked ship of coordinates ' + coordinatesKey);
        this._observers.forEach(function(observer) {
            observer('shipMarked', {row, column, markedShips})
        });
        if (this._markedShips === this._shipsNumber) {
            this._mode = 'fireShips';
            this._observers.forEach(function(observer) {
                observer('allShipsMarked');
            }, this);
        }
    }

    fireAt(row, column) {
        const coordinatesKey = 'x'+ row + 'y' + column;
        const targetCell = this._cells[coordinatesKey]; 
        if (targetCell.firedAt){
            return;
        }
        targetCell.firedAt=true;
        const result = targetCell.hasShip ? 'hit' : 'miss';
        if (result === 'hit') {
            this._score += 1;
            if (this._score === this._shipsNumber){
                this._observers.forEach(function(observer) {
                    observer('win')
                });
            }
        }
        console.log("cell coordinates " + coordinatesKey + ' was shot!');
        this._observers.forEach(function(observer) {
            observer('firedAt', {result, row, column})
        });
        if (result === 'hit') {
            this._observers.forEach(function(observer) {
                observer('scored', {score: this._score});
            }, this);
        }
    }

    addObserver(observerFunction) {
        this._observers.push(observerFunction);
    }
}
{% endhighlight %}

# App initialization

Here you can see how components aboved are used to create a game and what are the relations between them. Important part is to add observers to the model - here in the `switch` instruction you can how the view gets updated by events happened in the model.

{%  highlight js %}

const game = document.getElementById('game');
let board;
let controller;
let model;

function handleCellClick(row, column) {
    controller.handleCellClick(row, column);
}

board = new GameBoard(handleCellClick);
model = new GameModel();
const counter = new GameCounter();

counter.setOutOfValue('/' + model.getShipsNumber());
model.addObserver(function(eventType, params) {
    switch (eventType) {
        case 'firedAt' :
            board.setStateAt(params.row, params.column, params.result);
            break;
        case 'shipMarked' :
            board.setStateAt(params.row, params.column, 'mark');
            counter.setValue(params.markedShips);
            break;
        case 'allShipsMarked' :
            board.cleanBoard();
            counter.setStatus('Shoot the ships! ')
            counter.setValue('0')
            break;
        case 'scored' :
            counter.setValue(params.score);
            break;
        case 'win' :
            counter.setWinStatus();
            break;
    }
})

controller = new GameController(model);
game.appendChild(board.getElement());
game.appendChild(counter.getElement());

{% endhighlight %}