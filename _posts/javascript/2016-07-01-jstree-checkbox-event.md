---
title: jsTree 체크박스 이벤트
author: dejavuhyo
date: 2016-07-01 00:31:00 +0900
categories: [Language, JavaScript]
tags: [jstree, jstree-체크박스]
---

## 1. 공식 사이트

* <https://www.jstree.com/>

## 2. 체크박스 트리

![checkbox-tree](/assets/img/2016-07-01-jstree-checkbox-event/checkbox-tree.png)

```javascript
$(function () {
    $("#plugins1").jstree({
        "checkbox" : {
            "keep_selected_style" : false
        },
        "plugins" : [ "checkbox" ]
    });
});
```

## 3. 체크박스 클릭 이벤트

```javascript
$('#t').on('select_node.jstree Event', function (e, data) {
    var id = data.node.id;
    alert(id);
});
```

## 4. 체크박스 클릭 해제 이벤트

```javascript
$('#tree').on('deselect_node.jstree Event', function (e, data) {
    var id = data.node.id;
    alert(id);
});
```
