---
layout: post
title: Twitter contest joiner bot
description: ""
modified: 2018-03-09
tags: ["Projects"]
categories: ["bots"]
image:
    feature: P_twitter-bot/banner.jpg
---
### Automate the process of joining giveaway contests on Twitter :star2::star2::star2:

<figure>
	<a href="{{ site.url }}/images/P_twitter-bot/cool_stuff.jpg"><img src="{{ site.url }}/images/P_twitter-bot/cool_stuff.jpg" width="250px;"></a>
	<figcaption style="text-align: center;">Some cool stuff you can win if your lucky</figcaption>
</figure>
{: .image-right}

The inspiration for this post is drawn from this <a target="_blank" href="https://www.hscott.net/twitter-contest-winning-as-a-service/">article</a>. 
<br> A truly fun project I just had to try myself, so here it is; my own little version of the Twitter bot.

In order to use this bot, you will first have to sign up for the <a target="_blank" href="https://apps.twitter.com/">Twitter API</a>.
This should only take about 5 minutes. Get your keys, secrets, and token and fill out the required fields in the script.

<!-- more -->
<!--more-->

<br>

#### Python 3.x   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   <a target="_blank" href="https://gist.github.com/Sebastian-Nielsen/f57244bad3882b0232c04ef4c775cb29">GitHub gist</a>
{% highlight python %}
import tweepy, random, colorama
from time import sleep
from colorama import init, Fore, Back, Style

consumer_key    = '' # WRITE_HERE
consumer_secret = '' # WRITE_HERE
access_token    = '' # WRITE_HERE
access_secret   = '' # WRITE_HERE
your_username   = '' # Write your Twitter username here

auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_secret)
api = tweepy.API(auth)

search = '#rt ' + random.choice(hashtags)
print('Searching for:', search)
init()

for tweet in tweepy.Cursor(api.search, q=search, rpp=100).items():
   print(ctime())
   print(Back.MAGENTA+Style.BRIGHT+Fore.WHITE + 
		 '\n\nFound tweet by: @'+tweet.user.screen_name)
   print(Back.GREEN + str(tweet.text.encode('UTF-8')))

   if tweet.user.screen_name == your_username:
       print('Found tweet posted by bot, skipping..')
       continue
   sleep(4)
   try:
       if (tweet.retweeted == False) and (tweet.favorited == False):
           tweet.retweet()
           print(Fore.BLUE+Style.BRIGHT+'retweeted')
           sleep(10 * random.uniform(0.6, 1.8))
           tweet.favorite()
           print('liked')
       if tweet.user.follow() == False:
           sleep(12)
           tweet.user.follow()
           print('Followed user')
       else:
           print('Already following')

       print(Fore.YELLOW+'----sleeping----')
       sleep(900 * random.uniform(1.5, 1.7))
       
       # 10% chance to update the status 
       # (I suppose it make the bot seem more human)
       if random.choice([0,0,0,0,0,0,0,0,0,1]):
           text = random.choice(update_messages)
           print(Fore.RED+'Updater status med teksten:\n'+text)
           api.update_status(text)
           sleep(60)
		
   except tweepy.TweepError as e:
        sleep(900 * random.uniform(1, 1.5))
        print(e)
   except ReadTimeoutError as e:
        print(e)
{% endhighlight %}

You can experiment with the amount of time the bot should wait between each retweet/like/follow. 
But I wouldn’t advice you to decrease the sleep timer too much, since a too low sleep timer 
dramatically would increase the likely-hood of Twitter recognizing you as a spam bot;
Which obviously is something you would not want to happen. 

That said, you are free to do whatever you want with the code. Good luck! &nbsp;:four_leaf_clover::four_leaf_clover:
 

