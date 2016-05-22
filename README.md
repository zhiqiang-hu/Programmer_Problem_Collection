# 各大Android开发群问题总结及解决

标签（空格分隔）： Android

---

- 1、 android比较好用的websocket三方库
    - websocket老是会自动断
    - socket.io
        - 采用轮询的方式，会定时发心跳包检测连接状态，基于事件通信。
        - 服务端nodejs，客服端java,ios都有支持库
        - android端可以基于这个库封装你自己的推送和即时聊天框架
        - 包含有websocket的，底层基于okhttp-ws，支持http,https,websocket
    - 好像要用nio的socket，不然会有丢包现象

---
- 2 、retrofit如何设置cookiemanager
    - CookieManager 应该交给下面的 okhttp 做
    - 知道在OkhttpClient中设置  可是在okhttp3中  OkhttpClient没有设置cookieManager的方法
    - 那你搜搜 okhttp3 CookieManager 啊

---
- 3、网易云音乐（Android）的超链接如何添加，那个歌曲名，点进去
    - 使用spannable 

---
- 4、当后台服务器的数据发生增量变化时，我在本地的数据如何更新呢？
    - 插入

---
- 5、加固服务
    - 有兄弟用过爱加密的加固服务吗 ？
    - 百度和360的太特么不靠谱了，用了这个加固的一启动就挂了，做的兼容性不够、在考虑换个爱加密，兄弟们给点意见呗，我之前就是用360加固的
    - 加固完之后要再签一次名。。。它是要再签一次，不然启动就会挂了，加固完之后再签一次
    - 对呀。 现在用360也是这样，只不过用它的软件给你重新加了。不过爱加密的定制版貌似不用那么麻烦了。我也没体验到他们的收费版 只是听说是那样的

---
- 6、下拉列表
    - spinner

---
- 7、android:一个Open键引发的问题！！
>引用自[android:一个Open键引发的问题！！][1]
>引用自[Android安装应用后点击"打开"(Open)带来的问题及解决方式][2]

    - 我直接在Android studio 把app安装到手机这时候我按home 键,再次打开 app 没有任何问题。这时候我打包,把app打包成apk ,然后这时候按 home 键,这时候再打开app,,发现app 重新启动了.
    - 在Android目前的系统下,点击Open键和点击图标启动时，Intent的参数传递的不一样，activity的启动方式不同，导致上述问题的产生。但是，由于启动参数由系统设定，我们不好改变。那我们的思路就有两个：
1.第二次启动的时候，把第一次启动的杀掉
2.第二次启动的时候，将第一次启动的Activity唤醒，第二次启动关闭。
一般我们选择第二种，因此，我们就有了如下思路：
1.在启动第一个Activity的时候，判断启动方式是不是从点击OPEN键启动的.
2.在需要唤醒的activity中注册BroadcastReceiver，接收到广播之后，调用自己的onResume方法。
    - 在你的 launch activity 的 onCreate 里加这一段代码:

            @Override  
            protected void onCreate(Bundle savedInstanceState) {  
                super.onCreate(savedInstanceState);  
                if ((getIntent().getFlags() & Intent.FLAG_ACTIVITY_BROUGHT_TO_FRONT) != 0) {  
                    // 在这里发送广播，唤醒之前启动的Activity  
                    finish();  
                    return;  
                }  
              
                // Regular activity creation code...  
            }  
         
    - 更早之前的版本开始就有这个问题了，应该是使用 apk 安装完成之后用于启动 app 的 intent 携带的 flag 信息出现了奇奇怪怪的问题，另外你装完之后，打开再强杀一下，之后启动也不会有这个问题了       
    
---
- 8、关于安卓沉浸式渲染的问题,设置系统栏和app颜色一致的

>Demo见GitHub---[SystemBarTint][3]

    - 那个systembar tintmanager设置系统栏和app颜色一致的,我那个布局文件每个都要进行设置,这样很麻烦。。
    - 解决见Demo，写个基类就好了，封装一下，整个app都被沉浸炫染

- 9、app如何与网页进行交互呢，比如传输数据
    - Jsbridge  

---
- 10、使用retrofit之前,参数本身需要一系列的转换,用Rx要怎么改造.
    - 例如getData(input2String(inputStrem)),这个input2String(inputStrem)这个方法耗时长,怎么把它转到Rx中
    - 转换方法如下：

            InputStream ins;
            Observable.just(ins).map(new Func1<InputStream, String>() {
                @Override
                public String call(InputStream inputStream) {
                    return input2String(inputStrem);
                }
            }).flatMap(new Func1<String, Observable<?>>() {
                @Override
                public Observable<?> call(String s) {
                    return getData(s);
                }
            })

---
- 11、app初次安装时，会在cache目录里生成大量dex文件，在Android 6.0的机型上会复现

>解决方法[.dex file in android][4]
    
---
- 12、把这两个menu item显示在toolbar 

>参考 [How to add buttons like refresh and search in ToolBar in Android?][5]

---
- 13、将一个bitmap如何实现马赛克效果

>参考[Android群英传笔记——第六章：Android绘图机制与处理技巧][6]

