---
title: "Fix the strong problem when fetch s3 object"
date: "2021-08-14"
description: "This blog will introduce how to fix the utf-8 encoding file in s3 problem"
categories:
    - "AWS"
---

# Fix the strong problem when fetch s3 object
## Problem

I used to have a Alexa skill to play the music from my s3 storage, but it did not work properly for a while. In the beginning, I thought it was code logic, but after digging into the logic I could not find the error.

The main problem is here

```js
responseBuilder.addAudioPlayerPlayDirective(
            playBehavior,
            podcast.url,
            enqueueToken,
            offsetInMilliseconds,
            expectedPreviousToken,
          );
```

It should play the music from the url, I logged the podcast url and I also tried from the browser, it worked fine. But it just could not play the music. 

## Debug

I used a pure s3 url and I found it worked. I used another url with Chinese character in it, it threw the exception, so I found the error.



## Solution

Encode the url with `encodeURI(url)` and we can get the correct url to fetch the s3 data.