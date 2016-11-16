---
layout: post
title:  "从session id到session及cookie"
date:   2015-10-26 16:41:03 +0800
categories: jekyll update
---


#### 一、Session 

1.Session，在汉语中表示通话、会话、对话(期)、话路［对谈时间］的意思，其本来的含义一个终端用户与交互系统进行通信的时间(间隔),通常是指从注册(进入系统)到注销(退出系统)之间所经过的时间。
比如打电话时从拿起电话拨号到挂断电话这中间的一系列过程可以称之为一个Session。    
2.有时候我们可以看到这样的话“在一个浏览器会话期间，…”，这里的会话一词用的就是这个意思，是指从一个浏览器窗口打开到关闭这个期间。    
3.Session在我们的网络应用中就是一种客户端与服务器端保持状态的解决方案，有时候Session也用来指这种解决方案的存储结构；    
4.Session对象，就是客户端浏览器与服务器之间建立的互动信息状态。每一个不同的用户连接将得到不同的Session，也就是说Session与用户之间是一种一对一的关系。    
Session在用户进入网站时由服务器自动产生，并在用户正常离开站点时释放。    
使用Session的好处就在于，可以将很多与用户相关的信息，例如用户的帐号、昵称等保存到Session中；利用Session，可以跟踪用户在网站上的活动。例如：当你上网进入一个网站时，如果你没有登陆，无论你访问哪几个页面都会跳转回登陆页。还有就是你在购物时，不可能把你的东西放到别人的购物车里去，这就得用一个信息变量来判断！     
如果能够提供一些按需生成的动态信息会使web变得更加有用，就像给有线电视加上点播功能一样。这种需求一方面迫使HTML逐步添加了表单、脚本、DOM等客户端行为，另一方面在服务器端则出现了CGI规范以响应客户端的动态请求，作为传输载体的HTTP协议也添加了文件上载、cookie这些特性。其中cookie的作用就是为了解决HTTP协议无状态的缺陷所作出的努力。至于后来出现的Session机制则是又一种在客户端与服务器之间保持状态的解决方案。    

#### 二、Cookies 
    
Cookie是WEB上最常用的跟踪用户会话方式，当 Cookie被禁止后，一般都用URL重写来跟踪会话。Cookie是一种由服务器发送给客户的片段信息，存储在客户环境中，并在客户所有的对服务器的请求中都要发回它。就好比我们在用IE登陆某个电子购物商城时，IE在得到商品列表页面的 同时还收到Set-Cookie应答头信息，我们打开一个Cookie文件，我们所看到的格式一般都是：     
Cookie：NAME=VALUE；Comment=COMMENT；Domain=DOMAINNMAM；Max-age=SECONDS；Path=PATH；secure；Version=1*DIGIT     
其中NAME值对（值对间用分号分隔）是必须的，其余都是可选的。最重要的信息当然也在所必须的值对里了，VALUE是NAME的值，也是这个Cookie的标识，Max-age定义了Cookie的最长生存时间，其它几个可选值对可参阅http://www.faqs.org/rfcs/rfc2109.html。当我们选购了某种商品，向服务器发送选购清单时，会自动在你的请求信息头里加上NAME值对,如果Cookie被禁止，则用URL重写方式在URL请求地址上附加NAME值对。当Web服务器收到这个请求后，会检查该Cookie是否存在，然后相应的跟踪会话。从以上分析不难理解，其实Web服务器跟踪会话就靠Set-Cookie头信息，跟踪NAME值对进行身份验证。假如我们用非Web终端接收Web服务器的响应信息，从中解析出Cookie头信息，当再次向Web服务器发送请求时附加上解析出的Cookie信息，Web服务器据此不就可以进行身份认证了吗？     
Cookies中文是饼干的意思，对于为何引用Cookies，从网上查找了一些资料：     
在浏览器与WEB服务器之间是使用HTTP协议进行通信的，当某个用户发出页面请求时，WEB服务器只是简单的进行响应，然后就关闭与该用户的连接。因此当一个请求发送到WEB服务器时，无论其是否是第一次来访，服务器都会把它当作第一次来对待，这样的不好之处可想而知。为了弥补这个缺陷，Netscape开发出了cookie这个有效的工具来保存某个用户的识别信息，因此人们昵称为“小甜饼”。cookies是一种WEB服务器通过浏览器在访问者的硬盘上存储信息的手段：Netscape Navigator使用一个名为cookies.txt本地文件保存从所有站点接收的Cookie信息；而IE浏览器把Cookie信息保存在类似于c:\Internet 临时文件\的目录下。当用户再次访问某个站点时，服务端将要求浏览器查找并返回先前发送的Cookie信息，来识别这个用户。Cookies给网站和用户带来的好处：     
　　(1)、Cookie能使站点跟踪特定访问者的访问次数、最后访问时间和访问者进入站点的路径     
　　(2)、Cookie能告诉在线广告商广告被点击的次数，从而可以更精确的投放广告     
　　(3)、Cookie有效期限未到时，Cookie能使用户在不键入密码和用户名的情况下进入曾经浏览过的一些站点     
(4)、Cookie能帮助站点统计用户个人资料以实现各种各样的个性化服务，其实，cookie的作用就是为了解决HTTP协议无状态的缺陷所作的努力.    