---
- 14、右下角的输入法,搜索二字怎么监听?
    - 给EditText设置一个属性就行了
    
            editor.setImeOptions(EditorInfo.IME_ACTION_SEARCH);
            editor.setOnEditorActionListener(new TextView.OnEditorActionListener() {
                @Override
                public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
                    if (EditorInfo.IME_ACTION_SEARCH == actionId) {
                        // todo
                        return true;
                    }
                    return false;
                }
            });

---
- 15、怎么设置navigationitem 点击效果
    - itemBackground，试试这个，如果第三方的NavagationBar实现就另说
    - 这个字体颜色和图片的选中和未选中的效果咋搞啊？
        -  用那个属性要看你选择器是用什么控件，颜色你就写一个颜色的选择器，图片也是相同的道理，color引用一个选择器就可以了，字体的选择器是在color那里写的
        -  itemTextColor，itemTextAppearance，设置字体颜色和字体样式

---
- 16、parameter must be a descendant of this view  大家见过这个问题吗
    - 我发现是 listview 里 嵌套了 headerview ，item 里 有个button 点击弹出dialog 偶尔就报这个错误 。
    - 无解,这个在友盟后台能看到日志.至今不知道发生的原因

---
- 17、关于onmeasure  onlayout  onsizechanged这几个方法谁比较熟啊，能讲讲它在自定义view时有什么作用，并且怎么才能很好的去重写这几个方法呢
>参考[ProgrammingNotes][7] 

---
- 18、有个 framelayout 里有个 view ，先渲染 framelayout 还是 先渲染 view 呢
    - frameLayout 吧！毕竟，这个view是依附于frameLayout存在的。。。 

---
- 19、推荐的那个用来测试接口的工具叫啥来着？？？
>[ChromeRestClient][8]

---
- 20、AsyncTask的问题
>参考[译文：Android中糟糕的AsyncTask][9]

    - 容易内存泄露，cancel()方法不管用，更别说asyncTask写在activity里边带来的代码格式问题了

---
- 21、使用过listView嵌套GridView ，怎么获取GridView里的内容？
>参考[Android 高仿微信发朋友圈浏览图片效果][10]

---
- 22、Recyclerview嵌套recyclerview不更好？
>参考[RecyclerView--实现 ListView，GridView，瀑布流 效果][11]

---
- 23、话说……并发和并行……区别是啥
    - 并发就是一心二用(多用)，比如你一边听老师讲课，一边低头看课桌下韩寒的小说。这两件事你在同时做，而且这两件事并不一定需要相关。而并行就是兵分几路干同一个事情。比如别人看小说只能一行一行的看，而你能一目十行，这就是并行。

---
- 24、有木有好的屏幕适配方案推荐？
>参考[Android开发：最全面、最易懂的Android屏幕适配解决方案][12]

---
- 25、下标线怎么添加
>参考[android TextView设置删除线，超链接，颜色和字体等说介绍][13]
   
--- 
- 26、请教个问题，假设图片每次加载的流程都是先到缓存里面去找，找不到再去服务器上取，这时候服务器的图片更新了，【可能名字不变，只是换了图片】，这种情况下要怎么取到最新的那张图片？
    - 那你缓存可以设置一个有效时间

---
- 27、强烈建议发一篇XMPP篇，即时通讯

---
- 28、IOS那种渐变阴影的折线图怎么做
    -  https://github.com/ArthurGuibert/FSLineChart
这样满足需求么？    
        - 好像不满足
    - https://github.com/PhilJay/MPAndroidChart 这个可以
    - 加了个shader就有效果了
```
mShaderPaint.setShader(new LinearGradient(0, 0, 0, getHeight(), 0xff000000, 0x00000000, Shader.TileMode.CLAMP));
```

- 29、像微信那样滑动关闭Activity
    - http://gold.xitu.io/entry/5715f2111ea4930059d34d71

        
  [1]: http://blog.csdn.net/nupt123456789/article/details/34415849
  [2]: http://blog.csdn.net/lfdfhl/article/details/38070991
  [3]: https://github.com/Ztiany/SystemBarTint
  [4]: http://stackoverflow.com/questions/7750448/dex-file-in-android
  [5]: http://stackoverflow.com/questions/26778701/how-to-add-buttons-like-refresh-and-search-in-toolbar-in-android
  [6]: http://blog.csdn.net/qq_26787115/article/details/50967896
  [7]: https://github.com/Ztiany/ProgrammingNotes
  [8]: https://github.com/jarrodek/ChromeRestClient
  [9]: http://droidyue.com/blog/2014/11/08/bad-smell-of-asynctask-in-android/index.html
  [10]: http://blog.csdn.net/lyhhj/article/details/50002005
  [11]: http://blog.csdn.net/mr_wanggang/article/details/42343501
  [12]: http://www.jianshu.com/p/ec5a1a30694b?utm_campaign=haruki&amp;utm_content=note&amp;utm_medium=reader_share&amp;utm_source=weixin&amp;from=groupmessage&amp;isappinstalled=0
  [13]: https://yq.aliyun.com/articles/10124
