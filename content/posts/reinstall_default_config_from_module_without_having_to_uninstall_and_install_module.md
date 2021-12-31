---
title: "Reinstall default config from module without having to uninstall and install module - Drupal 8/9"
date: 2021-02-20T19:29:42+02:00
draft: false

author: ""
authorLink: ""
description: "Reinstall default config from module without having to uninstall and install module - Drupal 8/9"
license: ""
images: []

tags: [Tutorial, Drupal 8, Drupal 9, PHP]
categories: ["Tutorials", "Drupal 8"]
featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false
code:
  copy: true
  # ...
math:
  enable: true
  # ...
mapbox:
  accessToken: ""
  # ...
share:
  enable: true
  # ...
library:
  css:
    # someCSS = "some.css"
    # located in "assets/"
    # Or
    # someCSS = "https://cdn.example.com/some.css"
  js:
    # someJS = "some.js"
    # located in "assets/"
    # Or
    # someJS = "https://cdn.example.com/some.js"
seo:
  images: []
  # ...
---

Uninstall and install module may delete configuration along with contents. This will allow you to recreate or install everything in the config/install folder of your custom module:



`\Drupal::service('config.installer')->installDefaultConfig('module', $module);`

<!--more-->

*where $module is your module machine name.



This can be run with drush eval or drush php.

ie: 

`drush php-eval "\Drupal::service('config.installer')->installDefaultConfig('module', 'my_module_name');"`