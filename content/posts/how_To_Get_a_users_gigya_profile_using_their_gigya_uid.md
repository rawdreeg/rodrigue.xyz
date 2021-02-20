---
title: "How To : Get a user's gigya profile using their gigya uid - Gigya Drupal 8/9"
date: 2021-02-20T19:43:05+02:00
draft: false

author: ""
authorLink: ""
description: "How To : Get a user's gigya profile using their gigya uid - Drupal 8/9"
license: ""
images: []

tags: [Tutorial, Drupal 8, Drupal 9, PHP, Gigya]
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

toc:
  enable: true
  auto: true
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

This is technical document to serve an implementation reference by Developer. We assume that you have the gigya PHP SDK and gigya raas drupal 8 module installed in your project.

We've had multiple issues with getting user's profiles or showing users information on the site – This is because of the limitation of the[ Gigya Client Side - Web SDK ](https://developers.gigya.com/display/GD/Web+SDK). The user information have to be queried server side and this is how:

## Step-by-step guide

- You need to instantiate the helper class:

  ```php
    $helper = new Drupal\gigya\Helper\GigyaHelper();
    $api =  $helper->getGigyaApiHelper();
  ```

-  You can then fetch the user Gigya Account by calling fetchGigyaAccount($uid) this takes the gigya user id as a parameter

  

  ```php
  // The gigya UID is the drupal user uuid.
  // Assuming the you have the user account interface loaded this can be fetched like this.
  
  $uid = $account→uuid();
  $gigya_user = $helper->getGigyaApiHelper()->fetchGigyaAccount($uid);
  ```

  

- FetchGigyaAccount throws an exception:

  ```php
  Drupal\gigya\CmsStarterKit\sdk\GSApiException
  ```

  
  Remember to catch this in your code to avoid critical errors.

  