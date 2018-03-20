---
comments: true
date: 2018-03-19
layout: post
title: Text summarizer
categories: ["Natural language processing (NLP)", "Data analytics"]
tags: ["Python", "NLP"]
image:
    feature: P_Text-summarizer/summarize.png
---
Have you ever been given the task to read a long article and wished that a summary of it existed, so that you wouldn't 
have to read the whole god damn article? Well, you will soon be able to generate summaries in a matter of seconds..  

The text summarizer we are going to build in this post falls into the catagory of NLP. <br>Natural-language processing 
(NLP) is a field of artificial intelligence which enables computers to analyze and understand the human language. We 
are going to explore this field today by building our very own text summarizer.

<!-- more -->
<!--more-->

## The steps to build a text summarizer
1. Remove stop words (defined below)
2. Count the frequency of each word
3. Assign a score to each sentence depending on the words it contains
4. Build summary by adding every sentence above a certain score threshold

### What are stopwords?
Stop words are any words that does not add a value to the meaning of a sentence. They are 
the most common words in a language. Here are some examples of stop words:

`a` `above` `again` `all` `an` `any` `as` `be` `been` `being`

We usually remove stop words from the text as knowing their frequency does not help us decide, whether the sentence as a whole 
is important or not.

<div id="centerbox" >
  <h5>Hover the text to filter out stop words</h5>
  <article class="highlight interactive">
    <p>
		<span>Greed</span> has <span>poisoned</span> <span>men's</span> <span>souls,</span> has <span>barricaded</span> the <span>world</span> with <span>hate,</span> has <span>goose</span> <span>stepped</span> <span>us</span> into <span>misery</span> and <span>bloodshed.</span> <span>We</span> have <span>developed</span> <span>speed,</span> but we have <span>shut</span> ourselves in. <span>Machinery</span> that <span>gives</span> <span>abundance</span> has <span>left</span> <span>us</span> in <span>want.</span> <span>Our</span> <span>knowledge</span> has <span>made</span> <span>us</span> <span>cynical.</span>   
	</p>
	</article>
</div>


# Step 1 - removing stop words
We start off importing three libraries from the natural language toolkit (NLTK): 

{% highlight Python %}
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize, sent_tokenize
from nltk.stem.snowball import SnowballStemmer
{% endhighlight %}


#### Corpus
Corpus means a collection of text. It could be data sets of poems by a certain poet, bodies of work by a certain author, etc. 
In this case, we are going to use a data set of pre-determined stop words.

#### Tokenizers
Tokenizers devides a piece of text into a series of tokens (pieces). There are three different kinds of tokenizers: 
word, sentence, and regex tokenizers. 
Note that tokens in data analytics and cyber security are two completely different things.
<figure>
	<img style="margin-bottom: 0; margin-left: 25px;" width="180px;" src="{{site.url}}/images/P_Text-summarizer/stemmerExample.png" alt="example of a stemmer">
	<figcaption>The stem of waited, waits, and waiting is wait.</figcaption>
</figure>
{: .image-right}
#### Stemmer
The stem is the root of a word. A stem is a form to which affixes can be attached. 
See the example with wait to the right. 

<br> 
Now, let's create the stop words list and 
tokenize the text into tokens consiting of words. The `text` variable is the text of our choice, that we wish 
to analyse.

{% highlight Python %}
stopWords = stopwords.words("english") # Supports the Danish language too
words = word_tokenize(text)
{% endhighlight %}

# Step 2 - create a frequency table 

Creating the frequency table is straightward:

{% highlight Python %}
# Init dict to store word frequencies in
freqTable = dict()

for word in words:
    # Stem the word
    word = stemmer.stem(word.lower())
	
    # If the word isn't a stop word proceed
    if word in stopWords:
        continue
				
    # Increase freq of word with 1
    if word in freqTable:
        freqTable[word] += 1
    else:
        freqTable[word] = 1
{% endhighlight %}

# Step 3 - Assigning a sentence score
Now that we got our `freqTable`. We can now use it to figure out which sentences is the most relevant 
in terms of the overall message of our text. *"But why are sentences with more frequently accuring 
words more relevant"* - you might ask yourself. Good question.
<br>
Let's say we got a text conerning obesity, now the most frequently accuring words in this piece of 
text might be the following: `obesity`, `fast-food`, `commercials`, `children`.
Now, whenever a sentence is containing one of these words, it will automatically be weighted as more 
relevant, because it explicitly mentions one of the main subjects of the text. Comparatively, if there 
were to be a sentence containing a lot of the less frequent words. That sentence is most 
likely not as relevant regarding the overall message; thus a sentence we would like to avoid having 
in our summary.
 
