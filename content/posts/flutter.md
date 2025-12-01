---
title: "A Taste of Flutter + Firebase"
date: 2021-01-31T17:10:04-05:00
---
上个学期，因为疫情的缘故，我所在的学校为了控制食堂人流，引入了一款预约饭点的第三方移动应用。简单地说，这个应用的流程基本是：用户完成身份验证后，选择取餐地点和时间；下单后，系统会发送一封确认邮件，用户再凭这封邮件去食堂取餐。可以说，业务并不复杂，实现得好应该也不难，但是不知为何这个APP设计得就很烂，用户体验极差，预约流程又长又繁琐，每次点一顿饭大致都要经历下面这一长串步骤：

指纹解锁APP -> 等待APP跳转到下一个界面 -> 点击“Start an order” -> 等待APP跳转到下一个界面 -> 选择预约地点 -> 等待APP加载数据 -> 选择日期时间 -> 等待APP加载数据 -> 将早饭/中饭/晚饭加入购物车 -> 等待APP跳转到下一个界面 -> 结算购物车 -> 等待确认邮件

整个流程如果运气好，大概要 30–45 秒，如果运气不好，刚好处在点餐高峰期，应用后端便会不堪重负，前端数据一直加载不出来，应用不是卡死就是直接崩溃。这款应用还会经常莫名其妙地闪退，或者出现整个界面失去响应，只能强制关闭重来一遍。

到了食堂门口，还得掏出手机，在邮箱里翻那封早就被其他邮件淹没的确认邮件，给食堂门口那几位生无可恋的工作人员肉眼检票。刚开始大家还比较遵守规矩，会按照下单的时间和地点去食堂取饭，但到了学期中，大家被折腾得心力交瘁，于是出现了五花八门各式蒙混工作人员的手段，有的把确认邮件 P 图修改时间，有的拿过期邮件截图去欺负食堂阿姨视力不好，更有干脆直接闯卡的。总而言之，这款应用不光使用体验巨差，也根本达不到最初控制人流量的设计目标。

寒假的时候正好比较清闲，我就和另外两位 CS 系的同学，打算替学校的 Dining Service 做点事，用 Flutter 和 Firebase 开发了一款更简单易用的预约应用，准备在春季学期投入使用，改善大家的订餐体验。相较原来的 APP，新开发的 APP 主要有以下一些方面的改进：

- 使用 Google Account 一次性登录认证，省去每次解锁 APP 的步骤
- 日期、地点和时间选择全部放在同一界面上，前端数据实时刷新，方便用户一键预约
- 在就餐地点部署PROX读卡设备，用户点单后可以直接刷卡进入
- 新增历史订单查看和取消功能，方便修改或取消预约

整个项目断断续续折腾了大概一个月，最近总算告一段落。这几天刚好有空，就胡乱写点东西记录记录开发过程，假装一下这个博客还在正常更新。


## 为什么选择Flutter+Firebase

选择Flutter最主要的原因，是我对移动开发是两眼一抹黑，过去没有任何经验，需要一套比较好上手的技术栈，而且最好还是跨平台的，这样一来，就可以免去同时开发原生 Android 和 iOS 的麻烦。再加上这个项目本身业务并不复杂，即便跨平台方案在性能上略有损失，对整体用户体验的影响也不会太大。

一开始考虑的跨平台方案其实是Facebook家的ReactNative，但问题在于我对JavaScript也是两眼一抹黑，对npm/node.js更是一窍不通。Flutter使用的Dart 语言在语法风格上和Java、C++等都非常接近，对于稍微熟悉一点OOP的开发者来说，上手成本要小得多。即便不熟悉前端语言，也能鼓捣出个长得差不多的APP出来。

