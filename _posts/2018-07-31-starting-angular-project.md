---
layout: post
title:  "1ppm#7 starting Angular Project - deploy to AWS, linting, prettying"
date:   2018-07-31 20:30:00 +0100
categories: 1ppm
comments: true
---

# [github repo](https://github.com/archiewald/angular-s3)

Good day! In this post I will sum up few ideas you can use to make a process of developing and deploying Angular app smooth. They should be applicable to any other frontend project as well e.g. React or Vue one.

## Deploy on AWS

I choose AWS as a service to host my application. Using S3 buckets with CloudFront service support is a great way to provide static web pages to the user. Shortly saying, you can imagine S3 buckets as a place to store files similar to Dropbox or GoogleDrive. Cloudfront service is a Content Delivery Network - CDN - and can propagate you application on fast and reliable network so you can be sure the content will be well available all around the world. 

> Linking Cloudfront to Amazon S3 bucket with proper configuration is one of the best way in terms of quality/price to host static webpage.

After build process my Angular app becomes basically an `index.html` with bunch of javascript files. It is ready then to be served on AWS.

If you would like to host your app and you had no experience with AWS services before, I advise to have a look on below post:

[Deploy an Angular with S3 and CloudFront](https://medium.com/@peatiscoding/here-is-how-easy-it-is-to-deploy-an-angular-spa-single-page-app-as-a-static-website-using-s3-and-6aa446db38ef)

What was important for me was to get a TypeScript script which will automatically build and deploy from my local machine.

To run `.ts` deploy script from node you need to install `ts-node` package from npm and add following positions in your `package.json`:

```json
{
  "name": "angular-s3",
  ...
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "lint:scripts": "tslint --project . 'scripts/**/*.ts'",
    "e2e": "ng e2e",
    // new deploy scripts:
    "deploy": "ng build && ts-node scripts/deploy.ts",
    "deploy-no-build": "ts-node scripts/deploy.ts"
  },
  ...
}
```

Here you can have a look on the script in my project: [scripts/deploy.ts](https://github.com/archiewald/angular-s3/blob/master/scripts/deploy.ts)

I am aware that it can be few lines written as a bash script with usage of `aws-cli`. You might take it into consideration and decide by yourself ;)

## Set up Editor Config and Prettier

To get the best developer experience I like to configure [Prettier](https://prettier.io/) in my project. It is an opinionated code formatter - meaning you cannot configure everything, though there are some options available. Prettier will make sure all the code in the project is formatted in consistent way. Thanks to available plugins you can tell your IDE to format your code each time it is saved. I love it since it automates my work and let me focus on programming itself.

It works well together with [Editor Config](https://editorconfig.org/), which tells the editor what are the indentation rules so they are set it the moment the file is created.

## Set up tslint

[tslint](https://palantir.github.io/tslint/) is another great tool to improve your TypeScript code quality. It is much more than just code formatting - it can check if your code fits some best practices set up in config file. As said, I prefer Prettier to deal with formatting, meaning this part of tslint I would rather switch off.

Rather than switching off manually formatting rules, I recommend to install [tslint-config-prettier](https://www.npmjs.com/package/tslint-config-prettier) which will do the job for you.


To make sure your code will stay consistent when committed to your repository it is recommended to set 'pre-commit hooks'. [Husky](https://github.com/typicode/husky) is an easy-to-use tool for this purpose.