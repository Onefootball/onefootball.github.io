---
layout: post
title: Asking users for help
permalink: /asking-users-for-help/
tags: [android, debugging]
category: Android
author_id: mbobzien
---

We recently struggled with a very annoying issue in our android app: It was extremely hard to debug because we were not able to reproduce it properly. Still we got quite a few reports about the bug from users, so we decided do ask them for help debugging the app. In this article I will describe our experiences. Even if you develop for other platforms than android, our experiences might be usefull for you. So keep reading!

##The Problem

Recently we had an issue which was pretty hard to figure out. Users started to report that their settings within the app would partly disappear. For some it was a permanent issue and for others it just happened once and then disappeared again. Even a couple of colleagues reported this problem but sadly for us and luckily for them, it disappeared immediately again, so this didn’t help. More and more reports arrived in our mailbox but we were not able to reproduce the issue.
The fact that we not only store the settings locally but also sync them with our backend added even more complexity to the problem. We checked our crash logs but there was nothing that could explain the problem. We checked with the backend if they had changed anything in the last weeks but they assured us nothing was touched. Plus, all other platforms worked fine so the issue must have been on our side. 
We started to check our code. First I checked alone but found nothing. Then I asked a colleague to go with me through the logic in case I was just blind. Still we found nothing and still more and more users seemed to encounter the problem.
This was a programmer's nightmare! A bug keeps getting reported but you don’t have any crash logs and you have no idea how to reproduce the problem.

##The Plan

Giving up was not an option, so we decided to ask those for help who actually could reproduce the problem: Our users!
Our plan was to create a special version of the app with extensive logging. We hoped that our users would be kind enough to install this version and by doing so, help us identify the issue.

##Have theories you want to verify

We had several suspicions / theories which we wanted to check:

- Race conditions in our cache. 
 - <i>This is a problem that typically occurs when you have multiple threads depending on a shared state and you failed to synchronize the different threads properly. The evil thing about this issue is, that it heavily depends on the execution order of your threads. Everything can go fine a hundred times but every once in awhile your threads randomly get processor time in a specific order which causes the bug to appear. And when you have several million users this will happen a lot even if it does not happen for you!</i>

- A logic problem which we just did not see when checking the code.

- Some faulty error handling while syncing with the backend.

- A server instance sending us corrupt data for some reason.

In general it seems to be helpful if you have at least some idea where you have vulnerable parts in your code. This makes it much easier to decide what to log.

##What do you want to log?

To be able to properly judge which theories if any are true and wich are faulty we decided to log the following data:

- The user id

- The exact time the log was made
- The overall uptime of the device at the time
 - This helps you to detect restarts of the device
- The screens the user is navigating to

- The different method calls which seem to be relevant (writing into the database, starting syncs etc.)
 - If you  suspect race conditions in specific method calls, you should log method start and method end.

- The thread names / ids on which the methods were executed
 - This helps you identify race conditions.

- The states of the user settings after a change

- The data we got from the backend

- The data we sent to the backend

####Important things you should keep in mind when you create logs:
- Log everything you need to identify your problem. If you forget something crucial you are in trouble! Once you send your version to the user, the only thing you can do, is create a new version. But how many times can you send a new version to a user, before he decides that he is getting very annoyed by you?

- Don’t log too much. At first we logged so much information that it was hard to read what was going on. By reducing the information to the important parts it became a lot easier.

- Choose nice and readable formatting. Invest a bit of time in this. The better the logs are formatted the better you will be able to process the information. If you want to be extra fancy think of some way you can automatically process your logs. But for us it was enough to read the logs the old fashion way.

- Be careful with sensitive information! You don’t want to leak private information of the user in any way, so be careful what you log and how you send it around.

##How to log

We decided to use our bus system which is available throughout the app. Wherever we want to log something we post a log event with the relevant data. This log event looks like this:


```java
public class DebugLogEvent extends Event
{
   private static Clock sClock;
   public final String methodName;
   public final long threadId;
   public final String threadName;
   public final String message;
   public long elapsedTime;
   public long uptime;

   public DebugLogEvent(String methodName, Object objectToLog)
   {
       Clock clock = sClock;
       if(clock == null)
       {
           clock = new Clock();
       }

       this.elapsedTime = clock.getElapsedTime();
       this.uptime = clock.getUptime();
       this.methodName = methodName;
       Thread currentThread = Thread.currentThread();
       this.threadId = currentThread.getId();
       this.threadName = currentThread.getName();
       this.message = String.valueOf(objectToLog);
   }

   public static void setClock(Clock clock)
   {
       DebugLogEvent.sClock = clock;
   }
}
```
In the constuctor of the DebugLogEvent we set the method name which we formatted like this: `{className}.{methodName}`. The object we passed provided us with the log message through the toString() method. This way we could either pass in a string or any other object which had a well defined toString() method. As you can see all the other fields like the different timeStamps and thread names are automatically filled inside the constructor. The Clock class is just a wrapper which gives us the system time etc.

This event is picked up by our logger which in turn writes the information into a file.

Once we detect our original issue we send a second event which triggers our logger to send the file with all the logs to us. After sending the data it clears the file and restarts the logging.

##The reaction

Once we were happy with our logs we sent the version to those users who had contacted us and hoped they would cooperate in installing our version. And they did!
More than 25% installed our special version within 24h and tried to reproduce the problem. And they did not only help us greatly by doing so, they seemed to be really happy that we actually cared about their problems. The reactions were extremely positive. It seemed by starting a dialogue with our users, it showed them, that they were able to contribute and that we really cared about what they had to say.


##The result

The results were pretty satisfying. In less then 24 hours after contacting the users we had enough logs to work with. We were able to identify several weak points in our code and also falsified a couple of our theories. This way we were able to identify the problem and fix our code. 
In the end we had identified a combination of different little issues like race conditions, some poorly designed parts and even one actually obvious bug which we just did not see in the beginning. I guess sometimes you are just blind.
To make sure our fixes had worked, we created a new version which we sent to the same users as before.
The next day we opened our mailbox and started to read the responses. It had worked! The users reported the issue being fixed and we were more than relieved.

##Conclusion

Contacting the users and asking them for help did not only help us find our issue faster, it also seems as if  the users greatly appreciate it, when they actually see that you work on the problem they are experiencing. 
For us it felt a bit like a defeat to ask for help but to the users it just showed that we care and that we try everything we can to solve the problem. 
No one wrote us a negative response and in the end I firmly believe, that it is even more likely now that those who have helped us solve the issue will stay loyal users of Onefootball.
Probably you should not ask the users for help every week but maybe we developers should keep in mind that we have this option and that it’s not a bad one at all.

Here are some responses we got from the users:

>“Hi,
Everything is OK now.
Thanks you!!”

-
>“Thanks so much for the feedback. Now it works absolutely fine! Cheers!
Another feedback is:
1. If at the games section, a calendar section could be added, based on our favorites for that month, that would be great. Rather having to go down and scroll for all the matches!
2. Apart from that, I love Onefootball :) And actively ask my friends to use it too ! “

-
>“Thank you very much for the feedback!
I really appreciate that you give some of your time to listen to our issues. One Football is by far the best football app in the market, so I'm glad that you were able to fix the problems.
Everything is perfect with the 7.4.2 version, so thanks again.
We'll be in touch.”

>...