#### 三．Cookie机制     

Cookie机制采用的是在客户端保持状态的方案。     
Cookie机制，就是当服务器对访问它的用户生成了一个Session的同时服务器通过在HTTP的响应头中加上一行特殊的指示以提示浏览器按照指示生成相应的cookie，保存在客户端，里面记录着用户当前的信息，当用户再次访问服务器时，浏览器检查所有存储的cookie，如果某个cookie所声明的作用范围大于等于将要请求的资源所在的位置也就是对应的Cookie文件。 若存在，则把该cookie附在请求资源的HTTP请求头上发送给服务器，例如：当我们登陆了一个网站，并且填写了有关资料，以本站会员的名义登陆上了有关网页，这时你把浏览器关闭，再重启进入该网站的某一个页面时是以你登陆过的会员进去的，当然，不是所有网站都是这样，我们知道，cookie的保存有临时性的和持久性的，大多都是临时性的，也就是cookie只保存在客户端的内存中，而没有保存在硬盘上，当关闭浏览器，cookie也就销毁。以下是有关cookie机制的一些具体说明：     
cookie的内容主要包括：名字，值，过期时间，路径和域。     
其中域可以指定某一个域比如.google.com，相当于总店招牌，比如宝洁公司，也可以指定一个域下的具体某台机器比如www.google.com或者froogle.google.com，可以用飘柔来做比。     
路径就是跟在域名后面的URL路径，比如/或者/foo等等，可以用某飘柔专柜做比。路径与域合在一起就构成了cookie的作用范围。     
如果不设置过期时间，则表示这个cookie的生命期为浏览器会话期间，只要关闭浏览器窗口，cookie就消失了。这种生命期为浏览器会话期的 cookie被称为会话cookie。会话cookie一般不存储在硬盘上而是保存在内存里，当然这种行为并不是规范规定的。如果设置了过期时间，浏览器就会把cookie保存到硬盘上，关闭后再次打开浏览器，这些cookie仍然有效直到超过设定的过期时间。     
存储在硬盘上的cookie可以在不同的浏览器进程间共享，比如两个IE窗口。而对于保存在内存里的cookie，不同的浏览器有不同的处理方式。对于微软的IE浏览器，在一个打开的窗口上按Ctrl-N（或者从文件菜单）打开的窗口可以与原窗口共享，而使用其他方式新开的IE进程则不能共享已经打开的窗口的内存cookie；对于火狐狸firefox浏览器，所有的进程和标签页都可以共享同样的cookie。一般来说是用javascript的window.open打开的窗口会与原窗口共享内存cookie。浏览器对于会话cookie的这种只认cookie不认人的处理方式经常给采用Session机制的web应用程序开发者造成很大的困扰。    

#### 四、Session机制     

