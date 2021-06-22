---
title: jQuery 엔터키 이벤트
date: 2021-06-11 06:00:00 +0900
categories: [Language, JavaScript]
tags: [jquery-enter-key-event, enter-key-event, jquery-엔터키-이벤트, 엔터키-이벤트]
---

## 1. 엔터키 이벤트

* Script

```javascript
$(document).ready(function() {
    // ID가 message에서 엔터키를 누를 경우
    $("#message").keydown(function (key) {
        if (key.keyCode == 13) {
            alert("엔터키 누름");
        }
    });
});
```

* Html

```html
<input type="text" id="message" name="message"/>
```

## [출처 및 참고]
* <https://alpreah.tistory.com/101>
