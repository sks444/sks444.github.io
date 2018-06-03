---
layout: post
title: "The idea, Refined!"
tagline: "The cEP, The Newcomers List, The Gitter Data"
categories: gsoc pinned
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

Welcome all to my third blog!

In this blog, I will discuss about how we refined the project idea while writing the cEP and the implementation that has been done in the first two weeks of coding phase-1.

**So, first of all, what is a cEP?**

>cEP means `coala Enhancement Proposals`(*got the idea about lowercase `c`?;*))

The purpose of writing a cEP is to have some heavyweight discussions before implementing your project idea when you change a process or add a feature in coala. You can read more about it [here](https://github.com/coala/cEPs/blob/master/cEP-0000.md).

You must be wondering why I am talking about 'a proposal' in my coding phase, I should be doing coding, right?

Well, this is what John say:
>A detailed spec now results in less trouble later

So, let's begin with the discussion on how we succeeded in writing a detailed spec and 
pushed the idea from [here](https://sks444.github.io/gsoc/pinned/2018/05/13/GSoC'18-community-bonding-period.html) to [here](https://github.com/sks444/cEPs/blob/348c90e2607623146ced5cb6ba9004f32fa88efa/cEP-0020.md).

## Good and Bad to Qustions and Answers

Initially, the idea was to track newcomers' messages on coala's Gitter rooms and use basics of NLP to differentiate those messages into good and bad, and then give some points when a newcomer sends a good message and deduce some points for a bad message.

>The Bad one involves asking for reviews all the time, and we can do it by NLP with matching patterns like `please review [issue link]`

>The Good one involves asking questions, and we can do it by NLP with matching patterns like `How, ?`. And we can define some more rules like a newcomer can ask some particular number of questions in a day, to avoid irrelevant questions.

But the problem with above idea is:

(*in John's word*)
>But can you predict which requests for review were bad
>
>Answer: no you cant, unless you add lots of extra data to the problem
>
>Were they doing reviews, and not getting reviews?
>
>If so, asking for a review is ok
>
>Was there pr broken and asking for review .. that is generally bad
>
>I think 'good' vs 'bad' isnt a suitable approach

So, what do we do now? 
>Well, the original issues about this project was suggesting to calculate how many messages a user sends before reaching milestones
more messages means that the user was a noisy newcomer while completing a milestone.

But, what if a newcomer has just given answers on Gitter, he shouldn't be count as noisy, right? So, what do we do in this case?
>We can count questions asked and answers given by the newcomers separately; As there are the newcomers who have helped other newcomers and has given answers more than asking questions, this will help us to differentiate those newcomers who have more number of messages but aren't noisy.

The conclusion is we won't assign points to Gitter messages, we just count questions and answers by a newcomer while they complete a particular milestone(E.g.: messages b/w the time period of getting assigned to an issue to merging a pull request for the same.) and show it to leaderboard along with activity they have completed. And not promote them to developers until they have learned to learn things on their own.

## Level-Up for Points, Badge for Activity

As you can see in the [old design](https://sks444.github.io/gsoc/pinned/2018/05/13/GSoC'18-community-bonding-period.html), I had activities under levels, if a newcomer perform all the activities in level-I then he will be up on level-II and he can perform the activities in level-II now, And after completing all the activities he will get a coala developer badge.

But the problem with the above design was we can't really locked the activities under levels and forbid newcomers for performing level-III activities, if they are in level-I. (*anyone can review difficulty/low PR, right?*)

But, guess what? This is not the bigger problem with the above design.

(*quoting John's comment*)
>If levels can be achieved by doing a set of activities, how does that differ from a badge? When should we use a level vs a badge?
>
>If level is only points based, people will expect to be able to compare the levels sanely. Two different newcomers doing different types of activities might have similar points - ensuring they are on a similar level depends on getting the points allocation correct. ouch.
>
>If level is only points based, the proposed model is quite similar to stackoverflow with
>
> - points - reputation,
> - badges (customised sets of pre-reqs),
> - and [privileges](https://stackoverflow.com/help/privileges)(point count thresholds)
>I think most people earn badges and privileges on stackoverflow at roughly the same rate, but I guess some people might get lots of points without getting new badges.
>
>If we are doing points only levels, we probably want to do the same as stackoverflow, allocating privileges to point thresholds, otherwise these levels are not very valuable.
>This means people dont try to compare the effort by two people with similar levels -- when a person gets new privileges they usually start using them, and become more active. As the levels in stackoverflow have exponentially increasing thresholds, two people on the same level are busy trying to use those privileges to get to the next level.
>
>If we don't allocate some value to these points thresholds, people will only aim for the badges.

So, the conclusion of the whole game design is, when newcomers perform some activities they will get some points, and based on that points threshold they will be leveled up along with having some cool privileges of using functionalities available in tools like [corobo](https://github.com/coala/corobo) and [Gitmate](https://gitlab.com/gitmate/open-source/gitmate-2).

And in case of a newcomer performs an activity multiple times he/she will get badges related to that activity, which shows that the newcomer has some cool skills in performing certain type of tasks.

>You can read more about the idea, like, what activities we will be tracking, what points we are giving to those activities, and what levels and badges we are pleanning to have, in my [cEP](https://github.com/sks444/cEPs/blob/348c90e2607623146ced5cb6ba9004f32fa88efa/cEP-0020.md).

---

**Coming to the implementation part**

## The Newcomers List

The gamification project will be only implemented on the [coala newcomers](https://github.com/orgs/coala/teams/coala-newcomers) team. But the problem is, in the newcomers' team we also have all the developers, some of the maintainers and admins. So, the work is to filter out the newcomers who are not into any other team, which can be done by [this script](https://gist.github.com/sks444/ee1e6b8964f54e05b78c2514b2ac1405) but this script is being implemented in our [webservices](https://gitlab.com/coala/landing) to make an API for all of our newcomers for further use, but currently we also have a `Contributor` model in the webservices repo which produces an API for [all the contributors at coala](https://webservices.coala.io/contrib/) with their related activities. So, the good way to do the work is to link all those contributors to the teams they belong and then filter out the contributors who are into only one team and that is 'coala newcomers'.

>[This pull request](https://gitlab.com/coala/landing/merge_requests/46) is adding teams for every contributor and [this logic](https://gitlab.com/coala/landing/merge_requests/45/diffs#7a27d176ae264efdb0debb4e7b26711a1387cd39) is being used for getting the list of newcomers who are not into any other teams.

## Importing Gitter Data

To track the messages by newcomers on our Gitter channels we need to have a constant supply of newcomers' messages through an API which can be done by this [merge request](https://gitlab.com/coala/landing/merge_requests/45/) in the webservices. And then we can import Questions and Answers by newcomers in the community repo, with the use of basic NLP, I will discus 'how' in my upcoming blogs. 

>Thank You for reading!:)