具体入门我是从[官方文档](https://flutter.dev/docs)看起的。YouTube 上也有不少优质课程，比如[Flutter Tutorial for Beginners](https://www.youtube.com/playlist?list=PL4cUxeGkcC9jLYyp2Aoh6hcWuxFDX6PBJ)和[Flutter & Firebase App Build](https://www.youtube.com/playlist?list=PL4cUxeGkcC9j--TKIdkb3ISfRbJeJYQwC)。基本上跟着课程走，不太懂的地方看点文档，就能了解Dart的语言特性和Flutter一些基础的组件。一些简单的代码可以放在[Dartpad](https://dartpad.dev/)里面在线编译，比较方便。在有了这些基础后，又跑到了[Flutter Gallery](https://gallery.flutter.dev/#/)和[Flutter Awesome](https://flutterawesome.com/)看了不少源码，总算对整个框架有了点感性认识，下面就谈谈我的一些粗浅的观察。

### Dart/Flutter

- Flutter所用的Dart是门JIT语言，开发的时候可以快速地Hot Reload实时预览，进行测试、构建UI、修复错误，而不需要反复完整编译，大大节省了时间。同时，Dart的AOT特性意味着Release编译出来的ARM/x86机器码性能在理论上可以无限接近原生。当然Kotlin，C#之流的也有JIT/AOT，但奈何Kotlin开发这种轻量级的跨平台应用总有点大炮打蚊子的感觉，而C#的Xamarin有点半死不活的感觉。作为一个没什么经验的独立开发者，Dart配合上Flutter开发移动应用还是比较不错的组合

- Dart分代GC效率相当不错，至少在我开发的过程中，从未遇到过明显的GC卡顿。在Dart中，年轻代对象（比如各种Stateless Widgets）采用的清理方式类似JVM中的复制算法+可达性分析算法，新建对象的时候用bumping allocator快速分配，当某一半的内存用到一定程度时，就把仍然存活的对象复制到另一半，同时清空当前这半空间；接下来继续在另一半中执行bump + 可达性分析 + 复制，如此反复。对象存活到一定生命周期后会被移到老年区，然后用mark-sweep清理。[据说](https://medium.com/flutter/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30)Flutter的对象基本都是满足weak generational hypothesis的，所以不太可能用到mark-sweep？对于我这种经常微操UI，没事就Hot Reload一下UI的人，对象大概的确满足weak generational hypothesis吧

- 有一些用着非常舒服的语法糖比如 `?.`和`??`。关键词 `asnyc`、 `await`和 `Future`极大简化了各种异步操作

- 最重要的，用Flutter写UI布局很容易上手，适合对前端一无所知的人（比如我）。Flutter中一切组件都是以widget的形式存在，比如 `padding`, `center`, `column`什么的，想要自定义加几个参数就够了，同时Flutter打包了iOS和Material各种现成的widget，可以无脑调用，就算直男审美不会美工，撸个简单不丑的界面也不是什么特别大的问题。刷新widget也很无脑，用 `setState`更新数据，Flutter会根据当前改变的widget的`build`方法，重新渲染整个widget子树

当然Flutter也有很多令人抓狂的问题。首先Flutter的轮子非常少，一些稍微冷门一点的需求往往需要自己下场改原生组件，比如 `DropdownButtonFormField`的长度没法调节，当菜单里的元素较多时，会占满几乎整块屏幕空间，想要要限定高度只能自己手写组件，这个[issue](https://github.com/flutter/flutter/issues/23865)在GitHub上已经快三年了，至今解决无望。另外还有一些非常奇葩的bug，比如在带有GMS的模拟器跑APP，terminal经常会蹦出 `java.lang.IllegalArgumentException: Service not registered: lu@bc4a540`一类的exception错误，具体是什么原理的我并不是特别清楚，但既然似乎不影响程序运行，那也就随它去了。考虑到Flutter在GitHub上有八千多个没有解决掉的issue，这些都是小事情。


### Firebase作为BaaS

既然用了Dart/Flutter框架，Firebase自然成了后端的首选。一方面，Firebase作为Google的亲儿子，和Dart/Flutter无缝对接，另一方面，Firebase作为BaaS平台，打包解决了用户验证、数据库储存、Google Analytics等一系列后端功能。有了Firebase就不用花时间自己去配置各种SDK、搭建服务器或者折腾各种SQL。

比如实现第三方谷歌用户登录，直接几行代码搞定：
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

认证过的用户有一串`uid`，可以用这串`uid`在`firestore`下给用户建document管理数据。Firebase数据库比较优秀的一点是开发者可以在客户端监听数据库的变化，实时获取数据库的snapshot；同时，只要在客户端修改用户数据，Firebase后台的可视化界面也会立刻同步刷新，比较傻瓜，非常适合我这种不懂数据库的人调试后端。具体Firebase的一些奇技淫巧可以看[这里](https://firebase.google.com/docs/flutter/setup)。

## 未来可以改进的一些地方

最后的成品大概长[这样](https://play.google.com/store/apps/details?id=com.amherst.mammoth_meals)，虽然UI还是有点丑，但以我的审美，大概也指望不出什么更惊艳的设计了，所以就先硬着头皮投入使用，等学期中如果有空，再根据用户反馈慢慢打磨。

受时间所限，很多东西目前的实现都比较粗糙。比如APP的前端数据是fake实时刷新的，现版本的解决方案比较粗暴，直接开了两个timer，一个负责每五分钟从后台API抓取当日还剩余餐位的时间点，另外一个负责每分钟在前台刷新UI，然后根据本机的时间，并结合本机时间筛掉已经不符合条件的饭点（比如已经过期、人数已满）。后来发现如果后台杀不掉的话，这俩timer就会不停运行，向后端发送API请求。

为了解决这个问题，我打了个 `WidgetsBindingObserver`的补丁，让应用监视自己当前是否在前台，如果在前台则启动timer，如果在后台，那暂停timer。对于刷新频率不算太高的应用，这种方案基本可以在前端实时性和后端压力之间找到一个还算体面的平衡。但如果是那种需要快速、持续实时更新数据的场景，或许[Stream Builder](https://api.flutter.dev/flutter/widgets/StreamBuilder-class.html)是个更好的选择。

## 一些碎念

一个月折腾下来，移动开发个事情怎么说呢，繁琐且无趣，整个项目的大量时间，其实都耗在打断点抓虫上，、翻文档解决各种离奇问题、测试和集成队友代码，或者和学校的各部门扯皮上，真正写UI写业务逻辑的时间反而没有想象中那么多。对于这种架构比较简单的APP，也没有什么特别难实现的地方，在绝大部分现有的开发框架下，难写的组件都被打包好了。剩下的工作就是调个包，随便找个对框架语言有一定了解且善于使用搜索引擎的程序员，给台能上GitHub和Stack Overflow的电脑，再给够时间，东拼西凑一下，多半也能把东西堆出来。

软件开发有的人说它是门科学，有的人说它是门艺术，还有人说它是门工程学。在我看来，软件开发肯定算不上科学，因为搞开发肯定不在探求宇宙真理，似乎也不在弄理论性研究拓宽人类知识的边界。软件开发也不太像是门艺术，至少我没觉得我在我写的代码里面抒发了任何情感，也没有在打了补丁的方法中捕捉到什么和谐的美感。软件开发也许是工程学，涉及到计算机底层原理的函数、类库和各种架构的编写，或许可以被称为工程，但绝大多数套用层层的框架，来回拖拽类库的软件开发，顶多是土法炼钢罢了。

那么如果软件开发不是科学，不是艺术，在绝大多数场景下又谈不上是完整的工程，那么它是什么呢？我认为它更是门手艺活，好比华强北画PCB板子的老板，他不是科学家，不是艺术家，也未必是严格意义上的工程师，他不需要研究固态物理，不需要在PCB板子里表达自我，更不一定需要用高等数学去优化电路，但是他能熟练运用各种现有工具，把板子给整出来。
