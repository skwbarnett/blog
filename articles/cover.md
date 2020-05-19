---
title: 覆盖整个可视区域的遮罩效果
date: 2013-10-25 20:01:57
categories:
- CSS
tags:
- CSS
---

拿到设计稿后前端工程师们开始实现设计稿上的一切，对于一个前端工程师而言“覆盖整个可视区域的遮罩效果”只是万千实现任务中的一个，所以会做这么一个实现也不是什么大不了的。而今我想记录一下是因为当时我对这个的实现并不好。

<!--more-->

## CSS实现覆盖整个可视区域的遮罩效果
### HTML代码部分

    <div class="container">
      <img src="jin.png">
      <div class="cover">
    	<div class="inner-text">
    		<h1>透明层遮盖住</h1>
    	</div>
      </div>
     </div>

### CSS代码部分

    .cover{
    	position: fixed;
    	left: 0;
    	top: 0;
    	right: 0;
    	bottom: 0;
    	z-index: 1;
    	background: url(cover.png);
    }
    .inner-text{
    	position: absolute;
    	z-index: 2;
    	left: 50%;
    	top: 50%;
    	color: yellow;
    }

### 如图分析
1. ![](../assets/zhgai1.png)
2. ![](../assets/zhgai2.png)

效果还是很明显的嘛！首先，遮罩层用了半透明图片，当然这方面对于现代浏览器来说`opacity`也是不错的选择哦！其次`position: fixed;`对于IE6不支持该条规则的情况，我只能说：“IE6不如自挂东南枝！”。如果您确实相兼容IE6可以留言，我一定会尽快解答。

## 关于遮罩效果在实际中的应用
在网站设计中不乏可视区域遮罩以此突出显示操作内容的设计，诸如单击网页上的某个按钮比如登陆按钮，就会弹出登录窗口而把其他的内容给遮盖掉；其实这样还是好的，我比较讨厌打开一个网页会弹出遮罩层然后播放一段广告然后消失掉；这样的实现就涉及到脚本语言了，通过脚本语言会有很多丰富诡异的效果，我个人觉得最重要的还是要让浏览网页的人们感到舒适。

## 结语
内容就这些了，比较肤浅，具体的更丰富的实现还是靠大家发了，文中出错的地方，提前感谢您能帮忙指正！