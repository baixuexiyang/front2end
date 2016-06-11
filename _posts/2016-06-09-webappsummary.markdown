---
layout: post
title:  "webapp开发常见问题总结"
date:   2016-06-09 12:23:20 +0800
categories: 技术
archive: false
disqus: true
---
#### dom元素可编辑

{% highlight html %}
<style>
	.article-content{
		-webkit-user-select: text;
		user-select: text;
	}
</style>
<div class="article-content" contenteditable="true">
</div>
{% endhighlight %}

#### position:absolute 或者fixed  当虚拟键盘弹起时会顶上来

#### 唤起数字键盘

{% highlight html %}
<input type="number" min="0" inputmode="numeric" pattern="[0-9]*" title="Non-negative integral number">
{% endhighlight %}


#### 后退并刷新

{% highlight html %}
<a href="#" onclick="self.location=document.referrer;">返回</a> 
{% endhighlight %}

#### 禁用webkit高亮

{% highlight css %}
body {
	-webkit-touch-callout: none;
	-webkit-user-select: none;
	-khtml-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
}
{% endhighlight %}


#### ios 中日期问题

合法日期有两种， "2015/12/31 00:00:00"或者"2015-12-31T00:00:00"
{% highlight javascript %}
var dateString = "2015-12-31 00:00:00";
var d = new Date(dateString.replace(' ', 'T'));

var dateString = "2015-12-31 00:00:00";
var d = new Date(dateString.replace(/-/g, '/'));


var s = "2015-12-31 00:00:00".replace(/[ :]/g, "-").split("-"),
    d = new Date( s[0], s[1], s[2], s[3], s[4], s[5] );

var s = "2015-12-31 00:00:00".split(" ")[0].split("-"),
    d = new Date( s[0], s[1], s[2], 0, 0, 0 );
{% endhighlight %}


#### -webkit-tap-highlight-color:rgba(255,255,255,0)可以同时屏蔽ios和android下点击元素时出现的阴影

注意：transparent的属性值在android下无效。

#### -webkit-appearance:none可以同时屏蔽输入框怪异的内阴影。

#### -webkit-transform:translate3d(0, 0, 0)在ios下可以让动画更加流畅（这个属性会调用硬件加速模式），但是在android下可能会引起其他问题。


#### -webkit-background-size可以做高清图标，不过一些低版本的android只能识别background-size，所以有必要两个都要写上；用这个属性的时候推荐用cover这个值，可以自动去匹配宽和高。

####  背景图片

{% highlight css %}
background-image: url();
background-repeat: no-repeat;
background-position: center;
background-size: cover;
{% endhighlight %}

对于大图自适应可以加上
{% highlight css %}
background-attached: fixed;
{% endhighlight %}

####  android、ios4及以下，固定宽/高块级元素的overflow:scroll/auto失效

####  当用iScroll时候，不能使用:focus{outline:0}伪类，否则滑动会卡。

#### 页面在iphone上时，当选中input输入框的时候，input区域会有一个黑色的阴影 

{% highlight css %}
-webkit-appearance: none;
-webkit-tap-highlight-color: rgba(0, 0, 0, 0);
{% endhighlight %}

#### 禁用video全屏播放

webkit-playsinline


#### 多行文本省略

{% highlight css %}
overflow : hidden;
text-overflow: ellipsis;
display: -webkit-box;
-webkit-line-clamp: 2;
-webkit-box-orient: vertical;
{% endhighlight %}

#### canvas(x5内核)

小于1G的内存， canvas的内存不能超过100M
1G到2G的内存， canvas的内存不能超过300M
大于2G的内存 canvas的内存不能超过500M
为了防止内存占用过多，硬件加速的CANVAS最多支持5个
小于等于1G内存手机，由于内存没办法精确统计，当达到75M以上，CANVAS数量最多支持20个

#### click有300ms延迟

用 touchstart 事件替代 click 事件，或者用第三方fastclick

#### iscroll4问题总结

