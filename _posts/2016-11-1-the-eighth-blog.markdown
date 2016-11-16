---
layout: post
title:  "app如何保证通讯安全"
date:   2016-11-7 19:37:03 +0800
categories: jekyll update
---


<div class="skin_detail" id="article_content">
                        
<p><strong><span style="font-size:14px">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;app如何保证通讯安全，可以从一下几点入手：</span></strong></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp;1<span style="font-family:Arial; color:#333333"><span style="font-size:14px; line-height:26px">&nbsp;</span></span>：&nbsp;<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">pp和后端的通讯过程中，api请求有可能被别人截取或不小心泄露。那么，怎么保证api请求的安全呢？</span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;在这篇文章中，介绍一种常见的保证api请求安全的做法--url签名。</span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;服务器中验证用户名和密码都正确后,生成一个随机的不重复的token字符串（例如"daf32da456hfdh"），在redis或memcache中维护一个映视表，建立token字符串和用户信息的对应关系表，例如，把token字符串"daf32da456hfdh"和用户id"5"对应起来，服务器把token字符串返回给app用作身份验证。&nbsp;</span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp; &nbsp; &nbsp; &nbsp;这个身份验证是依赖于token字符串。如果用户泄露了自己的url, 那很大程度上token也被别人泄漏了。</span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><br>
</span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp; &nbsp; &nbsp;2： 怎么防止token被泄露？不让token在网络上传输就行。</span><br>
</span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">(1)</span><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px; white-space:pre">
</span><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">服务器中验证用户名和密码都正确后，把token字符串和用户id都返回给客户端，例如token字符串"daf32da456hfdh"和用户id"5"。&nbsp;</span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;
 &nbsp; &nbsp; &nbsp; (2)假设api请求为"test.com/user/info", 通过token字符串"daf32da456hfdh"生成md5签名后: md5("test.com/user/info&amp;token=daf32da456hfdh”)= C99DC0C22437AC275C08CE4A9708B25A</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;于是，api请求加上签名和用户标识后就是&#12288;＂test.com/user/info?userId=5&amp;sign= C99DC0C22437AC275C08CE4A9708B25A＂</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp; &nbsp; &nbsp; &nbsp; (3)服务器接收到这个url后，用(2)的算法生成签名和sign参数对比，如果发现相等的话，就表示这个url是有有效,那就继续执行这个api调用。</span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp; &nbsp;&nbsp;</span><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">改进方法是在传递的参数中增加时间戳，当发现这个时间戳离现在的时间已经很久了，就判断这个url已经失效了。</span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;
 &nbsp; &nbsp;&nbsp;</span></span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;
 &nbsp; &nbsp;但用时间戳怎么保证app的时间和服务器的时间同步？在app每次启动时和服务器同步时间，然后在app内建一个时钟，时间戳在这个app的内部时钟获取，防止用户修改了手机的时间导致时间不一致。</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp; &nbsp; &nbsp; &nbsp;于是有了下面的改进方法：</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
&nbsp;<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&#12288;&#12288;(1)假设api请求为"test.com/user/info", 用token字符串"daf32da456hfdh"和时间戳生成md5签名后: md5("test.com/user/info?userId=5&amp;token=daf32da456hfdh&amp;timestamp=1425860757”)= C116161A6F430343B6CECF08562F1371</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;&#12288;&#12288;于是，api请求加上签名和用户标识后就是&#12288;＂test.com/user/info?userId=5&amp;timestamp=1425860757&amp;sign= C116161A6F430343B6CECF08562F1371＂</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;&#12288;&#12288;(2)服务器接收到这个api请求，如果发现收到这个url请求的时间和time=1425860757相隔很久，就判断出这个url是被别人截取来反复调用了。如果时间合法，那就用(1)的算法再判断sign是否一致</span></span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><br>
</span></span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;
 &nbsp; &nbsp;</span></span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;
 &nbsp; &nbsp; &nbsp;对http网络请求body传输内容进行加密解密（AES）：</span></span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; line-height:26px"><span style="font-size:14px">&nbsp;&nbsp;</span></span></span></span></span></p>
<h1 style="margin:0px; padding:0px; color:rgb(51,51,51); font-family:Arial; line-height:26px">
<span style="font-size:12px">&nbsp; &nbsp; &nbsp; </span><span style="font-size:14px">&nbsp;1.对称加密的原理</span></h1>
&nbsp;<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="font-size:14px; color:rgb(51,51,51); font-family:Arial; line-height:26px">&#12288;&#12288;采用单钥密码系统的加密方法，同一个密钥可以同时用作信息的加密和解密，这种加密方法称为对称加密，也称为单密钥加密。&nbsp;</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="font-size:14px; color:rgb(51,51,51); font-family:Arial; line-height:26px">&#12288;&#12288;其实很简单，假设有原始数据"1000", 把1000加５就得到密文"1005",得到密文"1005"后减５就得到原始数据"1000"。把原始数据加５就是加密算法，把密文减５就是解密算法，密钥就是5。&nbsp;</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="font-size:14px; color:rgb(51,51,51); font-family:Arial; line-height:26px">&#12288; &#12288;本文所用的是AES这种通用的对称加密算法。</span>
<p></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;
 &nbsp; &nbsp; &nbsp; 可以把上面Token或sign里的某一部分作为双方加密解密的key,</span></span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;
 &nbsp; &nbsp; &nbsp; 实际运用中 ，<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">data内容是实际要post方法提交的数据。&nbsp;</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&#12288;&#12288;在这个过程中,token和post的数据都得到了加密保护。</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
&nbsp; &nbsp; &nbsp;<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><strong>&#12288;&#12288;客户端发送时加密的过程</strong></span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&#12288;&#12288;(1)取http header中的Token-Param中的16位长度作为密钥，用AES加密token。</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&#12288;&#12288;(2)用token作为密钥，用AES加密data。</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
&nbsp;<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&#12288;&#12288;<strong>服务端接收到这个api请求的解密过程：</strong></span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
&nbsp;<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&#12288;&#12288;(1)取http header中的Token-Param中的16位长度作为密钥，用AES解密, 得到token。</span><br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<br style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&#12288;&#12288;(2)用token作为密钥，用AES解密http body的内容，得到原文。</span></span></span></span></span></p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">&nbsp;
 &nbsp; &nbsp;</span></span></span></span></p>
<p style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;把token返回的时候，可以做个约定，在返回的时候截取某个字符串的一部分作为密钥，这个秘钥只用一次，就是用来解密token的，以后就只是用token来做秘钥了。&nbsp;</p>
<p style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
&nbsp; &nbsp; &nbsp; &nbsp; 整个加密解密的过程如下（MD5+AES）：</p>
<p style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px">
1、用户名密码 + https + url签名（url+时间戳+随机字串）链接+请求时间+保唯一的字串<br>
2、服务器返回token：aes(约定算法)=》（token+随机secret(就取上面那个签名中的16位)）<br>
3、app保存token后，以后每次机通信都通过 &nbsp;aes (token + 内容) &nbsp;传输</p>
<p><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><span style="color:rgb(51,51,51); font-family:Arial; font-size:14px; line-height:26px"><br>
</span></span></span></span></p>

                      </div>