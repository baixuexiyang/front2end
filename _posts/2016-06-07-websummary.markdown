---
layout: post
title:  "web开发常见问题总结"
date:   2016-06-07 22:34:20 +0800
categories: 技术
archive: false
disqus: true
---

#### 隐藏a链接点击时的虚线

IE：   hidefocus="hidefocus"   
Firefox/Chrome:    a:focus{outline:none;}

#### 禁用页面鼠标拖动选择元素

{% highlight css %}
-webkit-user-select:none;
user-select:none;
{% endhighlight %}


#### image usemap

1、兼容ios9，usemap要加#  
{% highlight html %}
<img usemap="#map"/>
{% endhighlight %}
2、去掉点击出现边框（三星手机）   
{% highlight html %}
<area hidefocus="true" onfocus="this.blur()">
{% endhighlight %}

#### window.open第二个参数不能重复   
   
#### toFixed()  返回字符串   
  
#### getSelection  

当点击页面获取输入框的光标位置，则点击元素必须是表单元素，比如button、input等

#### input事件

1、只能输入数字和小数点
{% highlight html %}
onkeypress="return event.keyCode>=48&&event.keyCode<=57||event.keyCode==46"
{% endhighlight %}
2、只可粘贴数字到文本框
{% highlight html %}
onpaste="return !clipboardData.getData('text').match(/[^\d]/)" 
{% endhighlight %}
3、禁止拖动内容到文本框中
{% highlight html %}
ondragenter="return false"
{% endhighlight %}

#### 背景透明，文字也透明

{% highlight html %}
<div style="filter:alpha(opacity=50);opacity:.5;background-color:#f00;width:500px;">
　　<span style="color:#000">背景透明，文字也透明</span>
</div>
{% endhighlight %}

#### 背景透明，文字不透明

{% highlight html %}
<div style="filter:alpha(opacity=50);opacity:.5;background-color:#f00;width:500px">
　　<span style="position:relative;color:#000">背景透明，文字不透明</span>
</div>
{% endhighlight %}

####  JSON.stringify(),indexOf() ie6/7/8有问题
