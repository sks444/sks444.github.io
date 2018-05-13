---
layout: post
title: "The"
tagline: "The Planning"
categories: gsoc pinned
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

Welcome all to my third blog!

This one aims to describe the implentation of the idea about 
the gamification system we had discussed [here](https://sks444.github.io/gsoc/pinned/2018/05/13/GSoC'18-community-bonding-period.html)

## Getting the Newcomers at coala

The gamification system will be deactivated when a newcomer
at coala becomes developer.

So, it's all about the newcomers' list which we can get 
from [this function](https://gist.github.com/sks444/ee1e6b8964f54e05b78c2514b2ac1405).

## Collecting the data

To move further and build this system we are gonna need to 
have the record of each of the activity a newcomer do on 
our community platforms. I.e.: Gitter, GitHub and GitLab.

### Gitter Data

The algorithms to filter out newcomers' messages from each
of our rooms will be implemented on our webservices.

And then the analysis of those messages will be done 
in the community repo.

For the analysis we will have a tool which can categorize
b/w questions asked and answered given by the newcomers
to our rooms.

For filtering out the `Questions` we will match the patterns
like `How`, `Why`, `When`,`?`..etc. And for filtering out 
the `Answers` we will remove the words like, `Welcome`,
`Thank You`..etc, we can also define a rule to check the length
of the answers, as a anwser should be long and explantory.

And then these questions and answers will be implemented 
into two Django models, `Questions` and `Answers` with the
fields: `message_id`, `text`, `sent_at` and `sent_by`.

Number of questions asked and answers given by newcomers, b/w getting assigned to a issue and merge of a pr for the same will be shown on the user dashboard which help maintainer decide which newcomer is noisy through the process and not promote them to developer and they have learned to learn things by themselves.

### GitHub and GitLab Data

For collecting the data from GitHub and GitLab we will use an 
awesome tool([IGitt](https://gitlab.com/gitmate/open-source/IGitt/)) build by our own community.:)

Like Gitter initial algorithms, we will implement `IGitt` into
our webservices to filter out only the useful data. I.e.:
`Issues opened by newcomers`, `Issues assigned to the newcomers`,
`Pull requests merged by newcomers`.

And then we will have a issue model in our community repo something like this:

```python
class Issue(models.Model):
	opened_by = models.ForeignKey(Newcomer)
	opened_at = models.DateTimeField()
	assigned_to = models.ForeignKey(Newcomer)
	labels = models.ManyToManyField(Label)
	reactions = models.ManyToManyField(Reactions)
```

And a pull request model would be something like this:

```python
class PullRequest(models.Model):
	crated_by = models.ForeignKey(Newcomer)
	crated_at = models.DateTimeField()
	gitmate_errors = models.IntegerField()
	status = models.CharField(max_length=100)
	labels = models.ManyToManyField(Label)
	closing_issue = models.OneToOneField(Issue)
```
### 