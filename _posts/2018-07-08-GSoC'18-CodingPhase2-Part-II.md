---
layout: post
title: "GSoC'18 Coding Phase-2: Part-II"
tagline: "Tests for the Gamification App"
categories: gsoc
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

>Testing always help us to figure out the unintentional bug we have committed while writing our code.

So, let's write some tests for the gamification app we have created earlier and find out what we have missed.

We could start by testing the [models](https://github.com/coala/community/pull/160/files#diff-4635bad0cddfe4cefad2a3ec7b930858) and see if all the methods we designed for the Participant model is working the way we wanted:

So, let's first setUp the required tests data:
```python
class ParticipantModelTest(TestCase):

    @classmethod
    def setUpTestData(cls):
        # Set up non-modified objects used by all methods
        Level.objects.create(number=1,
                             min_score=0, max_score=5,
                             name='newbie')
        Level.objects.create(number=2,
                             min_score=5, max_score=10,
                             name='beginner')
        BadgeActivity.objects.create(
            name='Created a newcomer bug')
        Badge.objects.create(
            number=1,
            name='The Bug Finder')
        Activity.objects.create(name='Created a newcomer bug',
                                points=5)
        Participant.objects.create(username='sks444')
        Participant.objects.create(username='test')
```

Now the first method we have in the participant model is [`__str__`](https://github.com/coala/community/pull/160/files#diff-4635bad0cddfe4cefad2a3ec7b930858R70) method, it means that the function object name should be the participant username:
```python
    def test_object_name_is_participant_username(self):
        participant = Participant.objects.get(username='sks444')
        expected_object_name = 'sks444'
        self.assertEquals(expected_object_name,
                          str(participant))
```
>And it passes!

The second method is [class Meta ordering](https://github.com/coala/community/pull/160/files#diff-4635bad0cddfe4cefad2a3ec7b930858R73) with `[-score]`, it means that the participant with the higher score would be up in the leaderboard.
```python
    def test_class_meta_ordering(self):
        participant1 = Participant.objects.get(username='sks444')
        participant1.score = 5
        participant1.save()
        participant2 = Participant.objects.get(username='test')
        participant2.score = 10
        participant2.save()
        participants = Participant.objects.all()
        self.assertEquals(participants[0].username, 'test')
        self.assertEquals(participants[1].username, 'sks444')
```
>And it passes! As we can see that the participant `test` is the first item in the list instead of `sks444` because it has the highest score.

The third method is [add_points](https://github.com/coala/community/pull/160/files#diff-4635bad0cddfe4cefad2a3ec7b930858R76) which simply calls the `update_score_and_level` method and `add_activity` method. And the `update_score_and_level` method uses the `find_level_for_score` method, So let's first test the `find_level_for_score` method, which should return a level object based on the total score of the participant.
```python
    def test_find_level_for_score_method(self):
        participant = Participant.objects.get(username='sks444')
        level = participant.find_level_for_score(5)
        self.assertEquals(level.name, 'beginner')
```
>And it passes!

Now, test for `update_score_and_level` method, which should update the total score and level of the participant based on the activity performed and the points assigned to that activity.
```python
    def test_update_score_and_level_method(self):
        participant = Participant.objects.get(username='sks444')
        # Before update
        self.assertEquals(participant.score, 0)
        self.assertEquals(participant.level.name, 'newbie')

        # Update
        participant.update_score_and_level(5)

        # After update
        self.assertEquals(participant.score, 5)
        self.assertEquals(participant.level.name, 'beginner')
```
>And it passes!

Now, the tests for the `add_activity` method, which should add a new activity to the participant who performs that activity and if that activity is already performed by the participant it should increase the `number_of_times` field with one.

>But, wait! I just realized that our old [`add_activity`](https://pastebin.com/raw/cAxXRFta) method doesn't check if an activity is already performed by the `same is user`, it just checks if an activity is performed(by any user), if yes it increases the `number_of_times` field with one. And that has to fixed before writing the tests.

Now, let's do some detailed testing for the [modified add_activity](https://pastebin.com/raw/ZjnVZygw) method.
```python
    def test_add_activity_method(self):
        participant = Participant.objects.get(username='sks444')

        # Befor applying add_activity
        self.assertEquals(participant.activities.count(), 0)

        # Apply add_activity
        points = 5
        activity = 'Created a newcomer bug'
        participant.add_activity(points, activity)

        # After applying add_activity
        self.assertEquals(participant.activities.count(), 1)

        # Performing the same activity again
        participant.add_activity(points, activity)

        # No new activity added
        self.assertEquals(participant.activities.count(), 1)

        # Number of times this activity has been performed
        # by the participant
        same_activity = participant.activities.get(
            name=activity,
            performer=participant.username)

        # 'number_of_times' of old activity increased by one
        self.assertEquals(same_activity.number_of_times, 2)
```
>As you can see, I have done testing for all the aspects of `add_activity` method. and it passes too:)

Similarly, I have written tests for the `find_badges_for_activity`, `add_badge`, tests for other models, tests for views and the management commands too, which can be found in the [gamification pr](https://github.com/coala/community/pull/160).

## What's Next

Phase-3 will be about improving the gamification app and improving the gitter messages analysis along with writing documentation and more testing.