Session机制采用的是在服务器端保持状态的方案。     
当用户访问到一个服务器，服务器就要为该用户创建一个SESSION，在创建这个SESSION的时候，服务器首先检查这个用户发来的请求里是否包含了一个SESSIONID，如果包含了一个SESSIONID则说明之前该用户已经登陆过并为此用户创建过SESSION，那服务器就按照这个SESSIONID把这个SESSION在服务器的内存中查找出来（如果查找不到，就有可能为他新创建一个），如果客户端请求里不包含有SESSIONID，则为该客户端创建一个SESSION并生成一个与此SESSION相关的SESSIONID。这个SESSIONID是唯一的、不重复的、不容易找到规律的字符串，这个SESSIONID将被在本次响应中返回到客户端保存，而保存这个SESSIONID的正是COOKIE，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。     
我们知道在IE中，我们可以在工具的internet选项中把COOKIE禁止，那么会不会出现把客户端的COOKIE禁止了，SESSIONID就无法再用了呢？找了一些资料说明，可以有其他机制在COOKIE被禁止时仍然能够把Session id传递回服务器。经常被使用的一种技术叫做URL重写，就是把Session id直接附加在URL路径的后面一种是作为URL路径的附加信息,表现形式为：     
http://…./xxx;jSession=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764；     
另一种是作为查询字符串附加在URL后面，表现形式为：     
http://…../xxx?jSession=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764     
还有一种就是表单隐藏字段。就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把Session id传递回服务器。这里就不介绍了。     
我们常说的在一个IE被打开是创建一个Session，当关闭IE时Session也就被删除，事实上，除非程序通知服务器删除Session，否则Session会被服务器一直保留，直到Session的失效时间到了自动删除。服务器不知道IE被关闭，IE不会主动在其关闭之前通知服务器它将要关闭。程序一般都是在用户做注销时删除Session。我们产生这种错觉的原因是：一般Session机制都使用cookie来保存Session id，而一旦关闭IE浏览器，Session id就不存在了，再连接服务器时找不到原来的Session了.如果服务器设置的cookie被保存到硬盘上，或者使用某种手段改写浏览器发出的 HTTP请求头，把原来的Session id发送给服务器，则再次打开浏览器仍然能够找到原来的Session。恰恰是由于关闭浏览器不会导致Session被删除，迫使服务器为seesion设置了一个失效时间，当距离客户端上一次使用Session的时间超过这个失效时间时，服务器就可以认为客户端已经停止了活动，才会把Session删除以节省存储空间。     
一般情况下，Session都是存储在内存里，当服务器进程被停止或者重启的时候，内存里的Session也会被清空，如果设置了Session的持久化特性，服务器就会把Session保存到硬盘上，当服务器进程重新启动或这些信息将能够被再次使用。    

#### 五、cookie机制与Session机制的区别和联系     

具体来说cookie机制采用的是在客户端保持状态的方案，而Session机制采用的是在服务器端保持状态的方案。同时我们也看到，由于在服务器端保持状态的方案在客户端也需要保存一个标识，所以Session机制可能需要借助于cookie机制来达到保存标识的目的，但实际上还有其他选择。例如，我们经常用到的会员卡，也就相当于这种情况。消费到了一定程度就有奖，就如下面例子说明：     
1.发给顾客一张卡片，上面记录着消费的数量，一般还有个有效期限。每次消费时，如果顾客出示这张卡片，则此次消费就会与以前或以后的消费相联系起来。这种做法就是在客户端保持状态。     
2、发给顾客一张会员卡，除了卡号之外什么信息也不纪录，每次消费时，如果顾客出示该卡片，则店员在店里的纪录本上找到这个卡号对应的纪录添加一些消费信息。这种做法就是在服务器端保持状态。     
以下是一些关于两者的区别与联系：     
具体来说cookie机制采用的是在客户端保持状态的方案。它是在用户端的会话状态的存贮机制，他需要用户打开客户端的cookie支持。cookie的作用就是为了解决HTTP协议无状态的缺陷所作的努力.而Session机制采用的是一种在客户端与服务器之间保持状态的解决方案。同时我们也看到，由于采用服务器端保持状态的方案在客户端也需要保存一个标识，所以Session机制可能需要借助于cookie机制来达到保存标识的目的。而Session提供了方便管理全局变量的方式。     
Session是针对每一个用户的，变量的值保存在服务器上，用一个Session来区分是哪个用户Session变量,这个值是通过用户的浏览器在访问的时候返回给服务器，当客户禁用cookie时，这个值也可能设置为由get来返回给服务器。     
就安全性来说：当你访问一个使用Session 的站点，同时在自己机子上建立一个cookie，建议在服务器端的SESSION机制更安全些.因为它不会任意读取客户存储的信息。     
正统的cookie分发是通过扩展HTTP协议来实现的，服务器通过在HTTP的响应头中加上一行特殊的指示以提示浏览器按照指示生成相应的cookie。     
从网络服务器观点看所有HTTP请求都独立于先前请求。就是说每一个HTTP响应完全依赖于相应请求中包含的信息.     
状态管理机制克服了HTTP的一些限制并允许网络客户端及服务器端维护请求间的关系。在这种关系维持的期间叫做会话(Session)。     
Cookies是服务器在本地机器上存储的小段文本并随每一个请求发送至同一个服务器。IETF RFC 2965 HTTP State Management Mechanism 是通用cookie规范。网络服务器用HTTP头向客户端发送cookies，在客户终端，浏览器解析这些cookies并将它们保存为一个本地文件，它会自动将同一服务器的任何请求缚上这些cookies 。     
来看一下Tomcat是如何实现web应用程序之间session的隔离的，从Tomcat设置的cookie路径来看，它对不同的应用程序设置的cookie路径是不同的，这样不同的应用程序所用的session id是不同的，因此即使在同一个浏览器窗口里访问不同的应用程序，发送给服务器的session id也可以是不同的。    
 
