---
title: "Flutter + Firebase 初步体验"
date: 2021-01-31T17:10:04-05:00
---
上个学期的时候，因为疫情的缘故，我所在的学校为了控制食堂的人流量，引入了一款预约饭点的第三方移动应用。简单地说，这款应用的功能就是在完成身份验证后，让用户选取取餐的地点和时间，软件在点单完成后发送确认邮件，然后用户凭确认邮件去食堂取餐。可以说，业务并不复杂，实现得好应该也不难，但是不知为何这个APP设计得就很烂，用户体验特别差，预约步骤特别麻烦，每次需要：

指纹解锁APP -> 等待APP跳转到下一个界面 -> 点击“Start an order” -> 等待APP跳转到下一个界面 -> 选择预约地点 -> 等待APP加载数据 -> 选择日期时间 -> 等待APP加载数据 -> 将早饭/中饭/晚饭加入购物车 -> 等待APP跳转到下一个界面 -> 结算购物车 -> 等待确认邮件

整个流程下来如果运气好那么要30-45秒，如果运气不好，刚好处在点餐高峰期，应用后端便会不堪重负，前端数据永远加载不出来，然后应用直接崩溃挂掉。这款应用还会经常莫名其妙地闪退，或者出现整个界面失去响应，导致用户只能强制关闭。而且到了食堂要掏出手机，翻出那封可能早已经被其他邮件所淹没的确认邮件，给食堂门口那几位生无可恋的工作人员肉眼检票。刚开始大家还比较遵守规矩，会按照下单的时间和地点去食堂取饭，但到了学期中大家都被搞疲了，于是出现了五花八门各式蒙混工作人员的手段，有修图改确认邮件时间的，也有使用过期的邮件截图欺负食堂大妈眼神不好的，更有直接闯卡的。总而言之，这款应用不光使用体验巨差，也根本达不到原来控制人流量的设计目标。

寒假的时候闲来无事，就和另外两位CS系的同学，用Flutter和Firebase替学校的Dining Service开发了一款更加简单易用的预约软件，准备在春天投放使用，改善一下大家的预约订餐体验。比起原有的APP，新开发的APP主要有以下一些方面的改进：

- 改用Google Account一次验证登录用户，省去了每次解锁APP的流程
- 日期、地点和时间选择全部放在同一界面上，前端数据实时刷新，方便用户一键预约
- 在就餐地点部署PROX读卡设备，用户点单后可以直接刷卡进入
- 新增历史订单查看和取消功能

这个项目前前后后，断断续续大概折腾了一个月，最近总算是弄完了。这几天刚好有空，就胡乱写点东西记录记录开发过程，假装一下博客还在更新。


## 为什么选择Flutter+Firebase

选择Flutter最主要的原因是我对移动开发是两眼一抹黑，过去没有任何经验，需要一个比较好上手的技术栈，而且这个技术栈最好是跨平台的，这样就可以避免同时开发原生安卓和iOS的麻烦，同时APP的业务不复杂，所以即使跨平台的应用会牺牲一些性能，对于整体的用户体验也不会有太大的影响。最开始考虑用的跨平台方案是Facebook家的ReactNative，但问题在于我对JavaScript也是两眼一抹黑，对npm/node.js更是一窍不通，相比之下Flutter框架使用的语言Dart和Java、C++等语言非常相似，比较熟悉OOP的开发者能很快上手，即便不熟悉前端语言，也能鼓捣出个长得差不多的APP出来。

