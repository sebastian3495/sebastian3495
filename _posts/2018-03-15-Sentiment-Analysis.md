---
comments: true
date: 2018-03-16
layout: post
title: Sentiment analysis of Danish texts (wordlist)
categories: ["Data science"]
tags: ["Data science", "Python", "Sentiment analysis"]
image:
    feature: P_sentiment-analysis-of-danish-texts/banner.png
---


### The process of determining the emotional tone behind a series of words :weary: :smiley:

Sentiment analysis — also known as opinion mining — is as simple as assigning each word in a text a value based on the bias 
of the word, after which the overall sentiment is decided by summarizing the values.
<br>
This is especially useful for larger companies, who wish to gain an insight in their customers opionions. However, students 
who have been giving the job to e.g. analyse a phrase, or locate all positiviely and negatively charged words, might 
find it just as useful.

<!-- more -->
<!--more-->

### What is a wordlist-based approach?
A wordlist-based approach bassically means, that we decide the polarity score of each word by looking up the word in a predefined dictionary
with scores already associated to each word. <br>

The good thing about it is that you can program it fairly easy with little to no code. However, it's not all rainbows and sunshine, it got its drawbacks.
Since the words are given a value based on the sentiment of the word individually, the context in wich the word appears is therefore not taken 
into consideration, which makes it bad at detecting things such as sarcasm or passive aggressiveness. For that reason, wordlist-based approaches is 
best suited for formal language as opposed to spoken language. 
<br> 
<br>
For more complex sentences a better choice would involve [training a model](https://www.nltk.org/), 
which calculates the polarity by forming a relationship between the words. However that said, it is 
still an execellent tool and extremely useful in certain scenarios.





# Let's get started

We will be using the `afinn` package to analyse a Danish text, as it supports the Danish language; although when it comes to English, `textblob` is
the preffered choice.
<br>

Anyways! We start off by instantiating an instance of the Afinn object, followed by a namedtuple that is going to require 3 values,
the polarity score of the word; the word itself; and the line number at which the word is located.

{% highlight Python %}
from afinn import Afinn # https://github.com/fnielsen/afinn   
from collections import namedtuple
afinn = Afinn(language='da', emoticons=True)
ScoreTuple = namedtuple('ScoreTuple', 'score word line')
{% endhighlight %}


Getting the sentiment score of a text is straight forward. It is worth taking into account, that it's a good practice
to clean the text from "special characters" before analysing it, as it might cause some troubles to for instance
 analyse a word such as: "extraordinary..?". <br>
 Note that it assumes `..?` is a part of the word.
{% highlight Python %}
def clean_text(text):
	'''Cleans the text from characters such as , . ! ? '''
	chars_to_clean = (',','.','!','?')
	for c in chars_to_clean:
		text = text.replace(c, '')
	return text

def sentiment_score_of_text(text, show_score=True):
	text = clean_text(text)    # Clean the text
	score = afinn.score(text)  # Get the score of the text
	
	if show_score:
		print(f"The text's score is: {score}")
		
	return score
{% endhighlight %}

Let's test the functions on the following text: 

{% highlight Python %}
          # A text with an overall positive attitude
>> text = """Dette er en dansk tekst, fyldt med positive og gode ord. Der er dog nogle dårlige og knap så
             gode ord ind i mellem. Men overordnet set er teksten positivt ladet med fantastiske himmerigske ord."""
>> sentiment_score_of_text(text)

"The text's score is: 5.0"

{% endhighlight %}

A higher score is equal to a higher confidence in that the text is positive, meanwhile a lower score is equal to a higher confidence
in that the text is conveying a negative sentiment. <br>
Now, let's take a closer look at each word individually using the following function:

{% highlight Python %}
def sentiment_score_of_words(text, ignore_neutral_words=False, show_analysis=True):
	text = clean_text(text)
	lineNr = 1
	scores = []
	lines = [line for line in text.split('\n')]

	for line in lines:
	    for word in line.strip().split(' '):
	        if (ignore_neutral_words and afinn.score(word) == 0):
	            continue
	        else:
	            scores.append( ScoreTuple(afinn.score(word), word, lineNr) )
	    lineNr += 1

	if show_analysis:
	    for nTuple in sorted(scores, reverse=True):
	        print(f"{nTuple.score} \"{nTuple.word}\", linje: {nTuple.line}")

	return scores
	
    # We set the ignore_neutral_words argument to True, 
    # as we do not want to print out the neutral words
>> sentiment_score_of_words(text, ignore_neutral_words=True)

"""
4.0 "fantastiske", linje: 2
2.0 "positivt", linje: 2
2.0 "positive", linje: 1
-3.0 "dårlige", linje: 1
"""
{% endhighlight %}

Not surprisingly, we only have a few words affecting our total text polarity score. <br>
But there you have it, a simple sentiment analysis using a wordlist-based approach wich took no more than 50 lines of code.
A simple yet rather effective way of creating a quick simple sentiment analysis.

Get the full code [here](https://gist.github.com/Sebastian-Nielsen/1cbb695423768d96e024333f1144d0e4).

#### Other use cases of sentiment analyses:
- [Sentiment analysis of Danish politicians on Wikipedia](https://finnaarupnielsen.wordpress.com/2013/01/09/more-on-automated-sentiment-analysis-of-danis/)
- [Sentiment analysis on Trump's tweets](https://dev.to/rodolfoferro/sentiment-analysis-on-trumpss-tweets-using-python-)
- [Exploring sentiment analysis as an application of text mining](https://dev.to/lornamariak/exploring-sentiment-analysis-o6j)

