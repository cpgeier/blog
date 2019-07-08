---
title: "Real-time Social Media"
date: 2019-03-09
draft: false
---

# A nearly free way to host a website streaming Twitter data

[I built a nearly free website for streaming live Twitter data.](https://no-bs.ml/). 

I also knew that there are so many hosting services with a "community" or "hobby" tier and I wanted to see if I could build a cool website that doesn't cost money to host.

Please give me a star on [my Github](https://github.com/cpgeier) if you like my work!
<div style='position:relative; padding-bottom:56.25%'><iframe src='https://gfycat.com/ifr/powerfulfamousamericanbadger' frameborder='0' scrolling='no' width='100%' height='100%' style='position:absolute;top:0;left:0;' allowfullscreen></iframe></div>

## Motivation

I really like looking at data and I thought watching the first 2019 Democratic debates were pretty exciting. I wanted to see if it was possible for me to create a live stream of Tweets from twitter around the election. I thought it would be really cool, especially during debates, to see what people were tweeting about. 

## Twitter API

![](/img/twitter_streaming_api.PNG)

The Twitter API has a streaming API endpoint and I used this to get all the data that I use in the application. The [python-twitter](https://github.com/bear/python-twitter) python package was super useful for connecting to the api. 

To pipe data in constantly I deployed a Python script with Docker on Azure Container service. This is the only part of the tech stack that may cost money, however this part could be run from a Raspberry Pi. I used the Azure Student discount in this case so it didn't cost anything for me.

## Where to store Twitter data?

Elasticsearch is a great place to put Twitter data because the data can be easily searched or filtered. I was able to have my Elasticsearch server hosted for free through [https://bonsai.io/](https://bonsai.io/). Although they use an older version it works pretty well. 

To use the free Elasticsearch service I had to impliment a way to clear out old records in the server since I only have ~120mb on the free server version. I ended up just instructing my Python function to clear old records after the size gets over 100mb. 

## An API for Elasticsearch data

Now I needed a way to retrieve that data from Elasticsearch. Express is a fantastic backend and while I was searching for real-time data stream platforms I found [Socket.io](Socket.io) which seemed to have a really good support and documentation. 

I ended up following [this tutorial](https://auth0.com/blog/real-time-charts-using-angular-d3-and-socket-io/) to get up and running with Angular, D3, and Socket.IO. 

## Front end

For the front end I used Angular, Bootstrap and Ngx-Charts. Originally I used the D3 setup from the tutorial, but I figured D3 was too combersome to plot simple streaming data.

### Streaming Tweets

One of the major problems I ran into was trying to get the Twitter tweets to render and then disappear in a way that looked good. 

The reason why two tweets appear now isn't really intentional. Originally I wanted to have one tweet on the screen that instantly switches to a new tweet after a period. 

However since the only easy way to render tweets in the Twitter way is to use their Javascript which doesn't play nice with Angular. 

I ended up doing a really weird hack to get it to work and if I spend more time on this project I'll probably try to fix it. 

{{< highlight javascript>}}
      // @ts-ignore
      twttr.widgets.createTweet(
        this.tweetId,
        document.getElementById("tw-" + this.first)
      );
{{< / highlight >}}

The key was using this javascript to embed the tweet in the page rather than chaning the embed HTML. 



## Tools

All the tools I used are listed here:

1. Elasticsearch
    - Tweet storage
    - Ability to filter tweets
    - Deployed through Bonsai.io
2. Express & Socket.io
    - Maintains live connection to send data
    - Calls Elasticsearch every second for new data
    - Deployed through Azure Container Service
3. Angular & Bootstrap
    - Front end framework
    - Deployed through Heroku

Someother tools I used were:

- DockerHub - Free container builds
- Freenom - Free domain hosting

## Moving forward

The way I host my application (Heroku, Netlify, and Azure Container Service) allows me to have CD of the application as I make changes to it. I hope to containerize more of my services to make this project easier to host.

Also, in the future I'm hoping to add more live visualizations of specifically tweet keyword data like "Joe Biden" or "Donald Trump."

One of my main objectives with this site is to stay as unbiased as possible and so I'm hoping to add visualizations that go cater to all sides of the election.