#### 六、常见问题     

1、Session在何时被创建     

Session在有客户端访问时就被创建，然而事实是直到某server端程序调用HttpServletRequest.getSession(true)这样的语句时才被创建，注意如果JSP没有显示的使用 <%@page Session=”false”%> 关闭Session，则JSP文件在编译成Servlet时将会自动加上这样一条语句HttpSession Session = HttpServletRequest.getSession(true);这也是JSP中隐含的Session对象的来历。由于Session会消耗内存资源，因此，如果不打算使用Session，应该在所有的JSP中关闭它。     

2、Session何时被删除   
  
综合前面的讨论，Session在下列情况下被删除：     
a.程序调用HttpSession.invalidate();     
b.距离上一次收到客户端发送的Session id时间间隔超过了Session的超时设置;     
c.服务器进程被停止（非持久Session）。     
3、如何做到在浏览器关闭时删除Session     
严格的讲，做不到这一点。可以做一点努力的办法是在所有的客户端页面里使用javascript代码window.oncolose来监视浏览器的关闭动作，然后向服务器发送一个请求来删除Session。但是对于浏览器崩溃或者强行杀死进程这些非常规手段仍然无能为力。     
4、有个HttpSessionListener是怎么回事     
你可以创建这样的listener去监控Session的创建和销毁事件，使得在发生这样的事件时你可以做一些相应的工作。注意是Session的创建和销毁动作触发listener，而不是相反。类似的与HttpSession有关的listener还有HttpSessionBindingListener，HttpSessionActivationListener和 HttpSessionAttributeListener。     
5、存放在Session中的对象必须是可序列化的吗     
不是必需的。要求对象可序列化只是为了Session能够在集群中被复制或者能够持久保存或者在必要时server能够暂时把Session交换出内存。在Weblogic Server的Session中放置一个不可序列化的对象在控制台上会收到一个警告。     
6、开两个浏览器窗口访问应用程序会使用同一个Session还是不同的Session     
对Session来说是只认id不认人，因此不同的浏览器，不同的窗口打开方式以及不同的cookie存储方式都会对这个问题的答案有影响。     
7、如何防止用户打开两个浏览器窗口操作导致的Session混乱     
这个问题与防止表单多次提交是类似的，可以通过设置客户端的令牌来解决。就是在服务器每次生成一个不同的id返回给客户端，同时保存在Session里，客户端提交表单时必须把这个id也返回服务器，程序首先比较返回的id与保存在Session里的值是否一致，如果不一致则说明本次操作已经被提交过了。可以参看《J2EE核心模式》关于表示层模式的部分。需要注意的是对于使用javascript window.open打开的窗口，一般不设置这个id，或者使用单独的id，以防主窗口无法操作，建议不要再window.open打开的窗口里做修改操作，这样就可以不用设置。     
8、为什么Session不见了     
排除Session正常失效的因素之外，服务器本身的可能性应该是微乎其微的；理论上防火墙或者代理服务器在cookie处理上也有可能会出现问题。出现这一问题的大部分原因都是程序的错误，最常见的就是在一个应用程序中去访问另外一个应用程序。     
9、服务器关掉后，当前Session会丢掉吗     
这个取决于你使用什么样的web服务器以及web服务器是如何配置的。tomcat在shutdown前默认会自动将Session保存到指定的目录中，重新启动是重新加载，因此tomcat重新启动后，Session是可以继续使用的。此外，你还何以将Session保存到数据库中，这个要在server.xml中配置。     
10、Cookie的过期和Session的超时有什么区别     
会话的超时由服务器来维护，它不同于Cookie的失效日期。首先，会话一般基于驻留内存的cookie不是持续性的cookie，因而也就没有截至日期。即使截取到JSESSION cookie，并为它设定一个失效日期发送出去。浏览器会话和服务器会话也会截然不同。     