Let's start by creating a list of sentence tokens and a dictionary which we will be using for storing 
the sentence value of each sentence. 

In order to be able to iterate over the freqTable starting from 
the highest frequent words
{% highlight Python %}
sentences = sent_tokenize(text)
sentenceValue = dict()
{% endhighlight %}

We go through every sentence and check if the text contains any of the words from our `freqTable`, 
if it's true we increase the `sentenceValue` with the respective `wordFreq`.

{% highlight Python %}
for sentence in sentences:
    for word, wordFreq in freqTable.items():
        if word in sentence.lower():
            if sentence in sentenceValue:
                sentenceValue[sentence] += wordFreq
            else:
                sentenceValue[sentence] = wordFreq
{% endhighlight %}

Great! We now have a dictionary containing a value of each sentence, all we need to do now is to 
compare each sentence's value with a threshold; in order to filter away the less relevant 
sentences. 




# Step 4 - Build the summary




Before we can build our summary. We first have to find/calculate a threshold. The threshold's job is 
to help us decide which sentences, we should add to our summary. The threshold should neither be too 
big nor small, so that we can get a summary that is just the right size.

As longer sentences have a higher chance of containing more valuable words, they thus  
an advantage over sentences with less words. Luckily, we can easily solve this by calculating the average 
word value of each sentence. Now, we just have to think of a good threshold, that it is 
capable of filtering just the right ammount of text away.
Our best bet for the most suitable threshold is:
the `average` of the sentences' `average word value`. 
<br>
Let's start by calculating the `average word value` of each sentence and store it in a list:

{% highlight Python %}
# List containing the average word value of each sentence
avg_wordValue_of_each_sent = []

for sentence in sentences:
    number_of_words = len( re.findall(r"[a-zA-Z_']+", sentence) )
    avg_wordValue.append( (sentenceValue[sentence] / number_of_words) )

# The average word value of all sentences
avg_wordValue_of_all_sent = sum(avg_wordValue_of_each_sent) / len(avg_wordValue_of_each_sent)
{% endhighlight %}

Excellent, now all we need to do is to compare each sentence to the average word value of all 
sentences. You may alter the threshold weight to your liking: <br>
`higher weight -> less text` and `lower weight -> more text`

{% highlight Python %}
# Threshold weight - I prefer 0.8 
weight = 0.8

summary = ''
for i, sentence in enumerate(sentences):
    # Add sentence to summary if the avg word value of sentence
    # is bigger than the avg word value of all sentences.
    if avg_wordValue_of_each_sent[i] > (weight * avg_wordValue_of_all_sent):
        summary +=  " " + sentence

# Display the summary
print(summary)
{% endhighlight %}

