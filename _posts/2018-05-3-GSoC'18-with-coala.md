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

I am a computer science undergraduate at [SMVDU](http://www.smvdu.ac.in/). I am currently in my 3rd year but I had not started coding until the summer vacation of my 2nd year.

During these three months of summer, I managed to learn the fundamentals of following skills:

* [HTML5](https://en.wikipedia.org/wiki/HTML5)
* [CSS3](https://en.wikipedia.org/wiki/Cascading_Style_Sheets)
* [Bootstrap3](http://getbootstrap.com/docs/3.3/)
* [Python](https://www.python.org/)
* [Django](https://www.djangoproject.com/)

I had also started working on a Django based project [socrates](https://socrates.news). But because of the late start, I didn't have much exposer in computer science field and was not quite sure about what to do next.

## PyCon India'17

At the beginning of my third year, I went to attend [PyCon India](https://in.pycon.org/2018/). That was really a great start to my career in computer science engineering. I learned a lot of things there, I got to know more about Open Source, GitHub, Git and many more new technologies. I attended all the talks I could attend and also a [coala](https://coala.io) dev-sprint which went really great, and that is where I first heard about this awesome organization which provides easy linting and fixing of our badly written codes, in all the languages.

## The Decision

PyCon was really a great motivation for me, but still, because of the late start, I needed to choose a path and stick to it until I get the success. I didn't have a clue which path would be the best for me or I can say I didn't even know how many paths do I have to choose from. But the good thing was I have some awesome seniors at my uni who always talk about contributing to Open Source and preparing for [Google Summer of Code](https://summerofcode.withgoogle.com/). In fact, some of them has already done their projects with GSoC and some of them are even mentors now. So, the one and only path I can think of at my stage is contributing to Open Source and preparing for GSoC.

I started browsing through GSoC website, read all the blogs to get to know more about it, and then I choose some organizations to contribute to based on my skills. I started with getting started tutorials of the shortlisted organizations but I was still a newbie, so, I was having lots of problems with the starting phase.

Thanks to awesome [newcomer docs](http://api.coala.io/en/latest/Developers/Newcomers_Guide.html) at coala for helping, in setting up the work environment with almost no problems and learning more about the work process of the organization.

As I had started working with coala, I got myself assigned to my first newcomer issue with their awesome bot [corobo](https://github.com/coala/corobo), which was an easy fix and I managed to solve it within 5 to 10 minutes which got approved and merged at the master branch in a day.

It was an awesome feeling to live with when I placed my first contribution to Open Source.
But I had to get going with the same pace if I have to get more out of working on Open Source projects.

So, with the constant and continuous working of five months and with the help of my great mentor John and other fellow reviewers on my PRs. I managed to become the second most active contributor to one of the coala's new [repository](https://github.com/coala/community) which is a community website based on static Django. 

![](https://image.ibb.co/jjJtES/random.png)

## The Proposal

My learning and contributions curve with coala was growing exponentially and in the meantime, GSoC had also announced the list of selected organizations in which coala was one of them, we were pretty sure that coala will be shortlisted but you can never predict Google results.

As I was browsing through coala's [projects](http://projects.coala.io/) list, I asked John that if we have a project based on our community repo and he suggested [Newcomer metrics and Gamification](http://projects.coala.io/#/projects?project=newcomer_metrics_and_gamification&lang=en) project, I did my initial research on the project and found it very interesting, the work was to implement a gamification system for newcomer to developer process at coala.

So, I did some more research for a month and discussions with John and managed to submit a proposal before Students applications deadline.

### The Abstract

As coala tries to be a welcoming organization for newcomers, it gives them
clear pathways and as much direct assistance as possible. However, it is
already known that newcomers face many barriers while attempting to contribute
to the open source community for the first time. Some barriers they face include
orientation issues that can potentially demotivate newcomers from placing
their first contribution. On the other hand, gamification is widely used to
engage and motivate people to accomplish tasks and improve their performance.
Therefore, the goal of this project is to use gamification to orient and
motivate newcomers to overcome onboarding barriers to contribute and engage
with coala and its community.

For the implementation of this process, we will incorporate a gamification
system which will allow us to track newcomer's progress and assign some
points to each of the activity they do. We will also have different levels
and give them badges on the basis of points earned and performed activities
by the newcomers.

So, every newcomer at coala will have to complete the processes involved in
the gamification system in order to become a developer at coala or to get the
**coala Developer** badge.

### The Detailed idea

There will be three game design elements to help newcomers: Points, Levels
and Badges. For each of these elements, we will have a set of rules to
describe the operation of the gamified environment. i.e.:

### Points and Rules

Let’s start by defining some rules for earning points. Points are a
simple tool to reward behavior, and they provide an excellent base upon which to
build a gamified design. The first step is to define the activities that we want
newcomers to take. i.e.:

- Created Issues by a Newcomer
- Created Pull Requests by a Newcomer
- Number of Gitmate errors on the PR
- CI status of the PR
- Number of reviews done by a Newcomer
- Number of comments in one review by a Newcomer
- Comments on the Issues by a Newcomer
- Activities on Gitter channels
- Introduce `.coafile` in other projects

Then we will assign some points to each of the activity a newcomer perform.
E.g.: If a user is creating a difficulty/newcomer issue he will be awarded
with `10` points, similarly, the value of the points will be increased
according to the difficulty level of the issue.

### Levels

To increase a player’s competitive instinct, we can implement a series of
levels that confer rank as newcomers become more active. A good and
straightforward way to award levels in a new gamified design is to base them
on point thresholds. As players earn points, they move up an incremental
series of levels. To infuse the level with meaning i.e.: a ranking within the
system, the levels should be named in a way that indicates status.

- Level-I:
  - Name: Beginner
  - Points_required = 50
- Level-II:
  - Name: Intermideate
  - Points_required = 150
- Level-III:
  - Name: High
  - Points_required = 300

More complex designs, such as awarding levels based on consecutive activities
are also possible. E.g.:

- Level-I:
  - Joining our Gitter channel and the community
  - Running CI tools on your fork
  - Running coala-ci on a popular GitHub repo
- Level-II:
  - Getting assigned to a difficulty/newcomer issue
  - Creating and merging PR for that issue
  - Reviewing at least one difficulty/newcomer issue
- Level-III:
  - Getting assigned to a difficulty/low issue
  - Creating and merging a PR for that issue
  - Reviewing at least one difficulty/low issue

But that would be hard to implement and out of the scope of this project
as we can't automatically prevent newcomers from performing the activities
which comes under upcoming levels.

### Badges

Another way of explicitly nudging a newcomer to action is to award badges for
completing tasks. Badges enable newcomers to follow their performance and compare
to other newcomers.
Badges will be awarded after performing certain activities by the newcomers:

- Badge-I:
  - Name: The bug finder
  - Details: The one who find bugs in the existing codebase
  - Activities: Created at least 2 or more issues which has a label bug
- Badge-II:
  - Name: The reviewer
  - Details: The one who review others pull requests
  - Activities: Reviewed at least 4 or more PRs
- Badge-III:
  - Name: The coder
  - Details: The one who code
  - Activity: Merged at least 3 or more PRs
- Badge-IV:
  - Name: The coala Developer
  - Details: The ones who are in the coala developers team
  - Activities:
    - Introduce `.coafile` in other projects
    - Merge a difficulty/newcomer Pull Request
    - Review at least a difficulty/newcomer Pull Request
    - Merge a difficulty/low Pull Request
    - Review at least a difficulty/low or higher Pull Request

So, for becoming a developer at coala, a newcomer must complete all the
activities mentioned in the `coala Developer` badge. But in case a newcomer
is not able to become a developer for some resaon, he/she can get other
badges based on their activities. We can also introduce more badges in future.

## The Result

Waiting is the hardest thing to do in life, and when you wait for the one and only result which could take you and your career on a better path it becomes harder.

I did no work in the last week of result announcements, I just eat and sleep. No nothing.

And when that day finally comes I didn't even eat and sleep.

I am really happy to state that I have been selected to work with coala for [GSoC 2018](https://summerofcode.withgoogle.com/).

![](https://image.ibb.co/e6SdW7/gsoc.png)

## Whats Next

I am looking forward to have a great summer working with coala and my project mentor's [John](https://github.com/jayvdb), [Pranav](https://github.com/prnvdixit), [Andrew](https://github.com/andrewda) and [Gopala](https://github.com/gkrishnan724).

I will also write a blog post on weekly basis with the updates on my work expreience and progress. That's all folks, stay tuned. Thank You for reading!:)
