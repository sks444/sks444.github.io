---
layout: post
title: "GSoC'18 with coala"
tagline: "Preparation, Selection and The Project Details"
categories: gsoc pinned
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

## The Background

I am a computer science undergraduate at [SMVDU](http://www.smvdu.ac.in/). I am currently in my 3rd year but I have not started coding until the summer vacation of my 2nd year.

During these three months of summer, I managed to learn the fundamentals of following skills:

* [HTML5](https://en.wikipedia.org/wiki/HTML5)
* [CSS3](https://en.wikipedia.org/wiki/Cascading_Style_Sheets)
* [Bootstrap3](http://getbootstrap.com/docs/3.3/)
* [Python](https://www.python.org/)
* [Django](https://www.djangoproject.com/)

I had also started working on a Django based project [socrates](https://socrates.news). But because of the late start, I didn't have much exposer in computer science and was not quite sure about what to do next.

## PyCon India'17

At the beginning of my third year, I went to attend [PyCon India](https://in.pycon.org/2018/). That was really a great start of my career in computer science engineering. I learned a lot of things there, I got to know more about Open Source, GitHub, Git and many more new technologies. I attended all the talks I could attend and also a [coala](https://coala.io) dev-sprint.

## The Prepration

PyCon was really great motivation for me but still because of the late start I needed to do something very quick. So, I thought of preparing for [Google Summer of Code](https://summerofcode.withgoogle.com/)

I have had heard about GSoC many times before but never thought I would be preparing for it. I did some more research about the program and started browsing through the listed [organizations](https://summerofcode.withgoogle.com/organizations/) and decided to go with coala. I found the docs and getting started tutorials really easy for me and very soon I managed to set up the work environment.

The good thing was that when I joined coala, it had started a [new repo](https://github.com/coala/community) based on Django. So, I started contributing to that repo. Initially, It was tough for me to understand many things but thanks to [John](https://github.com/jayvdb)(maintainer at coala) and other fellow reviewers on my PRs, I managed to become the second most active contributor to the community repository. 

![](https://image.ibb.co/jjJtES/random.png")

It took me a total of five months to submit 12 patches but it was a great experience and lots of learning.

## The Result

After a month of constant researching, editing and discussing with John, I had managed to submit a proposal for [Newcomer metrics and Gamification](http://projects.coala.io/#/projects?project=newcomer_metrics_and_gamification&lang=en) project under coala.

And I am really happy to state that I have been selected to work with coala for [GSoC 2018](https://summerofcode.withgoogle.com/).

![](https://image.ibb.co/e6SdW7/gsoc.png)

## The Project: Abstract

As coala tries to be welcoming organization for newcomers, giving them clear pathways and as much direct assistance as possible. However, it is already known that newcomers face many barriers while attempting to contribute to the open source community for the first time. Some barriers they face include orientation issues that can potentially demotivate newcomers from placing their first contribution. On the other hand, gamification is widely used to engage and motivate people to accomplish task and improve their performance.Therefore, the goal of this project is to use gamification to orient and motivate newcomers to overcome onboarding barriers to contribute and engage with coala and its community.


## The Project: Detail Idea

As the goal of this project is to make use of metrics and gamification to motivate and support newcomers to overcome the orientation barriers to contribute to the community.
To achieve this goal we have selected three game design elements to help newcomers: Levels, Points, and Badges. For each of these elements, we will have a set of rules to describe the operation of the gamified environment. i.e.:

- **Levels** group different tasks, enabling users to unlock specific and contextualize set of actions. Levels will be applied to make newcomers focus on a given step before moving ahead on the contribution process. E.g.: We will divide our newcomer process into three levels and each level will contain three tasks:
  - Level-I:
    - Joining our gitter channel and the community
    - Running CI tools to your fork
    - Running coala-ci on a popular github repo
  - Level-II:
    - Getting assigned to a difficulty/newcomer issue
    - Creating and merging PR for that issue
    - Reviewing at least one difficulty/newcomer issue
  - Level-III:
    - Getting assigned to a difficulty/low issue
    - Creating and merging a PR for that issue
    - Reviewing at least one difficulty/low issue

- **Points** will be the most applied element out of three game design elements. Acquiring points means that the actions performed by the users are in accordance with what was expected. Points can be earned by means of performing actions to the different tasks under levels. E.g.: If a user wants to go to next level he will have to collect a certain number of points from doing the tasks under levels. But in case of Level-II and Level-III if a user is not able to acquire the required number of points after performing all the tasks, he will have to review more PRs to collect the required points. 

- **Badges** enable newcomers to follow their performance and compare to other newcomers. After completing the level three successfully the newcomer will be a developer. And he will be awarded a badge based on their performance in each level and the total number of points.

## Whats Next

I am looking forward to have a great summer working with coala and my project mentor's [John](https://github.com/jayvdb), [Pranav](https://github.com/prnvdixit), [Andrew](https://github.com/andrewda) and [Gopala](https://github.com/gkrishnan724).

I will also write a blog post on weekly basis with the updates on my work expreience and progress. That's all folks, stay tuned. 