That's it! We now have a fully functional text summarizer. 
You can get the full code [here](https://gist.github.com/Sebastian-Nielsen/1cbb695423768d96e024333f1144d0e4).





# Let's test it




<div markdown="0"><a style="text-align: center; width: 100%;" onclick="toggle_visibility('toggle_1');" class="btn btn-info">Show sample text</a></div>
<div id="toggle_1" style="display: none;">
  <article class="highlight">
    <p>
The financial crisis of 2008-09 demolished the credibility of big business in the West. Afterwards executives kept out of the public eye. Instead of holding their annual meetings in grand buildings in Manhattan, Wall Street banks shifted them to smaller cities where fewer people would turn up—Jersey City for Goldman Sachs and New Orleans for JPMorgan Chase. Bosses kept schtum during congressional and parliamentary roastings. Those who spoke their mind paid a price. In 2011 Bob Diamond, the boss of Barclays, told Britons that the time for remorse was over. By 2012 he was fired. When Mitt Romney ran for president that year his business career was a liability.
<br>
The years of penitence ended in early 2017. After being shunned by the Obama administration, most executives were flattered when they were asked to break bread at the Trump White House, and to share ideas about how to reform the tax code and cut red tape. Elon Musk of Tesla, Jeff Immelt of General Electric and others joined advisory councils aimed at boosting the economy. Accusations of cronyism circulated from the start, however, and by August any proximity to Mr Trump became toxic. Most executives were relieved when the circus came to an end. 
	</p>
	</article>
</div>



<div markdown="0"><a style="text-align: center; width: 100%;" onclick="toggle_visibility('toggle_2');" class="btn btn-info">Show result</a></div>
<div id="toggle_2" style="display: none;">
  <article class="highlight">
	<h5>Weight = 1</h5>
    <p>
	 Afterwards executives kept out of the public eye. Bosses kept schtum during congressional and parliamentary roastings. Those who spoke their mind paid a price. By 2012 he was fired. The years of penitence ended in early 2017.
	</p>
	<h5>Weight = 0.8</h5>
	<p>
	The financial crisis of 2008-09 demolished the credibility of big business in the West. Afterwards executives kept out of the public eye. Bosses kept schtum during congressional and parliamentary roastings. Those who spoke their mind paid a price. In 2011 Bob Diamond, the boss of Barclays, told Britons that the time for remorse was over. By 2012 he was fired. The years of penitence ended in early 2017. Accusations of cronyism circulated from the start, however, and by August any proximity to Mr Trump became toxic. Most executives were relieved when the circus came to an end.
	</p>
	</article>
</div>





<div markdown="0"><a style="text-align: center; width: 100%;" onclick="toggle_visibility('toggle_3');" class="btn btn-info">Show average word value of each sentence</a></div>
<div id="toggle_3" style="display: none;">
<table>
	<tr>
		<th><strong>avg word value of sentence</strong></th>
		<th><strong>sentence text</strong></th>	
	</tr>
	<tr>
		<td>4.0</td>
		<td>By 2012 he was fired.</td>
	</tr>
	<tr>
		<td>2.75</td>
		<td>Afterwards executives kept out of the public eye.</td>
	</tr>
	<tr>
		<td>2.71</td>
		<td>The years of penitence ended in early 2017.</td>
	</tr>
	<tr>
		<td>2.5</td>
		<td>Bosses kept schtum during congressional and parliamentary roastings.</td>
	</tr>
	<tr>
		<td>2.12</td>
		<td>Those who spoke their mind paid a price.</td>
	</tr>
	<tr>
		<td>2.0</td>
		<td>Most executives were relieved when the circus came to an end.</td>
	</tr>
	<tr>
		<td>1.94</td>
		<td>In 2011 Bob Diamond, the boss of Barclays, told Britons that the time for remorse was over.</td>
	</tr>
	<tr>
		<td>1.89</td>
		<td>Accusations of cronyism circulated from the start, however, and by August any proximity to Mr Trump became toxic.</td>
	</tr>
	<tr>
		<td>1.69</td>
		<td>
The financial crisis of 2008-09 demolished the credibility of big business in the West.</td>
	</tr>
	<tr>
		<td>1.68</td>
		<td>Elon Musk of Tesla, Jeff Immelt of General Electric and others joined advisory councils aimed at boosting the economy.</td>
	</tr>
	<tr>
		<td>1.64</td>
		<td>When Mitt Romney ran for president that year his business career was a liability.</td>
	</tr>
	<tr>
		<td>1.31</td>
		<td>Instead of holding their annual meetings in grand buildings in Manhattan, Wall Street banks shifted them to smaller cities where fewer people would turn up—Jersey City for Goldman Sachs and New Orleans for JPMorgan Chase.</td>
	</tr>
	<tr>
		<td>1.13</td>
		<td>After being shunned by the Obama administration, most executives were flattered when they were asked to break bread at the Trump White House, and to share ideas about how to reform the tax code and cut red tape.</td>
	</tr>
</table>
</div>
<style>
	td, th {
    border: 1px solid #dddddd;
    text-align: left;
    padding: 8px;
}
</style>





<script type="text/javascript">
<!--
    function toggle_visibility(id) {
       var e = $("#" + id);
	   e.slideToggle();
	};
    
//-->
</script>
<style>
code.language-python span.c {
	color: rgb(0,200,0);
}
</style>







<style>

article.highlight {
  margin-bottom: 10px;
  width: auto;
  padding: 0 25px;
  font-size: 0.9em;
  
  color: #eeeeee;
  background: #000000;
  background: rgba(0, 0, 0, 0.5);
  border-radius: 5px;
  box-shadow: 0px 0px 10px #000000;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
  
  /* 
    all elements in this element will change 
    color through a 0.1 second lasting linear
    transition.
  */
  * {
    transition: color 0.1s linear;
  }
  
  // make sure span keeps this color

  
  p {
    margin: 0;
  }
  
  /*
    change the text-color on hover, the color 
    in span will not change due to !important.
  
    additionally, the .hover class on the 
    article.highlight element will achieve
    the same result.
  */

}
article.highlight.interactive span {
    color: #eeeeee !important;
  }
article.highlight.interactive:hover, article.highlight.interactive.hover {
    color: rgba(238,238,238, 0.2);
  }
</style>

<script>
$(function() {
  setTimeout(function() {
    $("article.highlight").toggleClass("hover");
    setTimeout(function() {
      $("article.highlight").toggleClass("hover");
    }, 2000);
  }, 1000);
});
</script>


