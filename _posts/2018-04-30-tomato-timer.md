---
layout: post
title:  "1ppm#4 Tomato timer using React + Redux"
date:   2018-04-30 8:00:00 +0100
categories: 1ppm
comments: true
---

See the project [github repo](https://github.com/archiewald/tomato-timer).

This time I show you my first project where I introduce `Redux` library. As you might know,
Redux is a state management library for JavaScript applications, mainly adopted by `React` community - so much that Facebook as creator of React decided to hire Dan Abramov - Redux creator.

If you would like to get some solid basics on Redux core concepts, I totally recommend to learn with our prophet Dan himself, as he launched very nice video `tutorial on egghead`, available [here](https://egghead.io/courses/getting-started-with-redux). It is not devoted for complete beginners, Dan explains quite deeply his motivation behind Redux library and how it works - in fact in this course you mostly write it yourself to get best understanding.

So the Redux itself is not obligatory fot every React project. I think the best approach if you not sure if it fits your needs is to start with default React way - using local state and one-way data binding. As project get more complex you will get a feeling that it is not enough and some global state would be a blessing. But! Global state is dangerous - and here where Redux comes with help.

More on that on [Dan Abramov blog](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)

Ok, let have a look on a project itself!

<iframe src="https://archiewald.github.io/tomato-timer/" height='450px' width='480px'></iframe>

To start with, have a closer look on `actions` and `reducers`. What I like about Redux is writing them provide you very nice, even self-explanatory description of how you app works in global context.

`actions/index.js`
{% highlight js %}
export const Modes = {
    POMODORO: {
        name: "POMODORO",
        time: 20,
    },
    SHORT_BREAK: {
        name: "SHORT_BREAK",
        time: 5,
    },
    LONG_BREAK: {
        name: "LONG_BREAK",
        time: 10,
    }
}

export function setMode(mode){
    return {type: "SET_MODE", mode}
}

export function startTimer(baseTime = 0){
    return {
        type: "START_TIMER",
        baseTime: baseTime,
        now: new Date().getTime()
    }
}

export function pauseTimer(){
    return {
        type: "PAUSE_TIMER",
        now: new Date().getTime()
    }
}

export function resetTimer(){
    return {
        type: "RESET_TIMER",
        now: new Date().getTime()
    }
}
{% endhighlight %}

`reducers/index.js`
{% highlight js %}
import { combineReducers } from 'redux'
import {Modes} from '../actions/index'

const {POMODORO} = Modes;

function mode(state = {POMODORO}, action) {
    switch (action.type) {
        case "SET_MODE": {
            return action.mode
        }
        default: {
            return state
        }
    }
}

function control(state = {
    startedAt: undefined,
    stoppedAt: undefined,
    baseTime: undefined,
}, action) {
    switch (action.type) {
        case "START_TIMER": 
            return {
                ...state,
                baseTime: action.baseTime,
                startedAt: action.now,
                stoppedAt: undefined
            };
        case "PAUSE_TIMER":
            return {
                ...state,
                stoppedAt: action.now
            };
        case "RESET_TIMER":
            return {
                ...state,
                baseTime: 0,
                startedAt: state.startedAt ? action.now : undefined,
                stoppedAt: state.stoppedAt ? action.now : undefined
            };
        default:
            return state;
    }
}

const tomatoTimerApp = combineReducers({
    mode,
    control,
})

export default tomatoTimerApp;

{% endhighlight %}

In the main class `App.js` you can see main components, including container components. It is recommended approach to separate UI and state update this way, you can read more about it [here](https://redux.js.org/basics/usage-with-react#designing-component-hierarchy)

{% highlight js %}
class App extends Component {
  render() {
    return (
      <div>
        <TopBar/>
        <ModePanelContainer />
        <ClockContainer/>
        <ControlPanelContainer/>
      </div>
    );
  }
}
{% endhighlight %}

So for instance `ClockContainer` is only responsible to map store actions and props to`Clock` component. Then `Clock` component contains all the logic behind creating timer itself and its update each second. It shares then this info with two Timers - the main one and title one - which you can see on page title if you open it in separate window [archiewald.github.io/tomato-timer/](https://archiewald.github.io/tomato-timer/).

If you are more interested in Clock logic just visit github repo [src/components/Clock.jsx](https://github.com/archiewald/tomato-timer/blob/master/src/components/Clock.jsx).

Some libraries I used for this project:

* [moment.js](https://momentjs.com/) very helpful when it comes to handling time in JavaScript,
* [react-helmet](https://github.com/nfl/react-helmet) helped me to render page title each second without much pain,
* [react-sound](https://github.com/leoasis/react-sound) provided me nice Sound component to play when timer ends.

PS. Honestly - I would say this project is not a best example to introduce Redux, with this very simple UI I would definetely be comfortable without it. Anyway, everyone need to start somewhere and non-profit learning projects tend to be simple :) I am glad that I made it this way but keep in mind this app might be done lot easier way.



