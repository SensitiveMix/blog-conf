---
title: 那些年用过的 JQuery 之经典 QQ 表情插件
date: 2017-06-11 22:26:45
categories:
  - 开发工具
tags: 
  - Javascript
  - Jquery
---
由于目前在接触聊天室项目，接到一个需求实现一个类似QQ表情。在网上找了一款jquery经典表情插件。下面介绍一个用法：

HTML：

　　首先在html页面的head中引入jQuery库文件和QQ表情插件jquery.qqFace.js文件

       然后在body中加入以下html代码：

<pre><span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">1</span> 　　<span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">div</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">id</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="show"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">div</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">2</span>     <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">div</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="comment"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">3</span>     <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">div</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="com_form"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">4</span>         <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">textarea</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="input"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">id</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="saytext"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">name</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="saytext"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">textarea</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">5</span>         <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">p</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">span</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="emotion"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>表情<span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">span</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">input</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">type</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="button"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="sub_btn"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">value</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="提交"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">p</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">6</span>     <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">div</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">7</span>   <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">div</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span></span></span></span></span></span></span> </pre>


CSS：

我们用CSS来美化页面，关键是表情按钮图片span.emotion的鼠标滑上与移开效果，以及调用表情插件后，显示的表情.qqFace面板效果，请看代码:

<pre> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">1</span> <span data-mce-style="color: #000000;">.comment{width:680px; margin:20px auto; position:relative}
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">2</span> <span data-mce-style="color: #000000;">.comment h3{height:28px; line-height:28px}
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">3</span> <span data-mce-style="color: #000000;">.com_form{width:100%; position:relative}
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">4</span> <span data-mce-style="color: #000000;">.input{width:99%; height:60px; border:1px solid #ccc}
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">5</span> <span data-mce-style="color: #000000;">.com_form p{height:28px; line-height:28px; position:relative}
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">6</span> <span data-mce-style="color: #000000;">span.emotion{width:42px; height:20px; background:url(icon.gif) no-repeat 2px 2px;
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">7</span> <span data-mce-style="color: #000000;">padding-left:20px; cursor:pointer}
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">8</span> <span data-mce-style="color: #000000;">span.emotion:hover{background-position:2px -28px}
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">9</span> <span data-mce-style="color: #000000;">.qqFace{margin-top:4px;background:#fff;padding:2px;border:1px #dfe6f6 solid;}</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">10</span> <span data-mce-style="color: #000000;">.qqFace table td{padding:0px;}</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">11</span> <span data-mce-style="color: #000000;">.qqFace table td img{cursor:pointer;border:1px #fff solid;}</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">12</span> <span data-mce-style="color: #000000;">.qqFace table td img:hover{border:1px #0066cc solid;}</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">13</span> #show{width:680px; margin:20px auto} </pre>

jquery:

当我们点击页面输入框下方那个笑脸时，触发调用qqface表情插件，简单几行就搞定。

<pre><span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">1</span> <span data-mce-style="color: #000000;">$(function(){</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">2</span> <span data-mce-style="color: #000000;">$('.emotion').qqFace({</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">3</span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">4</span> <span data-mce-style="color: #000000;">assign:'saytext', //给输入框赋值</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">5</span> <span data-mce-style="color: #000000;">path:'face/'    //表情图片存放的路径</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">6</span> <span data-mce-style="color: #000000;">});</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">7</span> <span data-mce-style="color: #000000;">...</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">8</span> }); </pre>

当选择表情图片后，输入框中会插入一段如[em_5]之类的代码，代表插入的表情图片，实际应用中，点提交按钮后应该将这段表情代码连同其他内容插入到数据表中。而在页面显示的时候，我们应该将表情代码替换成真正的图片显示在页面上。下面的代码是插入表情图片后，点击提交按钮，使用javascript自定义函数将表情代码替换并显示：

