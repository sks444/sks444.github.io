---
layout: post
title: "GSoC'18 Coding Phase-3: Part-I"
tagline: "Import gitter messages"
categories: gsoc
image: /thumbnail-mobile.png
author: "Shrikrishna Singh"
meta: "Springfield"
---

Welcome all.

In this blog, I will discuss about how I am classifying the messages sends by newcomers
to out gitter rooms between questions and answers with the use of basic sentiment analysis. And then how it is being imported in the database.

>The code can be found at https://github.com/coala/community/pull/145

Let's start looking at an example:

*"Sorry for a silly question but, while working on an issue, once i find what the error is and fix it,how do i check if it works or not. For eg:- Once i find the fix for the issue coala/coala#5166 , how do i check if it works or not?"* 

As you can see, there has been use of the words like *What*, *How*, and the question mark notation *?*. So, the above statement can clearly be classified as a Question.

So, How can we classified this with use of NLP?
>So, for that, I am going detect the overall polarity of a statement. To compute the polarity, I'm going to use an approach based on dictionaries and some basic algorithms.

**Defining a structure for the text**

Before writing code, there is an important decision to make. Our code will have to interact with text, splitting, tagging, and extracting information from it.

But what should be the structure of our text?
- Each text is a list of sentences
- Each sentence is a list of tokens
- Each token is a tuple of three elements: a word form (the exact word that appeared in the text), a word lemma (a generalized version of the word), and a list of associated tags
This is a structure type I've found quite useful. Is ready for some "advanced" processing (lemmatization, multiple tags) without being too complex (at least in Python).

**Prepocessing the Text**

Once we have decided the structural shape of your processed text, we can start writing some code to read, and pre-process this text. With pre-process I mean some common first steps in NLP such as: Tokenize, Split into sentences, and POS Tag.

```python
import nltk

class Splitter(object):
    def __init__(self):
        self.nltk_splitter = nltk.data.load('tokenizers/punkt/english.pickle')
        self.nltk_tokenizer = nltk.tokenize.TreebankWordTokenizer()

    def split(self, text):
        """
        input format: a paragraph of text
        output format: a list of lists of words.
            e.g.: [['this', 'is', 'a', 'sentence'], ['this', 'is', 'another', 'one']]
        """
        sentences = self.nltk_splitter.tokenize(text)
        tokenized_sentences = [self.nltk_tokenizer.tokenize(sent) for sent in sentences]
        return tokenized_sentences
```

The above code will split our sentence into list of lists of word.
Here is the result when we split our message mentioned above:
```sh
In [5]: from gitter.splitter import Splitter

In [6]: splitter = Splitter()

In [7]: text = 'Sorry for a silly question but, while working on an issue, once 
   ...: i find what the error is and fix it,how do i check if it works or not. F
   ...: or eg:- Once i find the fix for the issue coala/coala#5166 , how do i ch
   ...: eck if it works or not?'

In [8]: splitted_sentences = splitter.split(text)

In [9]: splitted_sentences
Out[9]: 
[['Sorry', 'for','a', 'silly', 'question', 'but',',','while','working','on','an','issue',',','once','i','find','what','the','error','is','and','fix','it',',','how','do','i','check','if','it','works','or','not','.'],['For','eg',':','-','Once','i','find','the','fix','for','the','issue','coala/coala','#','5166',',','how','do','i','check','if','it','works','or','not','?']]


```

Now let's tag each list of words with POSTags:
```python

class POSTagger(object):

    def pos_tag(self, sentences):
        """
        input format: list of lists of words
            e.g.: [['this', 'is', 'a', 'sentence'], ['this', 'is', 'another', 'one']]
        output format: list of lists of tagged tokens. Each tagged tokens has a
        form, a lemma, and a list of tags
            e.g: [[('this', 'this', ['DT']), ('is', 'be', ['VB']), ('a', 'a', ['DT']), ('sentence', 'sentence', ['NN'])],
                    [('this', 'this', ['DT']), ('is', 'be', ['VB']), ('another', 'another', ['DT']), ('one', 'one', ['CARD'])]]
        """

        pos = [nltk.pos_tag(sentence) for sentence in sentences]
        #adapt format
        pos = [[(word, word, [postag]) for (word, postag) in sentence] for sentence in pos]
        return pos
```
Now, using this two simple wrapper classes, I can perform a basic text preprocessing, where the input is the text as a string and the output is a collection of sentences, each of which is again a collection of tokens.

