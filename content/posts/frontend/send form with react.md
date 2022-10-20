---
title: "Submit data with React"
date: "2020-10-21"
description: "Write correct way to submit data with react"
categories:
    - "react"
---


# Send data with React

This looks like a very simple topic for React aplication. But I met some problems during implementation. The main topic I would like to discuss here is CORS and Form submit.

## What is CORS

It is Cross-Origin Resource Sharing and it is a standard of W3C. It allows browser to send cross-origin requests. The main thing is the server needs to handle CORS interface.

There are two types of CORS:

- Simple request
- not-so-simple request

If one request meets the following requirements, it is simple request:

- request methods: HEAD, GET, POST
- HEADER only contains: Accept, Accept-language, Content-Type, Last-event-id, Content-Language:
  - Content type: only one of application/x-www-form-urlencoded, multipart/form-data, text/plain

For all other request, it is not-so-simple request.

We can see if we send data via a form, it is a simple request. If we want to send some RESTful API, it would be a post request with some json data in the body, which means that it is a no-so-simple request. 

## Send form data

This code snippet could be used to send form data. 

```js
import React from 'react';

export default function MyForm(){
    return (
      <form onSubmit={(event)=>{
        const form = event.target;
        const data = new FormData(form);
        fetch('/api/form-submit-url', {
            method: 'POST',
            body: data,
        });
      }}>
        <input
          name="username"
          type="text"
        />
        <input type="submit">Send data!</input>
      </form>
    );
  }
}

export default MyForm;
```

This looks very simple. But it would not work if we send a RESTFUL request.

## What happened if we only change in the onSubmit callback

As we know, send json requests with post is a no-so-simple request in CORS. So in the browser, it would happen this.

1. Browser send a preflight request first to ask the server, and it is a OPTIONS request
2. If get acknowledgements from the server, it would send the real request.

![](https://tczimg.s3.amazonaws.com/vscode/d149db8c6bec4970a27cd53d51854776.png)

reference: https://livebook.manning.com/book/cors-in-action/chapter-4/14

If we put restful requests in the `onSubmit`, when click the button with `type=submit`, the browser will send the preflight request and refresh the page. **So the actual request would never send.**

It is stuck me for a while, which means we could not use onSubmit to send restful requests.

## Send RESTful request 

The correct way to send RESTful requests is to use normal button to do it.


```js
import React, {useState} from 'react';

export default function MyForm(){
    const [username, setUsername] = useState("");
    return (
      <form >
        <input
          name="username"
          type="text"
          value={username}
          onChange={(event)=>{setUsername(event.target.value);}}
        />
        <button onClick=((event)=>{
            fetch('/api/form-submit-url', {
            method: 'POST',
            body: {username:username},
        });})>Send data!</input>
      </form>
    );
  }
}

export default MyForm;
```

## Remember to implement CROS in the server side

We can use the package to do it. 

- flask: [Flask-CORS](https://flask-cors.readthedocs.io/en/latest/)
- nodejs: [cors](https://www.npmjs.com/package/cors)