<pre> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">1</span> <span data-mce-style="color: #000000;">$(function(){
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">2</span> <span data-mce-style="color: #000000;">    ...
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">3</span> <span data-mce-style="color: #000000;">    $(".sub_btn").click(function(){
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">4</span> <span data-mce-style="color: #000000;">        var str = $("#saytext").val();
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">5</span> <span data-mce-style="color: #000000;">        $("#show").html(replace_em(str));
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">6</span> <span data-mce-style="color: #000000;">    });
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">7</span> <span data-mce-style="color: #000000;">});
</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">8</span> <span data-mce-style="color: #000000;">function replace_em(str){_ </span> _<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">9</span>     str = str.replace(/\<span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">g,'<；');</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">10</span> <span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">    str = str.replace(/\</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span><span data-mce-style="color: #000000;">/g,'>；');</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">11</span>     str = str.replace(/\n/g,'<span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">；br</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">/></span><span data-mce-style="color: #000000;">；');</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">12</span>     str = str.replace(/[em_([0-9]*)]/g,'<span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">img</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">src</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="face/$1.gif"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">border</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="0"</span> <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">/></span><span data-mce-style="color: #000000;">');</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">13</span> <span data-mce-style="color: #000000;">return str;</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">14</span> }</span> </pre>

下面着重介绍下在nodejs+express4中的应用：

步骤如下：

1：加入引用表情的span

<pre><span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">1</span>         <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">div</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="send-out"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">2</span>             <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">span</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="emotion"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">id</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="emotion"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">img</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="head_picture"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">src</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="/images/r-middle-pic07.png"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>表情<span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">span</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">3</span>             <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">input</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="send-text"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">id</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="Y_iSend_Input"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">type</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="text"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">maxlength</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="100"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">value</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">=""</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">4</span>             <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">input</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">id</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="Y_iSend_Bt"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">class</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="out_text"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">onclick</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="CHAT.submit();"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">type</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="button"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">value</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="发送"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">/></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">5</span>         <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">div</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span></span></span></pre>

2：加入头文件并调用jquery

<pre><span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">1</span> <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">script</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">type</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="text/javascript"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">src</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="/js/jquery.qqFace.js"</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">script</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span>
<span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">2</span> <span data-mce-style="color: #000000;">$(function(){</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">3</span> <span data-mce-style="color: #000000;">$('.emotion').qqFace({</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">4</span> <span data-mce-style="color: #000000;">assign:'Y_iSend_Input', //给输入框赋值</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">5</span> <span data-mce-style="color: #000000;">path:'/face/'    //表情图片存放的路径</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">6</span> <span data-mce-style="color: #000000;">});</span> <span data-mce-style="color: #008080;" style="color: rgb(0, 128, 128);">7</span>     });</span></pre>

3：用正则替换输入的[]表情

<pre> <span data-mce-style="color: #000000;">w.CHAT={
replace_em: function (str) {
            str = str.replace(/\_</span>_<span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">g, '<;');
            str = str.replace(/\</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">></span><span data-mce-style="color: #000000;">/g, '>;');
            str = str.replace(/\n/g, '</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">;br</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">/></span><span data-mce-style="color: #000000;">;');
            str = str.replace(/[em</span>_([0-9]*)]/g, '<span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);"><</span><span data-mce-style="color: #800000;" style="color: rgb(128, 0, 0);">img</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">src</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="/face/$1.gif"</span> <span data-mce-style="color: #ff0000;" style="color: rgb(255, 0, 0);">border</span><span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">="0"</span> <span data-mce-style="color: #0000ff;" style="color: rgb(0, 0, 255);">/></span><span data-mce-style="color: #000000;">');
            return str;
        },

        //提交聊天消息内容
        submit: function () {

            var str = $("#Y_iSend_Input").val();

            $("#Y_iSend_Input").val(CHAT.replace_em(str));

            var message_text = d.getElementById("Y_iSend_Input").value;
  }
}</span></span></pre>

4：显示

![](http://images2015.cnblogs.com/blog/792678/201605/792678-20160512163256265-556089818.png)

nodejs聊天室，浅析。