1.在iscroll4的滚动容器范围内，点击input框、select等表单元素时没有响应
这个问题原因在于iscroll需要一直监听用户的touch操作，以便灵敏的做出对应效果，所以它把其余的默认事件屏蔽了，解决的方法是，在iscroll4源码里面找到这一行，
{% highlight javascript %}
     onBeforeScrollStart: function (e) { e.preventDefault(); }
{% endhighlight %}
然后把它改成：
{% highlight javascript %}
    onBeforeScrollStart: function (e) {
    	var nodeType = e.explicitOriginalTarget ? e.explicitOriginalTarget.nodeName.toLowerCase() : (e.target ? e.target.nodeName.toLowerCase() : '');
    	if(nodeType !='select'&& nodeType !='option'&& nodeType !='input'&& nodeType!='textarea') e.preventDefault(); 
    }
{% endhighlight %}
    这样只要你touch的元素是 select || option || input || textarea时，它就不会执行e.preventDefault()，默认的事件就不会被屏蔽了。

    如果你有其他不想被屏蔽的元素，可以自己修改，不过需要注意onBeforeScrollStart里的屏蔽默认事件很重要，它是iscroll进行流畅滚动的基础，不要随便的把它去掉，否则你会发现滚动起来很卡顿。

 

2.往iscroll容器内添加内容时，容器闪动的bug

    我在做上拉加载更多内容的时候，肯定需要把新的内容插入到容器内，这时发现有时容器会出现闪动，一开始认为是insert进去的内容太多，后来又觉得是不是因为里面布局用了float的原因导致重新渲染，最后通通排除。

    其实病灶在于iscroll使用了太为先进的CSS3属性，可能web webkit对这些属性的支持力度还是不够好。

    涉及的两个属性是  translate3d 和 TransitionTimingFunction，或许是这两个属性在列表长度改变时会影响到渲染，所以导致页面闪动，解决办法就是找到源代码的，

     has3d = 'WebKitCSSMatrix' in window && 'm11' in new WebKitCSSMatrix()

改成：
     has3d = false

和在配置iscroll时，useTransition设置成false就可以了（useTransition默认是false的）。

    这样做有一点瑕疵就是滚动起来和原来比没那么流畅了（原来的效果真的是可以媲美原生app的），但是假如你不对比的话，是看不出来了。

    在效果和体验上面选择，我更看重体验。

    不过如果你符合下面的条件，我还是不建议你修改成我这样：

        1)即使你不修改，无论你怎么往iscroll容器里面插内容，它都不会闪动，这种情况大多出现在纯文字的列表。假如列表涉及复杂的布局和图片，很多时候会出现闪动bug

        2)如果你的web app只是单纯在手机浏览器浏览。translate3d 和 TransitionTimingFunction只是在IOS里的uiwebview支持不成熟，但是在手机上的safari完全没有问题，所以如果你不是用phonegap之类的框架开发混合app，你不需要担心这个问题。

        3)只针对android，因为android的webkit暂时还不支持translate3d，iscroll会自动选择不用。

 

3.过长的滚动内容，导致卡顿和app直接闪退

    说白了iscroll都是用js+css3实现的，对浏览器的消耗肯定是可观的，避免无限制的内容加载本身就是web产品应该避免的。

    假如无可避免，我们可以尽量减低iscroll对浏览器内存的消耗

        1)不要使用checkDOMChanges。虽然checkDOMChanges很方便，定时检测容器长度是否变化来refresh，但这也意味着你要消耗一个Interval的内存空间

        2)隐藏iscroll滚动条，配置时设置hScrollbar和vScrollbar为false。

        3)不得已的情况下，去掉各种效果，momentum、useTransform、useTransition都设置为false

 

4.左右滚动时，不能正确响应正文上下拉动

    在做这种效果时 ，假如这个幻灯片模块只是你页面的一部分，你还需要上下拉动页面去浏览其它内容时，你的手指在这个模块上做上下拨动时，恐怕会没有反应。原因还是和问题1一样的，因为屏蔽了默认事件。
