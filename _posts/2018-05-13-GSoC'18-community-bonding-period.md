---
layout: post
title: "GSoC'18 Community Bonding Period"
tagline: "The Project, The work, The Bonding"
categories: gsoc pinned
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

Here I will cover the details about my project, the work that has been done so far and the bonding with the awesome coala members.

## The Project

### Abstract

As coala tries to be welcoming organization for newcomers, giving them clear pathways and as much direct assistance as possible.
However, it is already known that newcomers face many barriers while attempting to contribute to the open source community for the first time. Some barriers they face include orientation issues that can potentially demotivate newcomers from making their first contribution.
On the other hand, gamification is widely used to engage and motivate people to accomplish the task and improve their performance.Therefore, the goal of this project is to use gamification to orient and motivate newcomers to overcome onboarding barriers to contribute and engage with coala and its community.

### The Detail idea

The goal of this project is to make use of metrics and gamification to motivate and support newcomers to overcome the orientation barriers to contribute to the community.
To achieve this goal I have selected three game design elements to help newcomers: Levels, Points, and Badges. For each of these elements, we will have a set of rules to describe the operation of the gamified environment. i.e.:

- **Levels** group different tasks, enabling users to unlock specific and contextualize set of actions. Levels will be applied to make newcomers focus on a given step before moving ahead on the contribution process. E.g.: We will divide our newcomer process into three levels and each level will contain three tasks:
  - Level-I
    - Joining our gitter channel and the community
    - Running CI tools to your fork
    - Running coala-ci on a popular github repo
  - Level-II
    - Getting assigned to a difficulty/newcomer issue
    - Creating and merging a PR for that issue
    - Reviewing at least one difficulty/newcomer issue
  - Level-III
    - Getting assigned to a difficulty/low issue
    - Creating and merging a PR for that issue
    - Reviewing at least one difficulty/low issue


- **Points** will be the most applied element out of three game design elements. Acquiring points means that the actions performed by the users are in accordance with what was expected. Points can be earned by means of performing actions to the different tasks under levels. E.g.: If a user wants to go to next level he will have to collect a certain number of points from doing the tasks under levels. But in case of Level-II and Level-III if a user is not able to acquire the required number of points after performing all the tasks, he will have to review more PRs to collect the required points.

- **Badges** enable newcomers to follow their performance and compare to other newcomers. After completing the level three successfully the newcomer will be a developer. And he will be awarded a badge based on their performance in each level and the total number of points.

> Sounds cool, right? What you just read above is same as what I had in my GSoC proposal.
> But, it got hundreds time better when I started writing my cEP. I will explain what a cEP is and what are the discussions I had with my mentors that make this idea better, in my next blog. Keep reading!:)

## The Bonding

As I had done most of my work with the [communtiy repository](https://github.com/coala/community). I still needed to get some more familiarity with our core repo's codebase. So, John assigned me some issues to solve in the coala and coala-bears.

The first [issue](https://github.com/coala/coala-bears/issues/2330) was about changing GitCommitBear to use [git-url-parse](https://github.com/retr0h/git-url-parse) for parsing the remotes. But git-url-parse had some upstream bugs to fix, which I managed to solve with the help of John and then I had some discussions with 
[virresh](https://github.com/virresh) on my PR as he has been doing work in that field and managed to get it merged.

The second [issue](https://github.com/coala/coala/issues/5107) was about adding support for writing ignore comment in jinja2. The issue has the difficulty level low but it took me some time to understand the codebase. And with the help of John and [Kriti](https://github.com/kriti21) I managed to get my second PR merged.

The third [issue](https://github.com/coala/coala/issues/4289) is a bug and requires a good understanding of the coala codebase. It took me three days to understand the codebase but still, I am not able to point the source of that bug. My search is in progress, hope to get it solve soon.

Other than solving issues and reading the codebase I also did some more research on my project and created a [PR](https://github.com/coala/cEPs/pull/131) for the cEPs(cEPs means coala Enhancement Proposals you can read more about it [here](https://github.com/coala/cEPs/blob/master/cEP-0000.md)). I also started reading other cEPs which helped to understand all the projects that will be done as a Part of GSoC under coala. I also contacted with other GSoC applicant [Ishan](https://github.com/ishanSrt), [Palash](https://github.com/palash25), [Vaibhav](https://github.com/RaiVaibhav), [Boxuan](https://github.com/li-boxuan) and [Nitanshu](https://github.com/nvzard) and get to know more about the projects they are doing. We have total of [19 projects](https://summerofcode.withgoogle.com/organizations/6207722836459520/?sp-page=2) that will be done this summer, this will be a great improvement for coala.

> Thank You for reading :) 