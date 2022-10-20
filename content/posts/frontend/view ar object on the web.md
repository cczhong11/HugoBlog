---
title: "Embed AR in your website"
date: "2021-01-18"
description: "Embed your 3D model in your website"
categories:
    - "AR"
---

It is fun to view AR object with your iPhone. In fact, it is not hard to embed the AR animation in your website.

Follow the following step and you can do it.

## Step 1: Prepare your usdz file

Create a 3D animation and export it as usdz file. You can use some tool like: [vectary](https://www.vectary.com/).

## Step 2: Put html code


```html
<a href="/path_to_asset/cup.usdz" rel="ar">
<img src="/path_to_asset/cup.jpeg"/>
</a>
```

## Step3: View it on your iOS device


Tap the image to view it in the argumented reality with an iOS device running iOS 12+.

{{< rawhtml >}}
<a href="https://tczimg.s3.amazonaws.com/ar/run.usdz" rel="ar">
<img src="https://tczimg.s3.amazonaws.com/ar/run.gif"/>
</a>
{{< /rawhtml >}}