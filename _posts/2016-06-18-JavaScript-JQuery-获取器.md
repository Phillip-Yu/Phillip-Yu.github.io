---
layout: post
title: 使用JQuery获取表格中单元格的长宽
date: 2016-6-16
categories: blog
tags:
  - JavaScript
  - JQuery
  - table
description: 使用JQuery获取表格中单元格的定位
---

HTML布局：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>方格坐标</title>
    <link rel="stylesheet" href="../css/方格坐标.css">
    <script src="../js/jquery-1.11.0.js"></script>
    <script src="../js/方格坐标.js"></script>
</head>
<body>
<table id="tab#">
    <tr>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>A1</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>A2</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>A3</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>A4</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>A5</span>
            </div>
        </td>
    </tr>
    <tr>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>B1</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>B2</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>B3</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>B4</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>B5</span>
            </div>
        </td>
    </tr>
    <tr>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>C1</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>C2</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>C3</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>C4</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>C5</span>
            </div>
        </td>
    </tr>
    <tr>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>D1</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>D2</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>D3</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>D4</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>D5</span>
            </div>
        </td>
    </tr>
    <tr>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>E1</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>E2</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>E3</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>E4</span>
            </div>
        </td>
        <td>
            <div class="divs">
                <input type="text"/>
                <span>E5</span>
            </div>
        </td>
    </tr>
</table>
<div id="labdiv">
    <label>

    </label>
</div>
</body>
</html>
```

CSS样式表：

```css
td{
    border-style: none;
    background-color: white;
    text-align: center;
    font-family: "Microsoft YaHei UI", serif;
}
input{
    width: 30px;
    height: 20px;
    border: 1px solid silver;
    color: black;
    display: none;
}
.divs{
    padding: 20px 20px;
    border-style: none;
    background-color: white;
    text-align: center;
    font-family: "Microsoft YaHei UI", serif;
}
div#labdiv{
    width: 314px;
    height: 20px;
    border: 1px solid silver;
    text-align: center;
}
```

```javascript
$(document).ready(function () {

    $("td>div").mouseover(function () {
        //获得焦点
        var row = $(this).parent().prevAll().length;
        var cal = $(this).parent().parent().prevAll().length;
        $("table~div").children("label")[0].innerText = (row + 1) + "x" + (cal + 1) + "   " + $(this).children("span")[0].innerText;
        $("tr:eq(" + cal + ")>td>div").css("background-color", "silver");
        $("tr td:nth-child("+(row+1)+")>div").css("background-color", "silver");
       //  $.each([0,1,2,3,4], function (i) {//可以使用遍历的方式逐个添加 也可以直接调用上面的选择器
       //
       //     $("tr:eq("+i+")>td>div:eq("+row+")").css("background-color", "silver");
       // });
        $(this).css("background-color", "red");

    });
    $("td>div").mouseout(function () {
        //失去焦点
        var row = $(this).parent().prevAll().length;
        var cal = $(this).parent().parent().prevAll().length;
        $(this).css("background-color", "white");
        $("tr:eq(" + cal + ")>td>div").css("background-color", "white");
        $("tr td:nth-child("+(row+1)+")>div").css("background-color", "white");
    });

    $("td>div").dblclick(function () {
        $(this).children("input").val($(this).children("span")[0].innerText);
        $(this).children("span")[0].innerText = "";
        $(this).children("input").css("display", "block");
    });
    $("td>div").children("input").blur(function () {
        $(this).css("display", "none");
        $(this).next("span")[0].innerText = $(this).val();
    });
});
```
