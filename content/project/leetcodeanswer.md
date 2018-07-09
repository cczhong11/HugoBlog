---
title: "Leetcode Answer websites"
date: "2018-07-08"
description: "A website helps you get answers for leetcode "
categories:
    - "GCP"
    - "Leetcode"
    - "Github"
---

# The idea

It is popular for students to practise their coding skills on leetcode.com. It offers very good questions and good articles. However, it is not easy for people to learn from other good answers. Although it provides discussion, but people needs to go through threads by threads to find the right answers. I want to build a website **for myself** to find questions easily. 

**All codes are belong to Leetcode.com, it is not a good idea to redistibute them**. I totally agree with LeetCode terms of service.

> "Content" means all software, communications, images, sounds, and material perceived or made available from the Applications. Unless otherwise specified in writing, all of our content is owned, controlled, or licensed by us. Content means all software, images, questions, solutions, or any material associated with the service and website. All content is copyrighted under United States copyright laws and/or similar laws of other jurisdictions, protecting it from unauthorized use.

> You agree that all content is our sole and exclusive property. We may use all content for any purpose, including for commercial or promotional use without restriction or compensation to you. You agree not to copy, redistribute, publish or otherwise exploit any Content in violation of the intellectual property rights.


# Cost

Thanks to the cloud platform and open source technology, we could build a website without a server. If you just want a static website, you could just use [github pages](https://pages.github.com/). But if you want to build a website with backend, you need to have a server to response website requests. The easiest way is to use the following tools:

- Google Domains
- Github pages + ReactJS
- Google Cloud Platform Function services
- Google Cloud Platform Datastore

![](leetcodeanswer.png)

Domain costs me $12 per year. The github pages and ReactJS are free. GCP Function is charged the times it executed, $0.40/million. For Datastore, it is free for 1GB and daily read and write under 50000 and 20000. Therefore, it almost cost me nothing. 

# Building
## Web crawler

In the beginning, I looked into requests from Leetcode.com from debug mode in Chrome and figure out the pattern. Then I wrote a python code to download all documents I needed to local.

Here I do not want to write more information about this procedure, because it has potential to violate the terms of service.

## Datastore

After getting enough documents, I designed the 3 tables to save all the data. 

- question to language: name is question id, the content is an array of all available languages.
- question and language to runtime: name is the question+language, content is all possible runtime.
- question and language and runtime to code: name is question+language+runtime and content is real solutions.

It is also possible to only have one table and write good query sentence. The reason for me to have three tables instead of one table is because if there will be a large flow in the future, lots of search query will be the same. Therefore, I will use space to trade off time. 

## Functions

In this part, I used JavaScript to query the datastore. In the beginning, I searched a lot about how to connect 

