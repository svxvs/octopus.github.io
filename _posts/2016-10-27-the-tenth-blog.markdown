---
layout: post
title:  "深入PHP赋值行为 "
date:   2016-10-27 16:41:03 +0800
categories: jekyll update
---

<div id="article_content" class="article_content">

<p>首先声明，我并没有去读<a href="http://lib.csdn.net/base/36" class="replace_word" title="PHP知识库" target="_blank" style="color:#df3434; font-weight:bold;">PHP</a>的源码，只是对于PHP的有时候诡异的表现感兴趣，找了一下开发人员<a target="_blank" href="http://http//www.laruence.com/">laruence</a>的博客结合PHP提供的函数debug_zval_dump刺探得到了本博客所阐述的工作机理。如果你想对PHP变量赋值行为有一个了解或想对PHP赋值行为（Copy on write 和 Change on Write）加深理解的话，本文是适合你的，比较深入的去看源代码吧。</p>
<p>首先阅读本博客请先阅读笔者关于<a target="_blank" href="http://blog.csdn.net/wenzhou1219/article/details/16832067">PHP变量存储结构博文</a>和<a target="_blank" href="http://http//www.laruence.com/">laruence</a>的博文<a target="_blank" href="http://www.laruence.com/2008/09/19/520.html" style="font-family:Arial,Verdana">深入理解PHP原理之变量分离/引用(Variables
 Separation)</a>，但是要指出的是Laruence关于引用赋值行为的描述和用debug_zval_dump刺探的结果有出入，下面我会指出Laruence的叙述有误的地方。</p>
