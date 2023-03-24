---
title: "inline_template vs #markup in Drupal"
date: 2023-03-24T13:03:09+02:00
tags: [Drupal, PHP]
draft: false
---

Imagine you want to render our text in your form. In the past, I would pass in the `#markup` index in my render array with some HTML as value. For example

``` PHP

$output['my_button'] = [
  '#markup' => '<div> <button id="generate-images">Generate Images</button></div>',
  '#allowed_tags' => ['div', 'button'],
];

```

This works for the most part. Especially when all you need is to render out a straightforward markup.

I was working on the [openai_image_for_drupal](https://github.com/rawdreeg/openai_image_for_drupal) drupal module with my friend, Joel. And we wanted the button
to have some additional attribute that would be defined in some configuration. So, `#markup` started to feel a bit inadequate. And that's when Joel introduced me to the [`inline_template`](https://www.drupal.org/node/2311123) render #type.


 The `inline_template` render type supports Twig syntax and lets you define Twig variable in the `#context` array index. The same example as above can be written as:
 
 ``` PHP
 
 output['my_button'] = [
      '#type' => 'inline_template',
      '#template' => '<div> <button id="image-generate-images" data-n="{{number_of_images}}" data-size="{{image_size}}">Generate Images</button></div>',
      '#context' => [
        'number_of_images' => $this->getSetting('number_of_images'),
        'image_size' => $this->getSetting('image_size'),
      ],
    ];
 
 ```
 
 This was pretty neat. And Feel free to check out [openai_image_for_drupal](https://github.com/rawdreeg/openai_image_for_drupal).
