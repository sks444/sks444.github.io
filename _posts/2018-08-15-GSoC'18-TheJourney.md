---
layout: post
title: "GSoC'18 The Whole Journey"
tagline: "Wrapping up"
categories: gsoc pinned
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

Hello all.

GSoC'18 has come to an end, it was a great learning experience for me.

I still remember my first big patch to coala, I had taken the issue just
because it has the word 'Django' in it and I was fascinated by it.
I still smile when I think about the code I had written in that pr in my first
push, it almost requires more than 100 lines of code and I had pushed only 5
to 6 lines. But the maintainers were so supportive that they believed that
I can do it. It took me almost 20 days to solve that issue and they were there
for me reviewing my dummy codes after each of my push.

**That's the kind of importance you get at coala if you're willing to learn
  and do something.**

Merge of that pr motivated me so much that I decided to push on and kept
contributing to coala for upcoming few months.

---

Now it was the time when Google has announced the
[organizations who are participating in GSoC'18](https://summerofcode.withgoogle.com/organizations/), and as expected [coala](https://summerofcode.withgoogle.com/organizations/?sp-search=coala#6207722836459520) was one of them. So I asked John if
there are any projects in the [projects list](http://projects.coala.io/#/projects) which will be implemented in the [repo](https://github.com/coala/community])
I have worked on in the last few months. John said it would [Newcomer metrics and Gamification](http://projects.coala.io/#/projects?project=newcomer_metrics_and_gamification&lang=en) project. And from there I started my research on that project.

---

After a few months of continuous work I had got into GSoC and now it was the time to write
a [cEP](https://github.com/coala/cEPs). And in my first push, I just copied the ideas from
my proposal and pasted it in the cEP, because that's what I thought would get implemented. But I was definitely wrong and for the next two weeks of the first phase, we(me and John) worked on just refining the project idea. Yeah, you read it right, two
weeks and no coding, only long important suggestions by John. But after we got the [cEP merged](https://github.com/coala/cEPs/pull/150) we had a clear path and light and now we just had to walk!

---

And then in the last two weeks of the first phase, I mostly worked on the
[webservices](https://gitlab.com/coala/landing) repo to generate APIs for the data
we needed about the newcomers to build the gamification app. I was using
[IGitt](https://gitlab.com/gitmate/open-source/IGitt/) to get the data from GitHub
and GitLab APIs. And I implemented [IGitt caching mechanism](https://gitlab.com/coala/landing/merge_requests/47) with some cron_job scripts for [issues](https://gitlab.com/coala/landing/merge_requests/49) and [mrs](https://gitlab.com/coala/landing/merge_requests/51) and I thought it would be enough to get the data at these [urls](https://webservices.coala.io/mrs/github/all) but what happened is when we go to those
URLs 'then' those scripts start running and it keeps loading as there are thousands of
issues and mrs at coala. IGitt caching mechanism would have benefited us once all
old the issues and mrs were in the database so that it can update the new issues and mrs
within seconds.

---

Now the second phase has come and I still had to make the APIs working but then
we realize there are also some problems with the contributors_cron job which will
be very important for us, as from there we will get our newcomers team. So, for the next few days, we worked on fixing [contributors cron_job](https://gitlab.com/coala/landing/merge_requests/57) with new [IGitt version](https://gitlab.com/coala/landing/merge_requests/56/diffs#7467689207bc56c9ef017581c0c04191bc16e11e_7_8).

And then I created a pr for the issues and mrs APIs with building a Django data model
to save the initial data for all the issues and mrs and then the IGitt caching mechanism
would help us in fetching the new data in less than seconds.

But then John suggested me to start working on the gamification app assuming that we have
all the data we need is coming from the APIs. 

Now, it was the most critical and challenging part of the project. I knew what to do but
I didn't know how to do. I had to design a model for the gamification app something like whenever a participant performs an activity his total score should increase based
on the points assigned to that activity and the current level of the participant
should increase based on the present total score. And this was the first part of the app
and I came up with a basic model of the gamification participant with the methods like `add_activity`, `find_level_for_current_score`, `update_levels_and_points` etc. Now when this model was ready I had to update the participants' levels and score just after a merged pr(authored by that participant) is saved into the database. So, I came up with Django [post_save](https://docs.djangoproject.com/en/2.1/ref/signals/#post-save) method and now I was updating each participant data just after saving of a merged mr.

But when I started adding new functionality to it, it was taking so much time to update all the participant. So I stopped there and thought more about it. And come up with an idea that why not just save all the mrs and issues first and then iterate through it to update the participants' data, and that's how I came up with methods
[`update_participants_data_with_mr`](https://github.com/coala/community/blob/master/gamification/process/update.py#L25) and [`update_participants_data_with_issues`](https://github.com/coala/community/blob/master/gamification/process/update.py#L107).

Similarly I added the second functionaltiy to participants model by adding methods like
[`find_badges_for_activity`](https://github.com/coala/community/blob/master/gamification/models.py#L147) and [`award_badges`](https://github.com/coala/community/blob/master/gamification/models.py#L182) and then iterated through each participant, getting all
the activities performed by them, matching them with badge activities and when
a participant has performed all the activities in a badge, awarding the participant with
that badge.

And then I also started writing [tests](https://github.com/coala/community/tree/master/gamification/tests) for the methods I had created which helped me a lot to improve the same.

---

In the third phase, I also started building the [Gitter](https://github.com/coala/community/pull/145) app which I have covered in my last blog.

Then I had a meeting with John and he suggested me that I should use [igitt_django data models to save IGitt data](https://gitlab.com/coala/landing/merge_requests/59) but the problem was there was no support of `sqlite3` to [igitt_django](https://gitlab.com/gitmate/open-source/igitt-django), so I created a [pr](https://gitlab.com/gitmate/open-source/igitt-django/merge_requests/7) for it and modified my existing scripts to use the same.

John also suggested me to create a [repo similar to webservices](https://github.com/sks444/webservices.coala.io) and use rawgit to get fix URLs for the APIs similar to
the webservices until the webservices is down. And the data could be updated by the end
of end each week by running the scripts locally.

In the end, we also added more tests and got 100% coverage for the gamification and the
data app and got gamification pr merged.

**My all the work done during GSoC can be found at my [GSoC'18 report](http://projects.coala.io/GSoC/2018/StatusReport/shrikrishna)**

### Thank You

I was very grateful to have a mentor like John throughout my whole journey who was there
for me everytime I was in problem. Thank you, John, for teaching me everything, I have learned a lot from you and you're really a great mentor.

I also want to thank my other GSoC-mates who helped me during this period and made this
journey easy and fun.