session_id()  是一个重载函数，

session_id() 可以用来获取/设置 当前会话 ID。 

语法: string session_id(string [id]);

本函数可取得或者重新配置目前存放 Session 的代号。若无参数 id 则表示只有取得目前 Session 的代号，加上参数则表示将 Session 代号设成新指定的 id。输入及返回均为字符串。  
####在PHP中输出 session_id() 的值

	session_start(); 
	echo session_id();
	// 输出 dqr58dnuqj2gufvg4o3tmjb9v4

####设置 session_id()

	session_id("NowaMagic");
	session_start(); 
	
	echo session_id();
	// 输出 NowaMagic


####session_id 恢复 session的内容

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;php的session是可以程序恢复的，这个和java不太一样。session的恢复机制可以实现多个应用程序session的共享，因为php的session都是以文件形式或者数据库存储的。首先是session_id的获取是通过session_id()函数获取，这个值可以进行传递。</p>

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;程序恢复session，首先要知道session_id，大家通过手册可以知道session的恢复通过session_id($id);但是在恢复时要注意一个先后顺序，要得到之前session的内容，必须在session_start()之前执行session_id($id)，这样才能在执行了session_start时初始化session的时候恢复到之前的内容，否则的话你得到的是一个空的session，你什么也得不到。之前session被重新初始化了。这个和session_start()的作用有密切关系，因为session_start告诉php，session要初始化，要从session文件中反序列化session内容，所以session_start的作用就是把之前存储的文件内容反序列化。session_start序列化之前要知道session_id，如果没有就生成一个新的session_id。如果有就反序列化相应文件的内容。</p>
	
![1](/images/cookie-session.png)


session机制是这一总服务器端的机制，服务器使用一种类似散列表的结构来保存信息。

>1.当程序需要为某个客户端的请求创建一个 session的时候，服务器首先检查该客户端的请求里是否包含了一个 session标记----称为 session id；  
2.如果已包含一个 session id则说明以前已经为该客户端创建过 session，服务器就 session id把这个 session检索出来使用 （如果检索不到，可能会新建一个）；  
3.如果客户端请求不包含 session id，则为该客户端创建一个 session并生成一个与此 session相关联的 session id；  
4.session id的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串；  
5.这个session id将被在本次响应中返回给客户端并保存。  
6.在客户端保存这个session id的方式默认采用cookie，这样在交互过程中浏览器可以自动的按照协议规则把这个标记发送给服务器。  
7.一般这个客户端的cookie的名字都类似于session id，不过这个名字是服务端可以设定的，那么可能也会是其他名字：
比如weblogic对于web应用程序生成的cookie，JSESSIONID= ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764，它的名字就是 JSESSIONID。
8.由于cookie可以被人为的禁止，必须有其他机制以便在cookie被禁时仍能把session id传递回服务器。经常被使用的一种技术叫做URL重写，就是把session id直接附加在URL路径的后面，附加方式也有两种，一种是作为URL路径的附加信息，组合形式为http://...../xxx;jsessionid=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764
另一种方式是作为查询字符串附加在URL后面，表现形式为http://...../xxx?jsessionid=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764

不过，这两种方式对用户来说是没有区别的，只是在服务器解析处理的时候所采用的解析手段不同，而采用第一种方式也有利于把session id的信息和正常程序参数区分开来。
而为了客户端与服务端在整个交互过程中始终保持一些状态，就必须在每个客户端可能请求的路径后面都包含这个session id。

另一种技术叫做表单隐藏自动。就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把session id传递回服务器。比如下面的表单

<form name="testform" action="/xxx">
<input type="text">
</form>
这个表单在信息被传递给客户端之前将会被改成
<form name="testform" action="/xxx">
<input type="hidden" name="jsessionid" value="ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764">
<input type="text">
</form>

这种技术现在已较少应用
实际上这个技术可以见到的用对action应用URL重写来代替。