Here is an example of POSTagged of splitted message mentioned above:
```sh
In [13]: from gitter.pos_tagger import POSTagger

In [14]: postagger = POSTagger()

In [15]: splitted_sentences = splitter.split(text)

In [16]: pos_tagged_sentences = postagger.pos_tag(splitted_sentences)

In [17]: pos_tagged_sentences
Out[17]: 
[[('Sorry', 'Sorry', ['NNP']),
  ('for', 'for', ['IN']),
  ('a', 'a', ['DT']),
  ('silly', 'silly', ['JJ']),
  ('question', 'question', ['NN']),
  ('but', 'but', ['CC']),
  (',', ',', [',']),
  ('while', 'while', ['IN']),
  ('working', 'working', ['VBG']),
  ('on', 'on', ['IN']),
  ('an', 'an', ['DT']),
  ('issue', 'issue', ['NN']),
  (',', ',', [',']),
  ('once', 'once', ['RB']),
  ('i', 'i', ['JJ']),
  ('find', 'find', ['VBP']),
  ('what', 'what', ['WP']),
  ('the', 'the', ['DT']),
  ('error', 'error', ['NN']),
  ('is', 'is', ['VBZ']),
  ('and', 'and', ['CC']),
  ('fix', 'fix', ['VB']),
  ('it', 'it', ['PRP']),
  (',', ',', [',']),
  ('how', 'how', ['WRB']),
  ('do', 'do', ['VBP']),
  ('i', 'i', ['RB']),
  ('check', 'check', ['VB']),
  ('if', 'if', ['IN']),
  ('it', 'it', ['PRP']),
  ('works', 'works', ['VBZ']),
  ('or', 'or', ['CC']),
  ('not', 'not', ['RB']),
  ('.', '.', ['.'])],
 [('For', 'For', ['IN']),
  ('eg', 'eg', ['NN']),
  (':', ':', [':']),
  ('-', '-', [':']),
  ('Once', 'Once', ['RB']),
  ('i', 'i', ['JJ']),
  ('find', 'find', ['VBP']),
  ('the', 'the', ['DT']),
  ('fix', 'fix', ['NN']),
  ('for', 'for', ['IN']),
  ('the', 'the', ['DT']),
  ('issue', 'issue', ['NN']),
  ('coala/coala', 'coala/coala', ['NN']),
  ('#', '#', ['#']),
  ('5166', '5166', ['CD']),
  (',', ',', [',']),
  ('how', 'how', ['WRB']),
  ('do', 'do', ['VBP']),
  ('i', 'i', ['RB']),
  ('check', 'check', ['VB']),
  ('if', 'if', ['IN']),
  ('it', 'it', ['PRP']),
  ('works', 'works', ['VBZ']),
  ('or', 'or', ['CC']),
  ('not', 'not', ['RB']),
  ('?', '?', ['.'])]]
```

**Defining a dictionary of questions and ignore expressions**

The thing is we can identify a **question** which includes words like *How*, *What* or a *?* notation, but we can never be sure about an **answer**, So, what I am going to do is to define **ignore** expressions, it means that we ignore the messages which contain the words like *Thank you*, *Welcome*, *Hello world* etc. And then the rest will go in the answer category.

Nows, I'm going to define two tiny dictionaries, one for questions expressions and other for ignore ones:

**question.yml**
```yml
when: [question]
why: [question]
how: [question]
?: [question]
what: [question]
where: [question]
```

**ignore.yml**
```yml
welcome: [ignore]
thank you: [ignore]
thanks: [ignore]
okay: [ignore]
great: [ignore]
thx: [ignore]
nice: [ignore]
awesome: [ignore]
superb: [ignore]
```

**Tagging the text with dictionaries**

The following code defines a class that I will use to tag our pre-processed text with our just defined dictionaries.

```python
class DictionaryTagger(object):
    def __init__(self, dictionary_paths):
        files = [open(path, 'r') for path in dictionary_paths]
        dictionaries_list = [yaml.load(dict_file) for dict_file in files]
        map(lambda x: x.close(), files)
        self.dictionaries = {}
        self.max_key_size = 0
        for curr_dict in dictionaries_list:
            for key in curr_dict:
                if key in self.dictionaries:
                    self.dictionaries[key].extend(curr_dict[key])
                else:
                    self.dictionaries[key] = curr_dict[key]
                    self.max_key_size = max(self.max_key_size, len(key))

    def tag(self, pos_tagged_sentences):
        return (
            [self.tag_sentence(sentence) for sentence in pos_tagged_sentences])

    def tag_sentence(self, sentence, tag_with_lemmas=False):
        """
        the result is only one tagging of all the possible ones.
        The resulting tagging is determined by these two priority rules:
            - longest matches have higher priority
            - search is made from left to right
        """
        tag_sentences = []
        N = len(sentence)
        if self.max_key_size == 0:
            self.max_key_size = N
        i = 0
        while (i < N):
            j = min(i + self.max_key_size, N)  # avoid overflow
            tagged = False
            while (j > i):
                expression_form = ' '.join(
                    [word[0] for word in sentence[i:j]]).lower()
                expression_lemma = ' '.join(
                    [word[1] for word in sentence[i:j]]).lower()
                if tag_with_lemmas:
                    literal = expression_lemma
                else:
                    literal = expression_form
                if literal in self.dictionaries:
                    is_single_token = j - i == 1
                    original_position = i
                    i = j
                    taggings = [tag for tag in self.dictionaries[literal]]
                    tagged_expression = (
                        expression_form, expression_lemma, taggings)
                    # if the tagged literal is a single token,
                    # conserve its previous taggings:
                    if is_single_token:
                        original_token_taggings = sentence[original_position][2]
                        tagged_expression[2].extend(original_token_taggings)
                    tag_sentences.append(tagged_expression)
                    tagged = True
                else:
                    j = j - 1
            if not tagged:
                tag_sentences.append(sentence[i])
                i += 1
        return tag_sentences
```

