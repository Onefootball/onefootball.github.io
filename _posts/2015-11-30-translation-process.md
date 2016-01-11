---
layout: post
title: Handling translations in a multi-platforms context
permalink: /handling-translations/
tags: translation,process
category:
author_id: adrienDeotto
---

Whether your Product is available on one platform or several, the question of how to handle translations is never easy. The reason of this difficulty is simple: translations require a very good coordination between different actors, at different stages of the Product development. People often tend to underestimate translations while they should actually be considered like proper features.

In this article I will not focus on why it is important to have good copies in your product to drive engagement, reflect your product’s quality and define your brand’s positioning, instead, I will talk about the tools and processes which helped us handle translations at Onefootball.



## The actors - who is involved in the translation process?

At Onefootball, the following actors are involved, successively:

* **Feature owner** – he/she is the responsible of a new feature and its definition, new copies included.
* **Native speaker** – we always have a native English speaker reviewing a new feature’s copies to sign them off. Ideally this person is part of the Marketing team and can also align the tone of voice of the final copies. 
* **Translation manager** – in our set-up, this role is covered by Technical Product Managers. They are responsible for preparing the ground for translation requests (we’ll see that later) and for co-ordinating the translators. At last, they are also responsible for making sure translations collected are propagated correctly to all platforms.
* **Translators** –they can either be internal or external resources to the company. The important is that they are native speakers.
* **Developers** – they are the one implementing the new features and the new copies. They are responsible for their platform-specific translation file.



## The process - how do those actors collaborate?

The translation flow is compounded of three phases:

* **Copy definition** – once the feature owner has defined the copies for the new feature, he passes them to the native speaker for sign-off. At the end of this step, the final copies are defined, reviewed and approved. They can be passed to the translation managers. English-UK is always used as a reference.
* **Translation management** – once they receive new copies, the translation managers add them in the collaborative translation tool (which we’ll see later). From there, they can easily request translations to the translators, monitor the progress and access the final outputs. At this point, English-UK should have been declined in as many languages as your product supports.
* **Integration** – developers are responsible for their translation file and should make sure to keep it updated on the collaborative translation tool. In our set-up each platform has its own project on our collaborative tool. Developers make sure to keep their project updated by uploading their platform’s translation file in English-UK  (AKA “root translation file”) to their project as soon as there are new copies. Then translation managers populate the translations collected earlier to platform-specific projects. At the end, developers can download the final translation files in one click.





## The tools – how to optimise the translation process?
 
We’re using Crowdin at Onefootball, to finally put a name on the collaborative translation tool I mentioned previously. 


### Features

In short, this tool allows for users to collaborate inside projects to translate files in different languages. Crowdin supports a lot of files formats including mobile application’s files format (json, xml, resw, etc.) and also provide an API that allows for uploading and downloading new copies\translations to a project easily. Each project has its root file (the app’s English translation file for example). Every time this file is updated, new un-translated copies appear across all languages configured in the project.

But more importantly, this tool works with a system of *translation memory*: every time a translator adds a translation in a project, it is added in this project’s translation memory that acts as a translation dictionary. In the end, all translations collected in a project’s translation memory can be transferred to any other project (regardless of the keys used in the key-value pairs in the translation files across projects). This feature is really useful when your Product is consistent and declined across several platforms since it will allow you to almost automatically keep maintaining consistent translations across all of them!


### Configuration

The way we configured Crowdin actually reflects the process described previously: we have four platform-specific projects (iOS, Android, Windows and Web) and a cross-platforms project that we use as a hub.

The cross-platforms project is the reference. It’s where the translation managers add the new app’s copies (updating project’s root file) and it’s also there that they are requesting translations. We used to ask translators to translate new app’s copies directly in platform-specific projects, but there are multiple advantages to have a separate, reference, project for managing the translations:

* It’s **easier** for your internal translators to always go to the same place for translating. You don’t want to demotivate your translators and lose time every time you need translations by asking them to translate new copies in 4 different places! In addition, people might end-up translating several times the same thing.
* It’s **more effective** for translation managers who can then invest on this project. The most important point when you request translations is to give context to your translators: this will ensure the quality of your translations. For example, we have the word “Table” in our app: if the person translating does not even know we’re a football application, he\she might translate it as a kitchen table! With Crowdin, we have the possibility to add some context to new copies by entering some notes or even by attaching screenshots underneath them.
* At last, having a reference project forces you to be as much **consistent** as possible when it comes to define new copies across your platforms. Consistency is a rule when you have 4 different platforms and in many cases like this one it also results in a gain of time and money having translators translating one copy instead of four.



### Integration in the translation process

In our set-up, developers are responsible for their own, platform-specific, project on Crowdin. They’re responsible for keeping their root file up-to-date and they’re responsible for informing the translation managers if they are missing some copies at the approach of a release.

As soon as translators have translated the new copies and developers have updated their root file (with the new copies in English), the translations managers use the Crowdin “auto-translate” feature that populate the newly collected translations from the reference project to all platform-specific projects.

In practice, this step requires, on Crowdin, a 100% match between the projects’ root files copies. For example, translations for the copy “Let’s start!” will not be applied to the Android project if Android uses the copy “Let’s start” (without an exclamation mark). In some cases, it is a good way to enforce all platforms to be consistent. In some others, it causes important limitations: some platforms have restrictions in regards to the way they declare their variables in strings. This aspect forces translation managers to manually adapt the translations for those copies in platforms-specific projects.

At the end, once translations are all populated in platform-specific projects, developers can download all the resulting translations files. They can do this either in a manual manner, by downloading a zip file including all the translation files, or in a more automatic way, by integrating Crowdin API in their development project and, with one command line, upload updates or download translations.




## Summary and conclusion

In a nutshell, the process is as follow:

1. A Feature owner defines a new feature.
2. He\she asks a native speaker to approve all the copies he defined.
3. Once the Feature owner gets its copies signed-off, he\she informs the translation manager. 
4. The translation manager adds the new copies in the “cross-platform” project on Crowdin.
5. A few days before a release, once copies from all features and platforms have been collected on the “cross-platform” project, the translation manager request translations to the translators.
6. Once all translations have been collected, and once developers have updated their platform-specific root file on Crowdin, the translation manager populates the translations to all platform-specific projects.
7. Developer can then download the translation files and update their owns.



As a conclusion, as many companies, and especially start-ups, we are dealing on a daily basis with the, never-be-underestimated, translation topic.
Translations can quickly result in a huge amount of time spending, particularly if you’re in a multi-platforms context and your product includes thousands of copies, but you will rarely be able to afford a dedicated person for it.

The solution we implemented is a process in which different actors collaborate in their area of responsibility to make the translation flow as smooth as possible.

At the end, the usage of translation tools can also dramatically improve the efficiency of this whole process by providing a common interface for all those actors to interact, by automating the numerous repetitive tasks and by supporting contextualisation features that ensure 
