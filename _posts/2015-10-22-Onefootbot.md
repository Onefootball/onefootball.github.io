---
layout: post
title: Onefootbot - the Onefootball chat robot
permalink: /onefootbot-chat-robot
tags: hubot, chatops
author_id: dpahl
category: devops
---

We've recently heard a lot about [ChatOps](https://www.pagerduty.com/blog/what-is-chatops/) and are thrilled by the 
possibilities and chances. So we introduced Onefootbot as our company robot based on [Hubot](https://hubot.github.com/) 
connected via [HipChat](https://www.hipchat.com/). [github](https://github.com/) wrote [Hubot](https://hubot.github.com/) 
to automate their company chat room. Later they released it as open source. It is written in CoffeeScript on Node.js and 
can easily be extended or customized by your own scripts. The general idea behind it is to bring your tools into your 
conversations and use the chat bot to work with plugins and scripts so teams can automate tasks and collaborate, working 
better, cheaper and faster.

## How does Hubot integrate with your tools ?

Hubot is shipped with a small group of core scripts in order to do things like posting images, translating languages and 
integrating with Google Maps, etc. In order to extend these core functions you can choose from a number of 
[Community packages](https://github.com/hubot-scripts) and configure them according to your needs. The real fun happens 
when adding your own scripts. 

Hubot is especially useful in connection with API's and web hooks when it comes to DevOps activities. Possible use cases are

* Start a test run on the CI server
* Trigger a build process
* Deploy your app to staging and/or production

## How to run your own robot ?

In order to run your own robot you need to know a few basics. Hubot is following an event driven approach. It listens 
to specified events and executes a callback that is defined in your CoffeeScript source code. 

An event where it reacts to can be a

* message mentioning the robot in a chatroom or via a direct private message
* text pattern that is detected in any message of a room where the robot is connected to
* HTTP request from an external system

A callback can do things like

* reply to a message in the chatroom or in a private chat
* return a HTTP response
* trigger a new HTTP request
* execute a shell command
* whatever can be done with Node.js

Please have a look into the Hubot [documentation](https://hubot.github.com/docs/scripting/) in order to get started with 
your own hubot scripts.

## So what can Onefootbot do?

### A fun exercise

In order to identify the possibilities of a chat robot we started with a very simple use case. As in 
[Onefootball](https://www.onefootball.com/) everything is around football we wrote a small hubot script that would listen
to the word football in all of our open chat rooms and respond with a random football quote.

The main functionality of the script looks like this:

```coffee
  robot.hear /football/i, (res) ->
    res.send getRandomFootballQuote()

  getRandomFootballQuote = () ->
    quotes = [
      'Football is the ballet of the masses. - Dmitri Dmitriyevich Shostakovich',
      'Some people tell me that we professional players are soccer slaves. Well, if this is slavery, give me a life sentence. - Sir “Bobby” Charlton',
      'You don’t have to have been a horse to be a jockey. - Arrigo Sacchi',
      'I don’t believe skill was, or ever will be, the result of coaches. It is a result of a love affair between the child and the ball. - Roy Keane',
      'Soccer riots kill, at most, tens. Intellectuals’ ideological riots sometimes kill millions. - John Jay McCarthy',
      'The ball is round, the game lasts ninety minutes, and everything else is just theory. - Josef “Sepp” Herberger',
      'International football is the continuation of war by other means. - George Orwell',
      'In football everything is complicated by the presence of the opposite team. - Jean-Paul Sartre',
      'In football, the worst blindness is only seeing the ball. - Nelson Falcão Rodrigues',
      ...
      'The first 90 minutes are the most important. - Sir “Bobby” Robson',
      'A football team is like a beautiful woman. When you do not tell her, she forgets she is beautiful. - Arsène Wenger',
      'It is better to win ten times 1-0 than to win once 10-0. - Vahid “Vaha” Halilhodžic',
      'Football is a simple game; 22 men chase a ball for 90 minutes and at the end, the Germans win. - Gary Lineker',
      'We must have had 99 percent of the game. It was the other three percent that cost us the match. - Ruud Gullit',
      'Sometimes in soccer you have to score goals.- Thierry Henry',
      'At the World Cup I watched the game Austria against Cameroon. On the one side exotics, alien culture , wild rites - and on the other side : Cameroon ! - Dieter Nuhr',
      'The subjunctive is the enemy of the losers. - Jens Lehmann'
    ]

    num = randomNum(quotes.length)

    quotes[num]

  randomNum = (max,min=0) ->
    Math.floor(Math.random() * (max - min) + min)
```

Just imagine how much you can annoy your co-workers by doing this in a football based company :-). Basically we had to
change the behaviour so that the robot only responds with football quotes when asked directly ;-).

### A more serious use case

So far that was a lot of fun but of course we also had a more serious use case in mind when introducing Onefootbot. The
goal was to improve our on call process so that all people in our company are able to find out who is currently on call 
in case of an incident and additionally be able to track the status of an incident so he/she does not have to contact 
the engineer who is on call every time an update is needed.

#### Mash it up - What did already exist ?

We are managing our on call schedule via [pagerduty](https://www.pagerduty.com/) so we used the existing 
[pagerduty integration for Hubot](https://github.com/hubot-scripts/hubot-pager-me) in order to connect Onefootbot with 
pagerduty. Together with the [HipChat integration of pagerduty](https://www.pagerduty.com/docs/guides/hipchat-integration-guide/) 
we were able to achieve what we wanted. All employees of Onefootball are able to discover who is currently on call and to 
follow the status of an incident just by using the internal company chat. This is especially useful on weekends. Even so 
you might not have your laptop with you all the time you can simply use the HipChat client on your mobile phone in order
to check for the things mentioned above.

#### Something was missing ...

All that helped a lot to make our on call process and incident management more transparent to all members of our company. 
But we noticed pretty soon that we could also use the bot to achieve some improvements for the persons being on call 
themselves. We were missing some functionality in the pagerduty script for Hubot in order to check for the on call members 
if they are on call and how does their on call schedule look like. So we contributed the functionality to the mentioned 
pagerduty integration script by adding these [2 commands](https://github.com/hubot-scripts/hubot-pager-me/pull/48).

![_config.yml]({{ site.baseurl }}/images/2015-10/oncall-schedule.png)