<p>总结来说，分析PHP赋值行为遵循以下原则:</p>
<p><span style="color:#ff0000">在保证常规赋值思维的前提下，保证占用尽量小的内存和做尽量少的指针指向变化。</span></p>
<h3><a name="t0"></a><strong><span style="font-size:14px">1.内存的问题("Copy on write")</span></strong></h3>
<p>由于PHP弱类型变量的原因，在PHP中存储一个变量会比在C等强类型语言中占用的内存大得多，如果也按照C语言中每个变量开辟一个单独的内存空间来保存，显然将占用大量的内存空间，显然这并不是最优化的设计。我们自己可以设想一下在PHP中诸如以下的赋值语句</p>
<p></p>
<div class="dp-highlighter bg_php"><div class="bar"><div class="tools"><b>[php]</b> <a href="#" class="ViewSource" title="view plain" onclick="dp.sh.Toolbar.Command('ViewSource',this);return false;">view plain</a><span data-mod="popu_168"> <a href="#" class="CopyToClipboard" title="copy" onclick="dp.sh.Toolbar.Command('CopyToClipboard',this);return false;">copy</a><div style="position: absolute; left: 476px; top: 833px; width: 23px; height: 12px; z-index: 99;"><embed id="ZeroClipboardMovie_1" src="http://static.blog.csdn.net/scripts/ZeroClipboard/ZeroClipboard.swf" loop="false" menu="false" quality="best" bgcolor="#ffffff" name="ZeroClipboardMovie_1" allowscriptaccess="always" allowfullscreen="false" type="application/x-shockwave-flash" pluginspage="http://www.macromedia.com/go/getflashplayer" flashvars="id=1&amp;width=23&amp;height=12" wmode="transparent" width="23" align="middle" height="12"></div></span><span data-mod="popu_169"> <a href="#" class="PrintSource" title="print" onclick="dp.sh.Toolbar.Command('PrintSource',this);return false;">print</a></span><a href="#" class="About" title="?" onclick="dp.sh.Toolbar.Command('About',this);return false;">?</a></div></div><ol start="1" class="dp-c"><li class="alt"><span><span>&lt;?php&nbsp;&nbsp;</span></span></li><li class=""><span><span class="vars">$a</span><span>=1;&nbsp;&nbsp;</span></span></li><li class="alt"><span><span class="vars">$b</span><span>=</span><span class="vars">$a</span><span>;&nbsp;&nbsp;</span></span></li><li class=""><span>?&gt;&nbsp;&nbsp;</span></li></ol><div class="save_code tracking-ad" data-mod="popu_249" style="display: none;"><a href="javascript:;" target="_blank"><img src="http://static.blog.csdn.net/images/save_snippets.png"></a></div></div><pre name="code" class="php" style="display: none;">&lt;?php
$a=1;
$b=$a;
?&gt;</pre>
<p></p>
<p>对$a显然要开辟一块新的内存，但是对于$b来说要不要开辟新的内存呢？<br>
显然，在PHP中我们是不需要像C一样，每次都去开辟变量内存的，针对上面的情况，我们只需要在变量符号表中加入名字b，同时将其对应的zval指针指向$a指向的zval内存地址即可。其内存结构变化示意图如下</p>
<p><img src="http://img.blog.csdn.net/20131121133841781?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VuemhvdTEyMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</p>
<p>这时候没有做内存开辟，那么什么时候会做内存开辟呢？答案是<span style="color:#ff0000">出现不可调和的矛盾时会产生内存开辟</span>。</p>
<p>我们观察“$a赋值X”这一语句，赋值可为普通赋值（=）和引用赋值（=&amp;），X可为常量或变量。<span style="color:#ff0000">所有的内存开辟都是在X端完成</span>，整理列表如下。</p>
<p>
</p><table width="758" cellspacing="0" cellpadding="0" border="1">
<colgroup><col width="96"><col width="72"><col width="103"><col width="108"><col width="379"></colgroup>
<tbody>
<tr height="19">
<td>$a</td>
<td>赋值</td>
<td>X</td>
<td>举例</td>
<td>判断依据和结果</td>
</tr>
<tr height="43">
<td rowspan="8">&nbsp;</td>
<td rowspan="4">=</td>
<td>常量</td>
<td>$a=1;</td>
<td><span style="color:#ff0000">新建</span><br>
$a&gt;A{refcount:1,is_ref:0,value:1,type:IS_LONG}</td>
</tr>
<tr height="71">
<td>is_ref=0<br>
refcount=1</td>
<td>$b=1;<br>
$a=$b;</td>
<td>$b&gt;A{refcount:1,is_ref:0,value:1,type:IS_LONG}<br>
\/<br>
$a&gt;A{refcount:2,is_ref:0,value:1,type:IS_LONG}<br>
$b&gt;A{refcount:2,is_ref:0,value:1,type:IS_LONG}</td>
</tr>
<tr height="170">
<td>is_ref=0<br>
refcount&gt;1</td>
<td>$c=1;<br>
$b=$c;<br>
$a=$b;</td>
<td>$c&gt;A{refcount:1,is_ref:0,value:1,type:IS_LONG}<br>
\/<br>
$c&gt;A{refcount:2,is_ref:0,value:1,type:IS_LONG}<br>
$b&gt;A{refcount:2,is_ref:0,value:1,type:IS_LONG}<br>
\/<br>
$c&gt;A{refcount:3,is_ref:0,value:1,type:IS_LONG}<br>
$b&gt;A{refcount:3,is_ref:0,value:1,type:IS_LONG}<br>
$a&gt;A{refcount:3,is_ref:0,value:1,type:IS_LONG}</td>
</tr>
<tr height="108">
<td>is_ref=1</td>
<td>$c=1;<br>
$b=&amp;$c;<br>
$a=$b;</td>
<td>$c&gt;A{refcount:1,is_ref:0,value:1,type:IS_LONG}<br>
\/<span style="color:#ff0000">这里Laruence叙述有误，is_ref=1时所有refcount清0</span><br>
$c&gt;A{refcount:0,is_ref:1,value:1,type:IS_LONG}<br>
$b&gt;A{refcount:0,is_ref:1,value:1,type:IS_LONG}<br>
\/<span style="color:#ff0000">is_ref=1,将A复制一份给$a</span><br>
$a&gt;B{refcount:1,is_ref:0,value:1,type:IS_LONG}</td>
</tr>
<tr height="18">
<td rowspan="4">=&amp;</td>
<td>常量</td>
<td>$a=&amp;1;</td>
<td>语法错误</td>
</tr>
<tr height="72">
<td>is_ref=0<br>
refcount=1</td>
<td>$b=1;<br>
$a=&amp;$b;</td>
<td>$b&gt;A{refcount:1,is_ref:0,value:1,type:IS_LONG}<br>
\/<br>
$b&gt;A{refcount:0,is_ref:1,value:1,type:IS_LONG}<br>
$a&gt;A{refcount:0,is_ref:1,value:1,type:IS_LONG}</td>
</tr>
<tr height="144">
<td>is_ref=0<br>
refcount&gt;1</td>
<td>$c=1;<br>
$b=$c;<br>
$a=&amp;$b;</td>
<td>$c&gt;A{refcount:1,is_ref:0,value:1,type:IS_LONG}<br>
\/<br>
$c&gt;A{refcount:2,is_ref:0,value:1,type:IS_LONG}<br>
$b&gt;A{refcount:2,is_ref:0,value:1,type:IS_LONG}<br>
\/<span style="color:#ff0000">refcount&gt;1,将A复制一份给$c</span><br>
$c&gt;A{refcount:1,is_ref:0,value:1,type:IS_LONG}<br>
$b&gt;B{refcount:0,is_ref:1,value:1,type:IS_LONG}<br>
$a&gt;B{refcount:0,is_ref:1,value:1,type:IS_LONG}</td>
</tr>
<tr height="122">
<td>is_ref=1</td>
<td>$c=1;<br>
$b=&amp;$c;<br>
$a=&amp;$b;</td>
<td>$c&gt;A{refcount:1,is_ref:0,value:1,type:IS_LONG}<br>
\/<br>
$c&gt;A{refcount:0,is_ref:1,value:1,type:IS_LONG}<br>
$b&gt;A{refcount:0,is_ref:1,value:1,type:IS_LONG}<br>
\/<br>
$a&gt;A{refcount:0,is_ref:1,value:1,type:IS_LONG}</td>
</tr>
</tbody>
</table>
可以看到，新建内存只有如下三种情况：<p></p>
<p>1.名字a不存在，赋给一个常量，这时候没办法必须新建</p>
<p>2.$b=&amp;$c，$a=$b，最开始$b和$c捆绑在一起，除非$b重新绑定到其他引用关系上，$b、$c的绑定关系是不会解除的，这时候$a想和$b在一起又不想和$c绑定（如果$a=&amp;$b，那就是a、b、c绑定了），那就只有分出去一个$b的复制品给$a了，希望这样解释比较好理解。在程序中判断条件为<span style="color:#ff0000">赋值行为为=且X的is_ref=1</span>。</p>
<p>3.$b=$c,$a=&amp;$b，这时候$b想和$a绑定在一起，$a又不想$c搀和进来（如果$b=&amp;$c，那就是a、b、c绑定了），那就只有为难$b分出个复制品给$c了。在程序中判断条件为<span style="color:#ff0000">赋值行为为=&amp;且X的refcount&gt;1</span>。</p>
<p><span style="color:#ff0000">可以看到所有要新建内存的地方都是矛盾不可调和的地方，这也是动态类型语言设计的高明之处，也是所谓的"Copy on write"原则的意思。</span></p>
<h3><a name="t1"></a><span style="font-size:14px"><strong>2.指针的问题（"Change on write"）</strong></span></h3>
<p>在之前我们谈到的$a,$b指向同一内存结构，实际上就是$a,$b名字对应的指针指向统一内存结构，对于如下语句</p>
<p></p>
<div class="dp-highlighter bg_php"><div class="bar"><div class="tools"><b>[php]</b> <a href="#" class="ViewSource" title="view plain" onclick="dp.sh.Toolbar.Command('ViewSource',this);return false;">view plain</a><span data-mod="popu_168"> <a href="#" class="CopyToClipboard" title="copy" onclick="dp.sh.Toolbar.Command('CopyToClipboard',this);return false;">copy</a><div style="position: absolute; left: 476px; top: 2606px; width: 23px; height: 12px; z-index: 99;"><embed id="ZeroClipboardMovie_2" src="http://static.blog.csdn.net/scripts/ZeroClipboard/ZeroClipboard.swf" loop="false" menu="false" quality="best" bgcolor="#ffffff" name="ZeroClipboardMovie_2" allowscriptaccess="always" allowfullscreen="false" type="application/x-shockwave-flash" pluginspage="http://www.macromedia.com/go/getflashplayer" flashvars="id=2&amp;width=23&amp;height=12" wmode="transparent" width="23" align="middle" height="12"></div></span><span data-mod="popu_169"> <a href="#" class="PrintSource" title="print" onclick="dp.sh.Toolbar.Command('PrintSource',this);return false;">print</a></span><a href="#" class="About" title="?" onclick="dp.sh.Toolbar.Command('About',this);return false;">?</a></div></div><ol start="1" class="dp-c"><li class="alt"><span><span>&lt;?php&nbsp;&nbsp;</span></span></li><li class=""><span><span class="vars">$a</span><span>&nbsp;=&nbsp;1;&nbsp;&nbsp;</span></span></li><li class="alt"><span><span class="vars">$b</span><span>&nbsp;=&nbsp;</span><span class="vars">$a</span><span>;&nbsp;&nbsp;</span></span></li><li class=""><span><span class="func">echo</span><span>&nbsp;</span><span class="string">'$a='</span><span>.</span><span class="vars">$a</span><span>.</span><span class="string">"\t"</span><span>.</span><span class="string">'$b='</span><span>.</span><span class="vars">$b</span><span>.</span><span class="string">"\n"</span><span>;&nbsp;&nbsp;</span></span></li><li class="alt"><span><span class="vars">$b</span><span>&nbsp;=&nbsp;2;&nbsp;&nbsp;</span></span></li><li class=""><span><span class="func">echo</span><span>&nbsp;</span><span class="string">'$a='</span><span>.</span><span class="vars">$a</span><span>.</span><span class="string">"\t"</span><span>.</span><span class="string">'$b='</span><span>.</span><span class="vars">$b</span><span>.</span><span class="string">"\n"</span><span>;&nbsp;&nbsp;</span></span></li><li class="alt"><span>&nbsp;&nbsp;</span></li><li class=""><span><span class="vars">$c</span><span>&nbsp;=&nbsp;1;&nbsp;&nbsp;</span></span></li><li class="alt"><span><span class="vars">$d</span><span>&nbsp;=&nbsp;&amp;</span><span class="vars">$c</span><span>;&nbsp;&nbsp;</span></span></li><li class=""><span><span class="func">echo</span><span>&nbsp;</span><span class="string">'$c='</span><span>.</span><span class="vars">$c</span><span>.</span><span class="string">"\t"</span><span>.</span><span class="string">'$d='</span><span>.</span><span class="vars">$d</span><span>.</span><span class="string">"\n"</span><span>;&nbsp;&nbsp;</span></span></li><li class="alt"><span><span class="vars">$d</span><span>&nbsp;=&nbsp;2;&nbsp;&nbsp;</span></span></li><li class=""><span><span class="func">echo</span><span>&nbsp;</span><span class="string">'$c='</span><span>.</span><span class="vars">$c</span><span>.</span><span class="string">"\t"</span><span>.</span><span class="string">'$d='</span><span>.</span><span class="vars">$d</span><span>.</span><span class="string">"\n"</span><span>;&nbsp;&nbsp;</span></span></li><li class="alt"><span>?&gt;&nbsp;&nbsp;</span></li></ol><div class="save_code tracking-ad" data-mod="popu_249"><a href="javascript:;" target="_blank"><img src="http://static.blog.csdn.net/images/save_snippets.png"></a></div></div><pre name="code" class="php" style="display: none;">&lt;?php
$a = 1;
$b = $a;
echo '$a='.$a."\t".'$b='.$b."\n";
$b = 2;
echo '$a='.$a."\t".'$b='.$b."\n";