具体入门我是从[官方文档](https://flutter.dev/docs)开始的，Youtube上面我也看了不少优秀的课程，比如[Flutter Tutorial for Beginners](https://www.youtube.com/playlist?list=PL4cUxeGkcC9jLYyp2Aoh6hcWuxFDX6PBJ)和[Flutter & Firebase App Build](https://www.youtube.com/playlist?list=PL4cUxeGkcC9j--TKIdkb3ISfRbJeJYQwC)，基本上跟着课程走，不太懂的地方看点文档，就能了解Dart的语言特性和Flutter一些基础的组件。一些简单的代码可以放在[Dartpad](https://dartpad.dev/)里面在线编译，比较方便。在有了这些基础后，又跑到了[Flutter Gallery](https://gallery.flutter.dev/#/)和[Flutter Awesome](https://flutterawesome.com/)看了不少源码，算是对整个框架有了一些了解，下面就谈谈我的一些粗浅的观察。

### Dart/Flutter

- Flutter所用的Dart是门JIT语言，开发的时候可以快速地Hot Reload实时预览，进行测试、构建UI、修复错误，而不需要反复编译，节省了大量时间。同时，Dart的AOT特性意味着Release编译出来的ARM/x86机器码性能无限接近原生。当然Kotlin，C#之流的也有JIT/AOT，但奈何Kotlin开发这种轻量级的跨平台应用有点大炮打蚊子，C#的Xamarin有点半死不活的感觉，作为一个没什么经验的独立开发者Dart配合上Flutter开发移动应用还是比较不错的组合

- Dart分代GC效率挺高，至少在我开发的过程中没有碰到过GC造成APP卡顿的现象。在Dart中，年轻段的对象如Stateless Widgets的清理方法类似JVM中的复制算法+可达性分析算法，新建对象的时候用bumping allocator快速分配，当内存一半满后，将活动的对象复制到另外一半去，清空现有的一半。在另一半继续bump + 可达性分析 + 复制，如此反复。对象达到一定的生命周期后会被移到老年区，然后用mark-sweep清理。[据说](https://medium.com/flutter/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30)Flutter的对象基本都是满足weak generational hypothesis的，所以不太可能用到mark-sweep？对于我这种经常微操UI，没事就Hot Reload一下UI的人，对象大概的确满足weak generational hypothesis吧

- 有一些用着非常舒服的语法糖比如 `?.`和
`??`。关键词 `asnyc`、 `await`和 `Future`极大简化了各种异步操作

- 最重要的，用Flutter写UI布局很容易上手，适合对前端一无所知的人（比如我）。Flutter中一切组件都是以widget的形式存在，比如 `padding`, `center`, `column`什么的，想要自定义加几个参数就够了，同时Flutter打包了iOS和Material各种现成的widget，可以无脑调用，就算直男审美不会美工，撸个简单不丑的界面也不是什么特别大的问题。刷新widget也很无脑，用 `setState`更新数据，然后Flutter会根据当前改变的widget的`build`方法重新渲染整个widget子树

当然Flutter也有很多令人抓狂的问题。首先Flutter的轮子非常少，一些稍微少见的功能都需要自己去改原生组件的代码，比如 `DropdownButtonFormField`的长度没法调节，菜单内若有过多的element会占满整个屏幕空间，想要要限定高度只能自己手写组件，这个[issue](https://github.com/flutter/flutter/issues/23865)在GitHub上已经快三年了，至今解决无望。另外还有一些非常奇葩的bug，比如在带有GMS的模拟器跑APP，terminal经常会蹦出 `java.lang.IllegalArgumentException: Service not registered: lu@bc4a540`一类的exception错误，具体是什么原理的我并不是特别清楚，但既然似乎不影响程序运行，那也就随它去了。考虑到Flutter在GitHub上有八千多个没有解决掉的issue，这些都是小事情。


### Firebase作为BaaS

既然用了Dart/Flutter框架，Firebase自然成了后端的首选。一方面，Firebase作为Google的亲儿子，和Dart/Flutter无缝对接，另一方面，Firebase作为BaaS打包解决了用户验证、数据库储存、Google Analytics等一系列后端功能。有了Firebase就不用花时间自己去配置各种SDK、搭建服务器或者折腾各种SQL。比如实现第三方谷歌用户登录，直接几行代码搞定：
```dart
// ...    
  
final FirebaseAuth _auth = FirebaseAuth.instance;  
final GoogleSignIn googleSignIn = GoogleSignIn();  
  
// 监听用户认证信息  
  
_auth.onAuthStateChanged(function(user) {});  
  
// 用户登录  
  
Future signInWithGoogle() async {  
  final GoogleSignInAccount googleSignInAccount = await googleSignIn.signIn();  
  if (googleSignInAccount == null) {  
  
    // cancelled login  
  
    print('ERROR! googleUser: null!');  
    return null;  
  
  }  
  
  final GoogleSignInAuthentication googleSignInAuthentication = await googleSignInAccount.authentication;  
  
  final AuthCredential credential = GoogleAuthProvider.credential(  
    accessToken: googleSignInAuthentication.accessToken,  
    idToken: googleSignInAuthentication.idToken,  
  
  );  
  
  UserCredential result = await _auth.signInWithCredential(credential);  
  
}  
  
// ...
```

认证过的用户有一串`uid`，可以用这串`uid`在`firestore`下给用户建document管理数据。Firebase数据库比较优秀的一点是开发者可以在客户端监听数据库的变化，并实时获取数据库的snapshot；同时在客户端改变用户数据，Firebase后端的GUI会实时刷新，比较傻瓜，非常适合我这种不懂数据库的人调试后端。具体Firebase的一些奇技淫巧可以看[这里](https://firebase.google.com/docs/flutter/setup)。

## 未来可以改进的一些地方

最后的成品大概长[这样](https://play.google.com/store/apps/details?id=com.amherst.mammoth_meals)，虽然UI还是有点丑，但以我的直男审美，恐怕也做不出来更好的了，所以就先这样投入使用了，学期中如果有时间再根据用户反馈慢慢改进。


由于时间的关系，很多东西并没有能较好的实现。比如APP的前端数据是实时刷新的，现版本的解决方案比较粗暴，直接开了两个timer，一个负责每五分钟从后台API抓取当日还剩余餐位的时间点，另外一个负责每分钟在前台刷新UI，然后根据本机的时间，筛选不符合条件的饭点（ie. 过了时间、人数已满 etc.）。后来发现如果后台杀不掉的话，这俩timer就会不停运行，向后端发送API请求。为了解决这个问题，我打了个 `WidgetsBindingObserver`的补丁，让应用监视自己是不是在前台，如果在前台则启动timer，如果在后台，那暂停timer。对于那些刷新频率不高的应用，用 `WidgetsBindingObserver`还是能比较好地平衡前端刷新和后端压力的。但对于一些需要快速实时更新数据，或许[Stream Builder](https://api.flutter.dev/flutter/widgets/StreamBuilder-class.html)是个更好的选择。

## 一些碎念

一个月折腾下来，移动开发个事情怎么说呢，挺繁琐且无趣，整个项目很大的一部分时间都花在各种打断点抓虫上，查文档解决各类奇葩问题，测试集成队友的代码，或者和学校的各官僚部门扯皮上，真正写UI写逻辑的时间并不多。对于这种架构比较简单的APP，也没有什么特别难实现的地方，在绝大部分现有的开发框架下，难写的组件都被打包好了，剩下的工作就是调个包，随便找个对框架语言有一定了解且善于使用搜索引擎的程序员，给台能上GitHub和Stack Overflow的电脑，再给够时间，东拼西凑一下，也就差不多了。

软件开发有的人说它是门科学，有的人说它是门艺术，还有人说它是门工程学。在我看来，软件开发不是科学，因为搞开发肯定不在探求宇宙真理，似乎也不在弄理论性研究拓宽人类知识的边界。软件开发也不太像是门艺术，至少我没觉得我在我写的代码里面抒发了任何情感，也没有在打了补丁的方法中找到什么和谐的美感。软件开发也许是工程学，涉及到计算机底层原理的函数、类库和各种架构的编写，或许可以被称为工程，但绝大多数套用层层的框架，来回拖拽类库的软件开发，顶多是土法炼钢罢了。那么如果软件开发不是科学，不是艺术，绝大部分场景下也不太像门工程，那么它是什么呢？我认为它更是门手艺活，好比华强北画PCB板子的老板，他不是科学家，不是艺术家，也不是工程师，他不需要研究固态物理，不需要在PCB板子里表达自我，也不需要运用数学物理知识优化板子，但是他能熟练运用各种现有工具把板子给整出来。
