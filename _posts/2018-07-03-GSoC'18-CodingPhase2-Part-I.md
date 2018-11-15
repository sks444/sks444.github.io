---
layout: post
title: "GSoC'18 Coding Phase-2: Part-I"
tagline: "Building the Gamification App"
categories: gsoc
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

Welcome all.

This blog will be about the implementation of the gamification system we discussed in the [cEP](https://github.com/coala/cEPs/blob/master/cEP-0020.md).

Summary of what we have to achieve:
- Import all the issues and mrs opened by newcomers in coala org on GitHub and GitLab.
- Create levels.
- Iterate through each of the mrs:
  - If the mr is merged:
    - Get an activity based on the labels on the mr.
    - That activity would be assign with some points.
    - Update the total score and current level based on the new points earned for
      the newcomer who opened the mr.
    - Add the activity to the newcomer's activities field.
    - Get the issues that mr is closing.
    - Get the activity based on the labels on the issue.
    - That activity would be assigned with some points.
    - Update the total score and current level based on the new points earend for
      the newcomer who opened the issue.
    - Add the activity to the newcomer's actvities field.
 - If the mr is closed without merge:
    - Get an activity "Closed a mr without merge"
    - Deduct points from total score and update the current level.
- Create Badge Activities and Badges.
- Iterate through all newcomers' activities.
  - Award them badges based on those activities.

>Throughout this blog I will discuss the work that has been done in [building gamification app](https://github.com/coala/community/pull/160) pr.

## Designing Django Data Models

So let's begin with designing Django models for the first part which involves Activity, Level and Newcomer models:

```python
class Newcomer(models.Model):
    username = models.CharField(max_length=100, primary_key=True)
    score = models.IntegerField(default=0, null=True)
    level = models.ForeignKey(Level, on_delete=models.CASCADE,
        default=1, null=True)
    activities = models.ManyToManyField(Activity)
```

We can see that the newcomer model has field name `level` ForeignKey with `Level` model and `activities` ManyToMany with Activity model.
> As a newcomer can only be at one level at a time but they can perform multiple activities.

Similarly we can design the `Level` and `Activity` model:

```python
class Level(models.Model):
    number = models.IntegerField(primary_key=True)
    min_score = models.BigIntegerField()
    max_score = models.BigIntegerField()
    name = models.TextField()
```
>min_score and max_score is the minimum and maximum score required to be in that particular level.

```python
class Activity(models.Model):
    name = models.TextField()
    points = models.IntegerField()

    # Number of times this activity has been performed by the
    # same newcomer
    number_of_times = models.IntegerField(default=1, null=True)
```

> As commented on the `number_of_times` field, it's required in case of the same newcomer perform the same activity multiple times. E.g. If a newcomer performs an activity `Created a Newcomer bug` twice then we will increase the number of times field for that activity with one
instead of adding a new activity.

Now, let's add some methods to the `Newcomer` model to add points, set levels and add activities:

A method to find suitable level based on the total score earned:
```python
    def find_level_for_score(self, score):
	    level = Level.objects.get(min_score__lte=score, max_score__gt=score)
        return level
```

A method to update the score and levels:
```python
    def update_score_and_level(self, points):
        """
        Update score and level based on points.
        """
        if points < 0 and self.score < abs(points):
            new_score = self.score = 0
        else:
            self.score += points
            new_score = self.score

        new_level = self.find_level_for_score(new_score)
        if new_level.number > self.level.number:
            self.level = new_level
```

A method to add activities:
```python
    def add_activity(self, points, activity_string):
        """
        Add activity to the newcomer.

        This method checks if the current activity is
        already peformed by the user, if yes, then it
        increase the 'number_of_times' field with one.
        If not then it adds a new activity to the newcomer.
        """
        activity, created = Activity.objects.get_or_create(
            name=activity_string, points=points)
        if created:
            activity.save()
            self.activities.add(activity)
        else:
            activity.number_of_times += 1
            activity.save()
```

Lets call all these methods with a single method:
```python
    def update_newcomer_data(self, points, activity_string):
        self.update_score_and_level(points)
        self.add_activity(points, activity_string)
```
>Now, whenever we call the method `update_newcomer_data` with passing two parameter
`points` and `activity` it will update total score, current level and adds that activity for
the newcomer who performed the same.

## Getting Activities based on Labels

I have created two methods [`get_issues_activity`](https://github.com/coala/community/pull/160/files#diff-7770b5f0e1da18c0562f83626d0c4824R69) and [`get_mrs_activity`](https://github.com/coala/community/pull/160/files#diff-7770b5f0e1da18c0562f83626d0c4824R247) which takes a QuerySet dict containing the 'name' as key and 'name of the label' as value and return a tuple of points and activity string.

## Creating Gamfication Data

Before going further we need to have some initial data in the database like `levels` and `newcomers`.

I have defined a method [`create_levels`](https://github.com/coala/community/pull/160/files#diff-a2f391b5ebdc079b160c3ebbb7eed3a1R10) which initialized the levels object we needed in a list and then we can use `bulk_create` method to create all the levels with one query.

Similarly, we can initialize all the necomers objects in a list and then use `bulk_create` method to create hundreds of newcomers at once.

E.g. We can get the newcomers_list from the function [`get_newcomers`](https://github.com/coala/community/pull/160/files#diff-a5004880077c7144b43cffcdf013f5bfR8) and create all the newcomers which will be involved in the gamification app with their initial data by doing the following:

```python
    newcomer_objects_list = []
    for newcomer in get_newcomers():
        newcomer_objects_list.append(
            Newcomer(username=newcomer))
    Newcomer.objects.bulk_create(newcomer_objects_list)
```

## Updating Gamfication Data

Now the time has come to iterate through the mrs and update the newcomers data:

```python

def update_newcomers_data(mr):
    """
    Update total score earned by the
    newcomer based on the activities performed,
    and the update the current level based on the
    total score.

    This method first check if the mr is merged or not,
    if it's merged, then get the activity and points based
    on the labels on mr and update the newcomer data who opened
    this mr.

    Further it gets all the issues this mr is closing and if its
    merged and then get the points and activity based on the labels
    on the issue and update the newcomer data who opened that issue.
    """
    logger = logging.getLogger(__name__)
    if mr.state == 'merged':
        labels = mr.labels.values('name')

        # Get the newcomer who opened this mr
        ncm = Newcomer.objects.get(username=mr.author)
        try:
            # Get activity and points based on labeles on the mr
            points, activity_string = get_merge_request_activity(labels)
        # Accept Exception if no activities found
        except Exception as ex:
            logger.error(ex)
            return
        # Update newcomer data
        ncm.add_points(points, activity_string)
        ncm.save()

        # Get all the issues numbers this mr is closing
        issues = mr.closes_issues.all()
        repo = mr.repo
        for issue in issues:
            # Get issue object from issue model
            i = Issue.objects.get(number=issue.number, repo=repo)
            i_labels = i.labels.values('name')

            # Get newcomer who opened the issue
            ncm = Newcomer.objects.get(username=i.author)

            # Get activity and points based on the labels on the issue
            points, activity_string = get_issue_activity(i_labels)

            # Update newcomer data who opened the issue
            ncm.add_points(points, activity_string)
            ncm.save()
    if mr.state == 'closed':
        ncm = Newcomer.objects.get(username=mr.author)
        points = 5
        activity = 'Closed a merge_request without merge'
        # Deduct 5 points for closing the merge_request
        ncm.deduct_points(points, activity)
        ncm.save()

```

## Doing it for Badges

Similar things can be done for badges:

Lets add a `badges` field to newcomers model:

```python
badges = models.ManyToMany(Badges)
```

Create Badge and BadgeActivity Models:

```python
class BadgeActivity(models.Model):
    name = models.TextField()

    # Number of times a newcomer have to perform this activity
    # to get this badge.
    number_of_times = models.IntegerField(default=1, null=True)


class Badge(models.Model):
    number = models.IntegerField(primary_key=True)
    name = models.CharField(max_length=200)
    details = models.TextField(null=True)

    # Activities a newcomer have to perform to get this badge
    b_activities = models.ManyToManyField(BadgeActivity)

```

Now lets add some methods to `Newcomer` model for awarding badges:

```python

    def find_badges_for_activity(self, activities):
        """
        Find the badge based on the activities peformed by the newcomer.

        :param activities: a QuerySet dict containing the 'name'
                           as key and 'name of the activity' as value
        :return: a badge object
        """
        activities = [activity['name'] for activity in activities]
        badge_objects_list = []
        badges = Badge.objects.all()
        for badge in badges:
            b_activities = badge.b_activities.values('name')
            b_activities = [b_activity['name'] for b_activity in b_activities]
            match_activity_list = []
            for b_activity in b_activities:
                if b_activity in activities:
                    match_activity_list.append(1)
            if b_activities.count() == len(match_activity_list):
                badge_objects_list.append(badge)
        return badge_objects_list

    def add_badge(self, activities):
        """
        Add badge to newcomer based on the activities performed.
        """
        badges = self.find_badges_for_activity(activities)
        if badges.count() == 0:
            return
        for badge in badges:
            self.badges.add(badge)
```

`find_badges_for_activity` will find the badge when all of its activities is in the `activities` list.

### Creating Badges

First we will create some badge_activities and then define the badges with adding suitable activities in each of them.

E.g. see [`create_badge_activity`](https://github.com/coala/community/pull/160/files#diff-a2f391b5ebdc079b160c3ebbb7eed3a1R39), [`create_badges`](https://github.com/coala/community/pull/160/files#diff-a2f391b5ebdc079b160c3ebbb7eed3a1R184) and [`add_activities_to_badges`](https://github.com/coala/community/pull/160/files#diff-a2f391b5ebdc079b160c3ebbb7eed3a1R230) methods.

### Awarding Badges

There is not much left to do for awarding badges, just iterated through all the newcomers and get all the activities done by them and then call the `add_badges` method defined in the `Newcomer` model with the `activities` parameter:

```python
def award_badges(newcomer):
    activities = newcomer.activities.values('name')
    newcomer.add_badge(activities)
    newcomer.save()
```

>That's it.
>Thanks for reading:)
>Will write about testing this gamification app next!