在谈论session机制的时候，常常听到这样一种误解<font color=red> [只要关闭浏览器，session就消失了] </font>。其实可以想像一下会员卡的例子，除非顾客主动对店家提出销卡，否则店家绝对不会轻易删除顾客的资料。对session来说也是一样的，除非程序通知服务器删除一个session，否则服务器会一直保留，程序一般都是在用户做log off的时候发个指令去删除session。然而浏览器从来不会主动在关闭之前通知服务器它将要关闭，因此服务器根本不会有机会知道浏览器已经关闭，之所以会有这种错觉，是大部分session机制都使用会话cookie来保存session id，而关闭浏览器后这个 session id就消失了，再次连接服务器时也就无法找到原来的session。如果服务器设置的cookie被保存到硬盘上，或者使用某种手段改写浏览器发出的HTTP请求头，把原来的session id发送给服务器，则再次打开浏览器仍然能够找到原来的session。
恰恰是由于关闭浏览器不会导致session被删除，迫使服务器为seesion设置了一个失效时间，当距离客户端上一次使用session的时间超过这个失效时间时，服务器就可以认为客户端已经停止了活动，才会把session删除以节省存储空间。

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

由JSESSIONID谈cookie与SESSION的区别和联系
在一些投票之类的场合，我们往往因为公平的原则要求每人只能投一票，在一些WEB开发中也有类似的情况，这时候我们通常会使用COOKIE来实现，例如如下的代码：
< % cookie[]cookies = request.getCookies();
if (cookies.lenght == 0 || cookies == null)
doStuffForNewbie();
//没有访问过
}

else
{
doStuffForReturnVisitor(); //已经访问过了
}

% >

这是很浅显易懂的道理，检测COOKIE的存在，如果存在说明已经运行过写入COOKIE的代码了，然而运行以上的代码后，无论何时结果都是执行doStuffForReturnVisitor()，通过控制面板-Internet选项-设置-察看文件却始终看不到生成的cookie文件，奇怪，代码明明没有问题，不过既然有cookie，那就显示出来看看。
cookie[]cookies = request.getCookies();
if (cookies.lenght == 0 || cookies == null)
out.println("Has not visited this website");2
}

else
{
for (int i = 0; i < cookie.length; i++)
{
out.println("cookie name:" + cookies[i].getName() + "cookie value:" +
cookie[i].getValue());
}
}

运行结果:
cookie name:JSESSIONID cookie value:KWJHUG6JJM65HS2K6 为什么会有cookie呢,大家都知道，http是无状态的协议，客户每次读取web页面时，服务器都打开新的会话，而且服务器也不会自动维护客户的上下文信息，那么要怎么才能实现网上商店中的购物车呢，session就是一种保存上下文信息的机制，它是针对每一个用户的，变量的值保存在服务器端，通过SessionID来区分不同的客户,session是以cookie或URL重写为基础的，默认使用cookie来实现，系统会创造一个名为JSESSIONID的输出cookie，我们叫做session cookie（会话cookie）,以区别persistent cookies（持久cookie）,也就是我们通常所说的cookie,注意session cookie是存储于浏览器内存中的，并不是写到硬盘上的，这也就是我们刚才看到的JSESSIONID，我们通常情是看不到JSESSIONID的，但是当我们把浏览器的cookie禁止后，web服务器会采用URL重写的方式传递Sessionid，我们就可以在地址栏看到sessionid=KWJHUG6JJM65HS2K6之类的字符串。
明白了原理，我们就可以很容易的分辨出persistent cookies和session cookie的区别了，网上那些关于两者安全性的讨论也就一目了然了，session cookie针对某一次会话而言，会话结束session cookie也就随着消失了，而persistent cookie只是存在于客户端硬盘上的一段文本（通常是加密的），而且可能会遭到cookie欺骗以及针对cookie的跨站脚本攻击，自然不如session cookie安全了。
通常session cookie是不能跨窗口使用的，当你新开了一个浏览器窗口进入相同页面时，系统会赋予你一个新的sessionid，这样我们信息共享的目的就达不到了，此时我们可以先把sessionid保存在persistent cookie中，然后在新窗口中读出来，就可以得到上一个窗口SessionID了，这样通过session cookie和persistent cookie的结合我们就实现了跨窗口的session tracking（会话跟踪）。
在一些web开发的书中，往往只是简单的把Session和cookie作为两种并列的http传送信息的方式，session cookies位于服务器端，persistent cookie位于客户端，可是session又是以cookie为基础的，明白的两者之间的联系和区别，我们就不难选择合适的技术来开发web service了。
