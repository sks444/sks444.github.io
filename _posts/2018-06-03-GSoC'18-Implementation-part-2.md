---
layout: post
title: "GSoC'18 Coding Phase-1: Week-3"
tagline: "GitHub, GitLab and the Django docs"
categories: gsoc pinned
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

Welcome, all.

Here, I will discuss the details on how I managed to get the
GitHub and GitLab data we needed for coala org to implement in the
gamification project. And in the end, I will go through few things
I have learned new in Django from its wonderful docs which will be
helpful in my upcoming coding phase.

---

## IGitt

You must be wondering what the heck is IGitt, right?

Well, this is what their doc says:
>IGitt is a simple library that allows you to access
various git hosting services like GitHub, GitLab and so on via one
unified python interface.

Isn't it great to have a common interface to access GitHub and GitLab
APIs with python?

And believe me, it is damn easy to use, let me show you some examples of
how to play around with it:

First, install it with `pip install IGitt` and then suppose that you want
to access all the repositories for a GitHub org and then get all the issues for each of those repositories.
```python
from IGitt.GitHub import GitHubToken
from IGitt.GitHub.GitHubOrganization import GitHubOrganization

#Get the org object
org = GitHubOrganization(GitHubToken('Your GitHub Token'), 'coala')

#Get its repositories
repos = org.repositories

#Iterate through repos and get the issues
for repo in repos:
	issues = repo.filter_issues(state='all')
	for issue in issues:
		#Get the state of the issue, like, its open or closed.
		print(issue.state.value)
```
Cool, right? We see that we can get all the issues for a repo with passing
the parameter `all` to `filter_issues` method, worth mentioning that
`filter_issues` also takes parameters like `open` and `closed` if you want to
get the issues based on their states.

While playing around with IGitt, I found out that IGitt doesn't support a method `filter_merge_requests` like `filter_issues` yet, well, it have a method
`merge_requests` which fetches all `open` merge_requests for a repo by 
default, but what if we needed to get `closed` and `merged` merge_requests?

So, what do we do then? We are gonna need this method to use it in our project.
>Well, its the beauty of open source that the thing which you don't find in
the library you are using, you can create it on your own.

So, that's what I did, I talked to the IGitt community and created a [merge request](https://gitlab.com/gitmate/open-source/IGitt/merge_requests/248) at IGitt to support this method. 

Well, till now I have only shown you the examples on how to get GitHub data with IGitt, what about GitLab? How we are gonna get the GitLab data?
>**No worries**, just replace **GitHub** with **GitLab** in the above example and you're good to go!

## IGitt with Django

You'll be amazed to know that to save all the GitHub and GitLab data provided by IGitt to the database in a Django project, we don't have to create Django models for IGitt and import all the data into it.

What we are gonna do is to use the available [Django's database caching](https://docs.djangoproject.com/en/2.0/topics/cache/#database-caching) with [IGitt caching mechanism](https://igitt.gitmate.io/using_cache.html). And then
we just need to query the IGitt objects once and then from next time onwards
it just fetches the data from the cache, plus the data would always be upto date via the ETag request header.

Have a look at this [Merge request](https://gitlab.com/coala/landing/merge_requests/47) where I am implementing IGitt caching mechanism on a
Django project and then fetching the data along with dumping it onto a Json
with the help of Django views.

## The Django Docs

Well, there are many good ways to start your work period.
>But, for me, from last seven days the good way to start the work, daily, is to read the [Django docs](https://docs.djangoproject.com/en/2.0/) for an hour or so.

I still remember the days when I started learning Django without knowing much about any programming language or Python for that matter. And those docs seemed
to me like it was written in some unknown language.

But, when I am reading them now, after having some experience with Python and built some small applications on Django, it feels like, I am reading the series of Harry Potter, everything seems connected and understandable!

I first started understanding more about the three layers available in Django, [The model layer](https://docs.djangoproject.com/en/2.0/#the-model-layer), [The view layer](https://docs.djangoproject.com/en/2.0/#the-view-layer) and [The template layer](https://docs.djangoproject.com/en/2.0/#the-template-layer).

In the model layer, I learned about how and when to use an [intermedate model](https://docs.djangoproject.com/en/2.0/topics/db/models/#extra-fields-on-many-to-many-relationships)
when there is `ManyToMany` relationship b/w two of your models like if a `Person` has to join some `Group`, he/she has to go `through` a `Membership` model. And then I learned about [Model inheritance](https://docs.djangoproject.com/en/2.0/topics/db/models/#model-inheritance), some cool [QuerySets](https://docs.djangoproject.com/en/2.0/topics/db/queries/)(*never knew that we have that much parameters available for our `get()`,`all()` and `filter()` methods.*), some more about [Model instances](https://docs.djangoproject.com/en/2.0/ref/models/instances/), and some more about [Migrations](https://docs.djangoproject.com/en/2.0/topics/migrations/), most importantly the [Data Migrations](https://docs.djangoproject.com/en/2.0/topics/migrations/).

In the view layer, I learned about [how Django processes a request](https://docs.djangoproject.com/en/2.0/topics/http/urls/#how-django-processes-a-request), what is the [Reverse URLs](https://docs.djangoproject.com/en/2.0/topics/http/urls/#reverse-resolution-of-urls) and why
we should [name our URL patterns](https://docs.djangoproject.com/en/2.0/topics/http/urls/#naming-url-patterns). And then I learned some more about the beauty of the [Class-Based Views](https://docs.djangoproject.com/en/2.0/topics/class-based-views/#class-based-views) and what is the [history](https://docs.djangoproject.com/en/2.0/topics/class-based-views/intro/#the-relationship-and-history-of-generic-views-class-based-views-and-class-based-generic-views) behind the need of it.

>*P.S. I would like to discuss these topics in details in my other blogs.*

> Thank You for reading ;)
