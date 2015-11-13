---
layout: post
title: Svg sprites
permalink: /SVG-sprites
tags: AngularJS, SVG, sprite
author_id: sinisag
category: webapp
---

[(CSS) Sprite] (https://en.wikipedia.org/w/index.php?title=Sprite_(computer_graphics)&redirect=no#Sprites_by_CSS) is a method, where we combine multiple small images or graphic into one large image and access the needed graphic by specifying 'coordinates' on the big image.
The main reason behind it is to improve performance of website. To understand this better, we need to be aware of browsers technical limitations.

## Browser limitations

- Limited amount of concurrent connections ([more] (http://www.browserscope.org/?category=network&v=top))
- Limited amount of concurrent connections to the same hostname

The second limitation we can tackle by serving assets from different hosts, but we are still limited by the amount of concurrent connections.

In addition every HTTP request comes with certain amount of overhead. We can partly address this by serving assets from cookie free domains (which should be the case regardless if you use sprites or not), but we can't fully prevent the overhead.

So generating sprites became a very popular way to overcome those limitations. With joining multiple icons/assets in to one big asset sheet, we download all related assets with one HTTP request.
Of course, there is some discussion about what makes sense to group in sprites, to download only needed assets and not something used rarely, but that discussion is out of the scope of this article.

Ok, using sprites is nice but why SVG sprites?

Imagine, you have a set of icons that you group in sprite and you have to support multiple devices (as we usually do) - desktop, iOS devices, mobile devices, tablets,... - with different screens. In that case, you can't use the same sprite if you wan to utilize screen specific advantages. For example, retina screens need assets double the size then other screens and serving bigger icons to screens that do not need them,
would cause an overhead. That is bad for multiple reasons, most obvious one being downloading more data then needed and therefore increase of download time.

Ok..so I create 2 sprite maps and serve them based on device type? Well, is not that simple, what if your icons change color based on context they are displayed in? Very quickly, you can end up with numerous amount of sprite files and complex logic to deal with them. At this point things can get out of hand very quickly.

## There is a better way!

Meet [SVGs] (https://en.wikipedia.org/wiki/Scalable_Vector_Graphics)! SVG stands for Scalable Vector Graphic. Main advantage of SVGs is that they are, as name suggests, scalable vector graphics. That means they will be sharp on all type of screens and in all sizes - you need just one asset to achieve that. But it gets better, you can also change icon color, width, height (and much more!) through CSS properties. That means, that if you can have your repeatable icons in form of SVGs, then you have to make only one sprite and it will serve any purpose.

## Generate SVG sprite

Here, we will assume, that you already have assets in SVG format. You can take all your SVGs and convert them to symbols. You can use a gulp/grunt (or any other task) to do it. Or if you prefer to manage you icons with external tools, you can use something like [fontastic] (http://fontastic.me).

So let's assume we have 3 SVG icons.

facebook.svg

```xml
<svg width="30" height="30" viewBox="0 0 30 30" xmlns="http://www.w3.org/2000/svg" xmlns:sketch="http://www.bohemiancoding.com/sketch/ns"><title>social_facebook</title><desc>Created with Sketch.</desc><path d="M0 15c0 8.285 6.715 15 15 15s15-6.715 15-15c0-8.284-6.715-15-15-15s-15 6.716-15 15zm18.552-4.635h-2.254c-.268 0-.564.35-.564.819v1.629h2.819v2.321h-2.819v6.971h-2.662v-6.971h-2.414v-2.321h2.414v-1.366c0-1.959 1.36-3.553 3.226-3.553h2.254v2.471z" sketch:type="MSShapeGroup"/></svg>
```

google.svg


```xml
<svg width="30" height="30" viewBox="0 0 30 30" xmlns="http://www.w3.org/2000/svg" xmlns:sketch="http://www.bohemiancoding.com/sketch/ns"><title>social_google</title><desc>Created with Sketch.</desc><path d="M13.588 10.758c-.377-1.328-.987-1.72-1.934-1.72-.103 0-.205.013-.304.042-.411.116-.736.458-.918.967-.185.516-.198 1.054-.036 1.668.288 1.094 1.066 1.888 1.851 1.888.102 0 .204-.014.303-.041.858-.242 1.395-1.552 1.039-2.804zm1.412-10.758c-8.284 0-15 6.716-15 15 0 8.285 6.716 15 15 15 8.284 0 15-6.715 15-15 0-8.284-6.716-15-15-15zm-.864 21.44c-.942.458-1.957.508-2.349.508l-.124-.002-.076.001c-.612 0-3.661-.14-3.661-2.917 0-2.729 3.32-2.941 4.338-2.941h.027c-.588-.784-.466-1.576-.466-1.576l-.22.007c-.383 0-1.122-.061-1.758-.471-.778-.5-1.17-1.351-1.17-2.53 0-3.329 3.635-3.464 3.671-3.464h3.632v.079c0 .407-.729.485-1.228.552-.167.023-.506.059-.603.108.92.489 1.068 1.262 1.068 2.412 0 1.308-.512 2.001-1.056 2.487-.336.301-.601.538-.601.854 0 .31.364.629.784.999.69.606 1.635 1.435 1.635 2.829 0 1.441-.62 2.474-1.843 3.067zm7.939-6.454h-2.369v2.368h-1.5v-2.368h-2.368v-1.5h2.368v-2.369h1.5v2.369h2.369v1.5zm-9.852 1.813l-.245.008c-.695.051-1.334.311-1.798.733-.46.415-.694.938-.66 1.471.07 1.109 1.262 1.761 2.711 1.655 1.426-.103 2.375-.922 2.306-2.033-.067-1.046-.974-1.834-2.314-1.834z" sketch:type="MSShapeGroup"/></svg>
```

twitter.svg

```xml
<svg width="30" height="30" viewBox="0 0 30 30" xmlns="http://www.w3.org/2000/svg" xmlns:sketch="http://www.bohemiancoding.com/sketch/ns"><title>social_twitter</title><desc>Created with Sketch.</desc><path d="M15 0c-8.284 0-15 6.716-15 15 0 8.285 6.716 15 15 15 8.285 0 15-6.715 15-15 0-8.284-6.715-15-15-15zm6.101 12.288l.008.383c0 3.903-2.972 8.405-8.406 8.405-1.668 0-3.22-.489-4.528-1.328.231.027.466.042.704.042 1.385 0 2.658-.473 3.67-1.266-1.292-.023-2.385-.877-2.759-2.051.18.034.365.053.556.053.269 0 .531-.036.778-.104-1.351-.271-2.37-1.466-2.37-2.896l.001-.038c.398.222.853.354 1.337.371-.793-.531-1.314-1.434-1.314-2.46 0-.541.145-1.048.4-1.485 1.457 1.788 3.634 2.964 6.089 3.088-.051-.217-.076-.442-.076-.674 0-1.631 1.322-2.954 2.954-2.954.85 0 1.617.359 2.156.933.674-.133 1.305-.379 1.877-.718-.222.69-.69 1.269-1.3 1.635.598-.071 1.167-.23 1.697-.465-.397.591-.897 1.113-1.475 1.529z" sketch:type="MSShapeGroup"/></svg>
```

Our output would be


```xml
<svg xmlns="http://www.w3.org/2000/svg" display="none">
    <symbol id="icon-social-facebook" viewBox="0 0 512 512">
        <path d="m0 256c0 141 115 256 256 256s256-115 256-256-115-256-256-256-256 115-256 256zm317-79h-39c-4 0-9 6-9 14v28h48v39h-48v119h-46v-119h-41v-39h41v-24c0-33 23-60 55-60h39z"/>
    </symbol>
    <symbol id="icon-social-google" viewBox="0 0 512 512">
        <path d="m232 184c-7-23-17-30-33-30-2 0-4 0-5 1-7 2-13 8-16 16-3 9-3 18-1 29 5 19 19 32 32 32 2 0 3 0 5-1 15-4 24-26 18-47zm24-184c-141 0-256 115-256 256s115 256 256 256 256-115 256-256-115-256-256-256zm-15 366c-16 8-33 9-40 9h-2-1c-11 0-63-3-63-50s57-50 74-50h1c-10-14-8-27-8-27h-4c-6 0-19-1-30-8-13-9-20-23-20-43 0-57 62-60 63-60h62v2c0 7-13 8-21 9-3 1-9 1-11 2 16 8 19 22 19 41 0 23-9 34-18 43-6 5-11 9-11 14 0 6 7 11 14 17 12 11 28 25 28 49s-11 42-32 52zm136-110h-41v40h-25v-40h-41v-26h41v-40h25v40h41zm-168 31h-5c-11 1-22 5-30 12s-12 16-12 25c2 19 22 31 47 29 24-2 40-16 39-35-1-18-17-31-39-31z"/>
    </symbol>
    <symbol id="icon-social-twitter" viewBox="0 0 512 512">
        <path d="m256 0c-141 0-256 115-256 256s115 256 256 256 256-115 256-256-115-256-256-256zm104 210v6c0 67-50 144-143 144-29 0-55-9-77-23 3 1 7 1 12 1 23 0 45-8 62-22-22 0-41-15-47-35 3 1 6 1 10 1s9-1 13-2c-23-4-41-25-41-49v-1c7 4 15 6 23 7-13-10-22-25-22-42 0-10 2-18 7-26 24 31 62 51 104 53-1-4-2-8-2-12 0-27 23-50 51-50 14 0 27 6 36 16 12-2 23-7 33-12-4 11-12 21-23 28 11-2 20-4 29-8-6 10-15 19-25 26z"/>
    </symbol>
</svg>

```

And that is already our SVG sprite. Now we just have to know how to reference it. Cool thing about SVG elements is, that we can reference them by id (for with CSS sprites, you need to specify 'coordinates' of specific icon as background position), so to get a Facebook icon, we can just do


```xml
<svg class="svg-icon">
    <use xlink:href="#icon-social-facebook"></use>
</svg>

```

We can apply some CSS properties to it (to svg-icon class) and style it the way we want and that would be it. With that little change, you can improve the way you handle your assets and make it much simpler to use in different context.


## Gotchas - is not always roses

- Internet Explorer (of course) will not load SVGs if you have them in external file (not embedded in template itself)
- Safari will not load your icons if they are referenced before they are defined (embedded after called)
- < IE9 is not supporting SVGs
- Using `<base>` changes behavior in Firefox (more below)

Currently, we are embedding SVG sprites in our template, just after opening `<body>` tag for good cross browser support. If you want to keep you resources out of your template, you can use a great shim written by Jonathan Neal [svg4everybody] (https://github.com/jonathantneal/svg4everybody).

And if you need to support older browsers (IE8 etc.) you can use image as fallback.

## Examples

[Here] (http://5minfork.com/SinisaG/svg-blog-demo) are a few examples of browser glitches [[source code] (https://github.com/SinisaG/svg-blog-demo)].

## Base tag

If you are like us and use [AngularJS] (https://angularjs.org/) everything written above will apply if you are using urls with hashbang (#!). But if you are using pretty urls with `<base>` tag or you use this tag for any other reason, then be aware, that Firefox is interpreting this differently then other browsers, so you will need to reference SVGs with an absolute url. For AngularJS or similar environments we suggest using a directive that keeps urls up to date:


```javascript
angular
    .module('MySvgModule')
    .directive('ofSvgIcon', ofSvgIcon);

function ofSvgIcon ($location) {
    return {
        restrict: 'EA',
        scope: {
          icon: '='
        },
        link: function (scope, element) {

            // you can modify your url here if needed
            var getSvgUrl = function (icon) {
                    return $location.absUrl() + icon;
            };

            //update xlink:href
            var setSvgPath = function () {
                element[0].setAttributeNS("http://www.w3.org/1999/xlink","xlink:href", getSvgPath(scope.icon));
            };

            // set icons on load
            setSvgPath();

            // subscribe to update event
            $rootScope.$on('update.svg', setSvgPath);
        }
    };
}

```


Additionally, you have to trigger path update anytime url changes (we use ui.router, event might be called differently if you use different routing). You can also put this straight in to your directive and leave out `update.svg` event, but we think it is good practice to add one more level of abstraction. It makes your code more immune to router changes - if you change the router and event name changes, you have to adjust your code only in one place.

```javascript
$rootScope.$on('$stateChangeSuccess', function () {
    $rootScope.$broadcast('update.svg');
});
```

Usage (you can also make template part of directive):

```xml
<svg class="icon-nav-info">
    <use of-svg-icon icon="'#icon-nav-admin'"></use>
</svg>
```

That has proven to work for us in all browsers we tend to support (IE10+, if you use IE9, you need to include [svg4everybody] (https://github.com/jonathantneal/svg4everybody)). Live example - [Onefootball - best app for football] (https://www.onefootball.com) ;)

I hope this clears sprites and SVG sprites for you.

If you want to learn more, here are some useful resources:

- [SVG sandbox] (https://github.com/metamn/svg)
- [Tips for designers] (http://sarasoueidan.com/blog/svg-tips-for-designers/)
- [SVG creation techniques] (https://24ways.org/2014/an-overview-of-svg-sprite-creation-techniques/)
- [SVG specs] (https://developer.mozilla.org/en-US/docs/Web/SVG)


Happy performance improving!