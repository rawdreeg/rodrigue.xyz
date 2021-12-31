---
title: Add captch in Drupal 8 Custom form using Securimage"
subtitle: ""
date: 2020-03-04T15:58:26+08:00
lastmod: 2020-03-04T15:58:26+08:00
draft: false
author: ""
authorLink: ""
description: "This is an easy guide to help you implement captcha to your Drupal 8 custom form using securimage"
license: ""
images: []

tags: [Tutorial, Drupal 8, PHP]
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
This is an easy guide to help you implement captcha to your Drupal 8 custom form using [securimage](https://github.com/dapphp/securimage) 

<!--more-->

Install lib through composer:

 ```bash
  composer require dapphp/securimage
  ```

  

We set up this method in a controller class:

```php
// Returns the captcha image
public function getCaptcha(){

 // Generate the captcha session
 $temp_store = \Drupal::service('user.private_tempstore')->get('my_module_name');
 $session_id = sha1(uniqid($_SERVER['REMOTE_ADDR'] . $_SERVER['REMOTE_PORT']));
 $temp_store->set('captcha_id', $session_id);

 // Configure the captcha
 $captcha_options = [
 'captchaId' => $session_id,
 'image_width' => 240,
 'image_height' => 70,
 'no_exit' => true,
 'use_database' => false,
 'send_headers' => false,
 'send_headers' => false,
 ];


 // Return the captcha image
 $captcha = new \Securimage($captcha_options);
 $response = new Response();
 $response->headers->set('Content-Type', 'image/png');
 $response->headers->set('Content-Disposition', 'inline');
 $response->setContent($captcha->show());

 return $response;
}

```



Create a route to hit generate image:

```yaml
my_module_name.captchaimage:
 path: /getcaptcha
  defaults:
 _controller: Drupal\my_module_name\Controller\MyController::getCaptcha
  requirements:
 _permission: 'access content'
```



on the Form build method render the image like this:



```php
// Add the captcha
$getBase = \Drupal::request()->getSchemeAndHttpHost();
$form['captcha'] = [
 '#type' => 'html_tag',
 '#tag' => 'img',
 '#prefix' => '<div class="row"><div class="col-md-4">',
 '#suffix' => '</div></div>',
 '#attributes' => [
 'id' => ['captcha'],
 'class' => ['my_captcha'],
 'src' => $getBase . '/getcaptcha', ],
];

$form['captcha_input'] = [
 '#type' => 'textfield',
 '#prefix' => '<div class="row"><div class="col-md-4">',
 '#suffix' => '</div></div>',
 '#attributes' => [
 'class' => ['captcha-text'],
 ],
];
$form['text']['#markup'] = "<a href='#' id='captcha_refresh'>Refresh</a>";

```

On the validate method you validate the form captcha as follow:

```php
// Validate the captcha
$captcha = new \Securimage();
if (!$captcha->check($form_state->getValue("captcha_input"))) {
 $form_state->setErrorByName('captcha_input', t("Wrong captcha"));
return false;

}
```

Handle the refresh button with javascript:

```javascript
// Captcha refresh

var captcha = document.getElementById("captcha");

if(captcha){
    jQuery("#captcha_refresh").click(function(e){
        d = new Date();
        captcha.setAttribute("src", siteBase + "/getcaptcha?" + d.getTime());
        e.preventDefault();
    });

}
```