$c = 1;
$d = &amp;$c;
echo '$c='.$c."\t".'$d='.$d."\n";
$d = 2;
echo '$c='.$c."\t".'$d='.$d."\n";
?&gt;</pre>这里采用命令行演示，结果如下
<p></p>
<p></p>
<p>$a=1 $b=1<br>
$a=1 $b=2<br>
$c=1 $d=1<br>
$c=2 $d=2</p>
<p>可以看到：</p>
<span style="font-family:Arial,Verdana">这里我们用一张图来说明其内存结构和指针指向变化示意图如下</span><br>
<p><span style="font-family:Arial,Verdana"><img src="http://img.blog.csdn.net/20131121174338390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VuemhvdTEyMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p><span style="font-family:Arial,Verdana">这两个例子的唯一不同在于定义$d时加上了一个引用定义符&amp;。为什么$b赋值时指向发生了变化而$d赋值时没有变化，是么时候指针指向才会发生变化呢?答案是同样是<span style="color:rgb(255,0,0)">出现不可调和的矛盾时会产生指针指向改变</span>。<br>
</span></p>
<p><span style="font-family:Arial,Verdana">同样我们观察“$a赋值X”这一语句，赋值可为普通赋值（=）和引用赋值（=&amp;），X可为常量或变量。<span style="color:#ff0000">所有的指针指向改变都是在$a端完成。</span><br>
</span></p>
<p>这里我就不再列表了，可以同样按照以上方法分析，结论同样是三种情况下指针指向发生改变：</p>
<p></p>
<p>1.名字a不存在，这时候新建了名字没办法必须将指针指向一个地方</p>
<p>2.$b=1，$a=$b，$a=2，最开始$a和$b指向同一内存结构，当$a要指向一个新的值又不希望$c指向这个值，那么只有将$a指针从原来的指向$b内存结构改为指向新建的内存结构。在程序中判断条件为<span style="color:#ff0000">赋值行为为=且$a的refcount&gt;1</span>。</p>
<p>3.$b=1，$c=2，$a=&amp;$b，$a=&amp;$c，这时候$a想和$c绑定在一起，只能绑定一个内存结构，$a只有舍弃$b，将$a的指针从原来指向$b内存结构改为指向$c内存结构。在程序中判断条件为<span style="color:#ff0000">赋值行为为=&amp;且$a的is_ref=1</span>。</p>
<p><span style="color:#ff0000">可以看到所有要改变指针指向的地方同样是矛盾不可调和的地方，这同样也是动态类型语言设计的高明之处，也是所谓的"Change on write"原则的意思。</span></p>
<br>
<p><br>
</p>
<p>实际上所有弱类型的语言机理大概都是如此，只有按照这样的逻辑来设计的动态语言才是<span style="color:#ff0000">最优性能同时不反人类的</span>，下一节深入到PHP的存储结构来讲解PHP的引用，说明PHP引用特别是对象引用容易被错误理解的地方。</p>
<p><br>
</p>
<p>原创，转载请注明来自<a target="_blank" href="http://blog.csdn.net/wenzhou1219">http://blog.csdn.net/wenzhou1219</a><br>
</p>
   
</div>