---
layout: post
title: "1ppm#9 my first hackathon!"
description: "Thoughts after joining hackathon for the first time"
date: 2018-11-12 18:00:00 +0100
categories: 1ppm
comments: true
image: /assets/2018-11-12/team2.jpg
---

### [github repo](https://github.com/pingwing/DaneDaneDane), [project mocks](https://xd.adobe.com/view/c37a07e8-b84a-4309-5964-fc9753b30264-b54b/?fullscreen) `password: Monster1`

I want to share with you my thoughts after joining hackathon for the first time. It was a super rich experience I think every developer should try.

## The Event 💻

[Hackathon Nowa strona danych](https://hackathon.gov.pl/) held by polish Ministry of Digitization and organized in a nice working space in Warsaw. The goal was to propose a solution to increase the usability of [dane.gov.pl](https://dane.gov.pl/) portal which holds data from over 100 public institutions.

## The Team 👨‍👩‍👧‍👦

Starting from the left:

- Kasia, UX Developer & Designer,
- Ewa, Project Lead,
- Przemek, JavaScript Developer,
- myself

<div class="img-block">
    <img src="{{ "/assets/2018-11-12/team.jpg" | absolute_url }}" alt="team">
    <p class="caption">Dane Dane Dane team - <a href="https://agnieszkawanat.com/">Agnieszka Wanat</a></p>
</div>

Thank you guys for the time we shared there. I really like brainstorming with you on the idea and feel everyone put a valuable part in the project.

## The Project 💡

After going through all the data already uploaded on the portal we decided the main problem is the quality of the data there. There are multiple data formats, mostly excel files, and these are variously formatted as well.

We decided to create a tool which helps providers to upload data which then is convertible to more machine-friendly formats like CSV or JSON. Shortly, the process goes as below:

1. Choose the type of data you want to upload. Categories, value types, is it a time domain dat etc.
1. The app creates excel template available to download.
1. Fill the template with data, upload.
1. The app validates provided data.
1. If ok, JSON and csv is generated from data, available to be downloaded and easy to read by machine.

Lot of stuff going on... But we managed to provide a beautiful [interactive mocks](https://xd.adobe.com/view/c37a07e8-b84a-4309-5964-fc9753b30264-b54b/?fullscreen) (big kudos for Kasia ️❤️) and create a simple demo app which processed exemplary data set. Nice job! (You can access mocks providing password: `Monster1` )

<div class="img-block">
    <img src="{{ "/assets/2018-11-12/team2.jpg" | absolute_url }}" alt="team 2">
    <p class="caption">working hard - <a href="https://agnieszkawanat.com/">Agnieszka Wanat</a></p>
</div>

## My thoughts 🔍

- Think about your goal. A final presentation is absolutely crucial on a hackathon, so whatever you do, make sure it will make it richer. Working on a feature you will not present makes no sense!

- Focus to provide a core functionality. I spent too much time working on layout, while there was simply no time for that. Work on the core of your project, just do really rough styling, or none at all, you can polish it if you will have some time left (probably not).

- Use tools you are comfortable with. Learning a new library on fly on 24h hackathon didn't work for me. If you want to deliver a working demo in such short period of time, your flow must be undisturbed. Easier said then done, for example I wanted to use [material-ui](https://material-ui.com/) really bad, even though I wasn't really fluent with this library. I know bootstrap quite well on the other hand, so I should made it my first choice.

- There are some great tools to build interactive mocks like Adobe XD, Balsamic, Zepplin etc. You can use it to present a user interface of your app and then do a rough demo to prove you are capable to do it. It's ok, most teams on the hackathon presented more/less this way.

<div class="img-block">
    <img src="{{ "/assets/2018-11-12/archie.jpg" | absolute_url }}" alt="team">
    <p class="caption">watching youtube cats - <a href="https://agnieszkawanat.com/">Agnieszka Wanat</a></p>
</div>
