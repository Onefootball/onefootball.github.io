---
layout: post
title: Environment-dependent configuration files with AngularJS and Grunt
permalink: /environment-dependent-configuration-files-with-angularjs-and-grunt
tags: javascript, angularjs, grunt, configuration
author: WebApp Team
category: webapp
---

You don't necessarily want to enable analytics tracking when testing your application on your local machine, nor do you want to log all of the debug messages on your production server. Here is a simple solution to handle multiple application configurations depending on the environment it's running in.


## The setup

  * An [AngularJS](https://angularjs.org/) application
  * A working [Gruntfile](http://gruntjs.com/)
  * [lodash](https://lodash.com/) and [ngConstant](https://github.com/werk85/grunt-ng-constant)


## The configuration file(s)

Here is an example of a simple configuration file living in `./app/config/config.json`

```
{
    "default_lang": "en",
    "debug": true,
    "analytics": {
        "key": "5afe-0209-d34d-b33f-1337",
        "enabled": true
    },
    "api": {
        "data_source": "https://data.example.com/:v1/:object/:id"
    }
}
```

We want to be able to extend that configuration file to adapt some of its values to the current environment. The first step is to add a key defining that environment:

```
{
    "env": "/!\ this file will be merged with a profile file by grunt and used to generate AppConfig",
    "default_lang": "en",
    "debug": true,
    "analytics": {
        "key": "5afe-0209-d34d-b33f-1337",
        "enabled": true
    },
    "api": {
        "data_source": "https://data.example.com/:v1/:object/:id"
    }
}
```

Then we'll create additional files containing the data we want to change:

  * `/app/config/config.production.json`

```
{
    "env": "production",
    "debug": false,
    "analytics": {
        "key": "5afe-0209-d34d-b33f-1337",
        "enabled": true
    }
}
```

  * `/app/config/config.staging.json`

```
{
    "env": "staging",
    "analytics": {
        "key": "0209-1337-d34d-b33f-5afe",
        "enabled": true
    },
    "api": {
        "data_source": "https://data.staging.example.com/v1/:object/:id"
    }
}
```

  * `/app/config/config.dev.json`

```
{
    "env": "dev",
    "analytics": {
        "enabled": false
    },
    "api": {
        "data_source": "https://data.dev.example.com/v1/:object/:id"
    }
}
```

  * `/app/config/config.test.json`

```
{
    "env": "test",
    "analytics": {
        "enabled": false
    },
    "api": {
        "data_source": "https://data.test.example.com/v1/:object/:id"
    }
}
```


## The Gruntfile

What we want now is to concatenate those files and make their content available to our Javascript application through the `AppConfig` module.


### Concatenating JSON files

```
    var _ = require('lodash');

    // Load the config file matching the 'profile' parameter, returns the default config + values from that file.
    var buildConfig = function (profile) {
        var conf1 = './app/config/config.json';
        var conf2 = './app/config/config.' + profile + '.json';

        if (!grunt.file.exists(conf2)) {
            grunt.fail.warn('File ' + conf2 + ' doesn\'t exist.');
        }

        return _.merge(
            grunt.file.readJSON(conf1),
            grunt.file.readJSON(conf2)
        );
    };
```


### Building up AppConfig

{% raw %}
```
    // https://github.com/werk85/grunt-ng-constant
    ngconstant: {
        // Options for all targets
        options: {
            space: '  ',
            name: 'AppConfig',
            wrap: '{%= __ngModule %} \n\n' //' angular.module("AppConfig", []).constant("_", _);\n'
        },
        // Environment targets
        development: {
            options: {
                dest: '<%= yeoman.dist %>/config/config.js'
            },
            constants: {
                CONFIG: buildConfig('dev')
            }
        },
        staging: {
            options: {
                dest: '<%= yeoman.dist %>/config/config.js'
            },
            constants: {
                CONFIG: buildConfig('staging')
            }
        },
        production: {
            options: {
                dest: '<%= yeoman.dist %>/config/config.js'
            },
            constants: {
                CONFIG: buildConfig('production')
            }
        },
        test: {
            options: {
                dest: '<%= yeoman.dist %>/config/config.js'
            },
            constants: {
                CONFIG: buildConfig('test')
            }
        }
    },
    ...
```
{% endraw %}

Then register those tasks when needed, for example in developement:

```
    grunt.registerTask('build', [
        'clean:dist',
        'less:development',
        'ngconstant:development',
        'watch'
    ]);
```



## The application

Finally, in `app.js` we can access the configuration variables:

```
var app = angular.module('MyApp', ['AppConfig']);

app.config(['CONFIG', function (CONFIG) {

    // depends on the build environment
    console.log('env', CONFIG.env);

})];
```
