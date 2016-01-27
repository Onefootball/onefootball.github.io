---
layout: post
title: CSS-essentials, Onefootball open source CSS project
permalink: /onefootball-open-source-css-essentails
tags: css, flex, onefootball, css-essentials, open source
author_id: sinisag
category: webapp
---

Working daily on our [onefootball webapp] (https://www.onefootball.com),
we come across a lot of different design challenges. As we all know, with complexity of design,
complexity of CSS rises.

In 2 years (it has just been 2 years of our first commit to webapp - yey!), we have written
quite a few CSS tricks and decided to gather them into one package.
That is how [CSS-essentials] (https://github.com/Onefootball/css-essentials) was born.

The main idea behind it is just publishing something that we find useful
and want to share within our projects. Also we believe that exposing code to community
can only make it better - two heads are better then one - and making your code public
motivates you to write clearer, more readable code.  

Currently, the package contains our most used feature, which is our grid (and some mixins).
We needed a grid which was 24 columns based and a lot of control over it (we have 6 different breakpoints)
and decided to write our own. Adding a desire to experiment with modern CSS to the mix,
our flex based, 24 columns grid was born. We use it in ["wild"] (https://www.onefootball.com) for over a year now
and have not experienced any major problems.

It is worth noting that our webapp tends to support **recent browsers** like  IE10+ and android browser 4.3+
(I list this two as bottom limitation, because..well..they are the special ones),
so if your want support for older browsers, this might not be the best choice for you.

However if you aim for new, modern browsers, then we encourage you to give it a try!
We appreciate every open github issue, every pull request and every high-five!

In the future, we expect to update our CSS-essentials with more cool stuff, but will
keep in mind to respect what the name suggests, and keep it lean and essential.

Package is available through bower and npm.

You can find more information as well as a complete documentation on the
[github page] (https://github.com/Onefootball/css-essentials) of the project