Here is an example our message of Tagging the POSTag with DictTag:
```sh
In [18]: from gitter.dict_tagger import DictionaryTagger

In [19]: dicttagger = DictionaryTagger(['gitter/question.yml', 'gitter/ignore.ym
    ...: l'])

In [20]: dict_tagged_sentences = dicttagger.tag(pos_tagged_sentences)

In [21]: dict_tagged_sentences
Out[21]: 
[[('Sorry', 'Sorry', ['NNP']),
  ('for', 'for', ['IN']),
  ('a', 'a', ['DT']),
  ('silly', 'silly', ['JJ']),
  ('question', 'question', ['NN']),
  ('but', 'but', ['CC']),
  (',', ',', [',']),
  ('while', 'while', ['IN']),
  ('working', 'working', ['VBG']),
  ('on', 'on', ['IN']),
  ('an', 'an', ['DT']),
  ('issue', 'issue', ['NN']),
  (',', ',', [',']),
  ('once', 'once', ['RB']),
  ('i', 'i', ['JJ']),
  ('find', 'find', ['VBP']),
  ('what', 'what', ['question', 'WP']),
  ('the', 'the', ['DT']),
  ('error', 'error', ['NN']),
  ('is', 'is', ['VBZ']),
  ('and', 'and', ['CC']),
  ('fix', 'fix', ['VB']),
  ('it', 'it', ['PRP']),
  (',', ',', [',']),
  ('how', 'how', ['question', 'WRB']),
  ('do', 'do', ['VBP']),
  ('i', 'i', ['RB']),
  ('check', 'check', ['VB']),
  ('if', 'if', ['IN']),
  ('it', 'it', ['PRP']),
  ('works', 'works', ['VBZ']),
  ('or', 'or', ['CC']),
  ('not', 'not', ['RB']),
  ('.', '.', ['.'])],
 [('For', 'For', ['IN']),
  ('eg', 'eg', ['NN']),
  (':', ':', [':']),
  ('-', '-', [':']),
  ('Once', 'Once', ['RB']),
  ('i', 'i', ['JJ']),
  ('find', 'find', ['VBP']),
  ('the', 'the', ['DT']),
  ('fix', 'fix', ['NN']),
  ('for', 'for', ['IN']),
  ('the', 'the', ['DT']),
  ('issue', 'issue', ['NN']),
  ('coala/coala', 'coala/coala', ['NN']),
  ('#', '#', ['#']),
  ('5166', '5166', ['CD']),
  (',', ',', [',']),
  ('how', 'how', ['question', 'WRB']),
  ('do', 'do', ['VBP']),
  ('i', 'i', ['RB']),
  ('check', 'check', ['VB']),
  ('if', 'if', ['IN']),
  ('it', 'it', ['PRP']),
  ('works', 'works', ['VBZ']),
  ('or', 'or', ['CC']),
  ('not', 'not', ['RB']),
  ('?', '?', ['question', '.'])]]
```

**A simple sentiment measure**

We could already perform a basic calculus of the questionness or ignoreness of a message.

Simply counting how many question and ignore expressions we detected, could be a (very naive) sentiment measure.

```python
def value_of(sentiment):
    if sentiment == 'ignore':
        return 1
    if sentiment == 'question':
        return -1
    return 0

def sentiment_score(text, review):
    return sum(
        [value_of(tag) for sentence in tagged_sentences(
            text) for token in sentence for tag in token[2]])
```
Now we can find the message_score for message and based on that we can decide which message is a question or which message is an answer. e.g. If the message_score is positive we ignore those messages, and if the messages score is negative, it goes into question and if the messages score is zero it goes into answers.

Here is an example of our message:
```sh
In [26]: from gitter.messages import message_score

In [27]: message = {
    ...:         "identifier": "5b588269c0fa8016e7379191",
    ...:         "room": "coala/coala/offtopic",
    ...:         "sent_at": "2018-07-25 14:00:09.456000+00:00",
    ...:         "sent_by": "Naveenaidu",
    ...:         "text": "Sorry for a silly question but, while working on an is
    ...: sue, once i find what the error is and fix it,how do i check if it work
    ...: s or not. For eg:- Once i find the fix for the issue coala/coala#5166 ,
    ...:  how do i check if it works or not?"
    ...:     }
    ...:     

In [28]: message_score(message)
Out[28]: -4
```
As you can see the message score is negative, so this message must be saved into the Question model.

>That's it, Thank you for reading, and as I mentioned above all the code can be found at https://github.com/coala/community/pull/145 and you can see how I have applied the whole theory in project.
