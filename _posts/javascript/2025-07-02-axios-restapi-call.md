---
title: Axios RestAPI 호출
author: dejavuhyo
date: 2025-07-02 09:55:00 +0900
categories: [Language, JavaScript]
tags: [axios, axios-restapi, axion-api]
---

## 1. Axios

```javascript
import axios from 'axios';

function get() {
    axios.get('http://localhost:8080/get?title=foo&id=1')
        .then(response => {
            console.log("GET Response:", response.data);
        })
        .catch(error => {
            console.error("GET Error:", error);
        });
}

function post() {
    axios.post('http://localhost:8080/post', {
        title: 'foo',
        body: 'bar',
        userId: 1,
    })
        .then(response => {
            console.log("POST Response:", response.data);
        })
        .catch(error => {
            console.error("POST Error:", error);
        });
}

function put() {
    axios.put('http://localhost:8080/put', {
        id: 1,
        title: 'updated title',
        body: 'updated body',
    })
        .then(response => {
            console.log("PUT Response:", response.data);
        })
        .catch(error => {
            console.error("PUT Error:", error);
        });
}

function del() {
    axios.delete('http://localhost:8080/delete/1')
        .then(response => {
            console.log("DELETE Response:", response.data);
        })
        .catch(error => {
            console.error("DELETE Error:", error);
        });
}
```
