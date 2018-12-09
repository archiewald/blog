---
layout: post
title: "1ppm#10 Angular tests on Jenkins server"
description: "How to set up Angular tests on Jenkins server using headless Chrome"
date: 2018-12-10 18:00:00 +0100
categories: 1ppm
comments: true
---

### [github repo](https://github.com/archiewald/angular-jenkins-ci)

Last months I spent some time learning Angular framework. I like React and I presume it will stay as my first-choice tool to build frontend, but I think it is important for every frontend developer to know both:

- you can learn from each and use patterns more popular in Angular in your React stack for example,
- both React and Angular are extremely popular, chances you will have to work with each of them some day are high.

Apart from learning Angular itself I wanted to dive more into writing tests and what one could name "frontend devops". The goal was to automatize stuff like linting, running tests, deploying. I realized that it is preferable to move such tasks into continuous integration environment.

Thanks to that we can make our developer experience better - we don't need to run these possibly heavy and long processes on local machines. They are more predictable since we run it each time on an environment of our choice (meaning OS, computing resources etc). As a CI environment I chose Jenkins for my experiments since this is the tool we use in codequest. Part of it I want to describe in below simple tutorial.

How to create Angular starter app and configure it so we can run tests on Jenkins? Let's learn!
I will assume you already have a Jenkins working instance and you did set it up to be triggered from your version control service. This article covers the part of configuring Angular and declaring a Pipeline.

<div class="img-block">
    <img src="{{ "/assets/2018-12-10/blueocean.png" | absolute_url }}" alt="blue_ocean">
    <p class="caption">A visual representation of a Pipeline in Jenkins Blue Ocean</p>
</div>

## create angular starter app 🚀

First install angular cli globally and create a new app:

```
npm install -g @angular/cli
ng new angular-jenkins
cd angular-jenkins
```

New angular project comes with unit and e2e tests already set up! Some basic "hello world" tests are provided and ready to run (note the output below is simplified):

```
npm run test

> Chrome 71.0.3578 (Mac OS X 10.14.0): Executed 3 of 3 SUCCESS (0.281 secs / 0.255 secs)
> TOTAL: 3 SUCCESS

npm run e2e

> Jasmine started
>
> workspace-project App
> ✓ should display welcome message

```

## configure tests to run on Chrome Headless 🤖

It is convenient to run tests on headless browser version since we simply don't need any interface to be available in CI process. Modify karma configuration file by adding following:

```
browsers: ["Chrome", "HeadlessChrome"],
customLaunchers: {
    HeadlessChrome: {
    base: "ChromeHeadless",
    flags: ["--no-sandbox"]
    }
},
```

Now we can decide if we want to run unit tests by adding a flag in a script in `package.json`. We make sure to not run a CI version in watch mode by adding a proper flag.

```
"test": "ng test --browsers=Chrome ",
"test:ci": "ng test --browsers=HeadlessChrome --watch=false",
```

How about e2e tests? The way I made it was not really pleasant 🙈. Can you suggest a better one? Please, comment!

First, copy-paste `protractor.conf.js` and name as `protractor.ci.conf.js`. In the exported config object modify `capabilities` key:

```
capabilities: {
    browserName: "chrome",
    chromeOptions: {
        args: [
            "--headless",
            "--disable-gpu",
            "--window-size=800x600",
            "--no-sandbox",
        ],
    },
},
```

Then I can set a new configuration file in a script in `package.json` for `e2e:ci`:

```
"e2e": "ng e2e",
"e2e:ci": "ng e2e --protractorConfig=e2e/protractor.ci.conf.js"
```

You can test if these CI variant scripts work in your local environment, they did on my macOS 🙂

## create a JenkinsFile ♻️

Let's create a very basic JenkinsFile with a Pipeline responsible for running `npm install` and our special CI test scripts variants.

For an image I use [angular-chrome-headless](https://github.com/avatsaev/anguar-chrome-headless-docker) with Chrome Headless installed and pre-configured to work with Angular unit/e2e tests.

Create a file named `JenkinsFile` with pipeline as below:

```
pipeline {
    agent {
        docker {
            image 'avatsaev/angular-chrome-headless'
            args '-u 0:0 --entrypoint=""' // set user to root
        }
    }
    stages {
        stage('npm install') {
            steps {
                sh "npm install"
            }
        }
        stage('Unit Tests') {
          steps {
              sh "npm run test:ci"
          }
        }
        stage('e2e tests') {
          steps {
              sh "npm run e2e:ci"
          }
        }
    }
}
```

That's it! Jenkins should successfully build such project and run the tests.
