### webchat-app-issues

早就知道小程序这个坑很深，一个多月的实践发现这个坑果然名不虚传...      

不过，开发的小程序如期上线了，还是值得庆贺！！！呵呵，记录一下那些让自己停顿的点。

#### 一、开发微信小程序遇到的坑

- 1、 {{}} 不能执行方法，只能处理简单的运算如 “+ - * /”，比如遇到遍历list，每个item的金额需要格式化，只能在js里预先格式化好再setData一遍( ╯□╰ )
- 2、只能通过,只能通过,只能通过setData()更新视图，坑..........
- 3、我们规定页面路径只能是五层，请尽量避免多层级的交互方式。
- 4、A页面-->B页面，B页面返回A，如何触发A的刷新，不增加页面层级。
     暂时想到的方案：在A页面的onshow事件里写A的初始化数据逻辑。
- 5、<del>undefined 当作字符串处理  if(xxx == "undefined") 【后期版本迭代优化了】</del>
- 6、遇到奇怪的问题（如样式等）先重启工具，百试不爽
- 7、不支持本地web字体(需要使用线上字体),经测试发现一些安卓就是不能正常显示。几经折腾才发现原来还要存放字体的服务器要支持跨域。尼玛，手机上调试也不报错，差点放弃web字体了。卡了好久，坑.............
- 8、本地资源无法通过 css 获取 可以使用网络图片，或者 base64，或者使用 `<image/>` 标签
- 9、不能直接操作 Page.data 【避免在直接对 Page.data 进行赋值修改，请使用 Page.setData 进行操作才能将数据同步到页面中进行渲染】
- 10、数字键盘用 type="digit"
- 11、已用 https，但报无法建立与该服务器的安全连接是什么情况【需要 nginx 配置一下 https 的加密标准为tls1.2及以上】
- 12、wx.setStorage(OBJECT) 【目前每个小程序限制5M】
- 13、数据监控【微信后台：事实访问次数/昨日概况/累计访问用户数/TOP受访页】
- 14、微信调试神器，ngrok，见 <a href="https://www.twindy.org/nginx-ngrock-net-through/" target="_blank">ngrok</a>
- 15、view 添加点击效果[需要开启hover效果]：  

```html
    <view hover hover-class="item-hover">
```
- 16、用户授权调试方法【开发工具-》清除手机授权数据 (缓存-清除手机授权数据) 】
- 17、安卓手机上小程序第一次加载时候首页时候， onshow方法会莫名其妙加载两遍，而同样在iphone下却不会，由于需要在onshow里面触发获取用户信息，所以系统加载两次onshow会导致后台报错。。。
- 18、禁止页面下拉： 设置 "disableScroll": true 。
- 19、安卓下会出现getAppConfig:fail，无法获取userInfo。微信问题，下载最新的微信安装包
- 20、后台不能接受POST数据，但是可以接收GET请求参数

```json
    url: url,
    data: data,
    method: "POST",
    dataType: "json",
    header: {
      'content-type': 'application/x-www-form-urlencoded' //==> 此处若为application/json则服务端无法获取POST的参数
    }
```

- 21、小程序第一次启动用户拒绝授权后，下一次无法唤起授权弹框，默认记住上一次用户的选择。暂时没有找到解决方法，微信也没有相关解析。【2017-01-10】
    微信更新了api（wx.authorize(OBJECT)）,支持唤起授权弹框【2017-07-05】
- 22、跳转到有tabbar的页面一定要用 wx.switchTab().这个在各个群里几乎每天都有人问到！！！
- 22、绑定开发者/体验者时记得去手机上确认邀请。（超多人问）

![error](https://cloud.githubusercontent.com/assets/6022948/21841292/940f4d50-d81c-11e6-96ac-76fdb4cc274a.png)

- 23、 微信后台更新了request合法域名，可以直接去微信开发工具-->项目-->配置信息-->刷新，立马生效。
- 24、 小程序官方没有提供java版登录信息解密示例，可以用这个demo <a href="https://github.com/cocoli/springboot-weapp-demo" target="_blank">java版解密demo</a>
- 25、 使用wx:for遍历的时候最好加上wx:key="{{item.id}}"，如果明确知道该列表是静态，或者不必关注其顺序，可以选择忽略，但是如果遍历的数组会发生改变，则有可能导致数据顺序会改变。

![wx-key](https://cloud.githubusercontent.com/assets/6022948/21972416/59402f42-dbf4-11e6-85c0-e369fc1e0bfd.png)

- 26、小程序开发页面便利之处在于rpx, 所以设计稿建议用iphone6尺寸。flex布局可以参考<a href="http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html" target="_blank">这篇</a>
- 27、微信小程序的会默认监听文件变化，然后自动刷新。但不足的是每次都是全量刷新，而不是模块的热替换，反而会影响开发速度，尤其对于喜欢频繁Command + S的开发者，你会发现你的小程序在不断的刷新。[@MinJieLiu](https://github.com/MinJieLiu)
- 28、微信小程序自 基础库 `1.6.4 `开始支持了`web-view`组件，即在小程序支持了嵌入网页的能力，但是使用的时候发现在开发者工具中不能显示网页，手机预览却可以【升级微信客户端最新版本】。原因是所使用的基础库不支持`web-view`组件，可通过选择最新的基础库解决。如下：

![image](https://user-images.githubusercontent.com/6022948/34928298-8a259daa-f9f7-11e7-8c85-f2d661937f6c.png)

- 29、 不支持H5和小程序通信，不能通过本地存储之类的手段打通， 如果硬是需要携带参数，可以尝试在`web-view` url 中携带，但是不推荐，H5可以使用公众号授权。web-view开放的能力（网页需嵌入jssdk）： [<web-view/>网页中支持的JSSDK接口](https://mp.weixin.qq.com/debug/wxadoc/dev/component/web-view.html)：
- 30、web-view 目前不支持微信支付

#### 二、小程序开发前相关配置

- 1、登录--->管理员扫二维码-->设置-->开发设置-->生成AppSecrect
- 2、服务器配置
   登录--->管理员扫二维码-->设置-->开发设置-->服务器域名-->扫二维码
- 3、绑定开发者
   用户身份-->开发者-->二维码  开发者最多10人
- 4、绑定体验者
   用户身份-->体验者-->二维码扫描 体验者20人


#### 三、小程序发布

发布只能用管理员账号。步骤如下：

- 开发工具中退出开发者账号
- 管理员扫码登录开发者工具，上传-->微信后台设为体验版本-->审核-->发布

注意： 审核时间不定，2-6天

#### 四、审核不通过原因收集
- 1、小程序简介没有介绍小程序功能
- 2、类目与页面提供的内容不一致
- 3、小程序提供的服务和内容必须是正式的，不能以测试内容提交，多次以测试内容提交
- 4、含有声音视频内容，请补充相关对应类目
- 5、首页图片与文字有互相重叠，建议优化
- 6、搜索框及少数下才能选中，页面评论点击无响应，页面图片分辨率尺寸失真
- 7、部分图片显示被压缩体检不好
- 8、有账号体系的小程序，除自有登录方式，必须支持微信授权登录
- 9、必须登录才能使用的服务，请提供测试账号
- 10、存在虚拟物品在线交易， ios系统需要走IAP,小程序暂不支付，请留意后续
- 11、小程序服务类目所对应的页面中的核心内容必须与该类目一致。
- 12、必须保证用户在该页面能使用该服务类目，不得隐藏，不得进行多次跳转
- 13、不得展示和推荐第三方小程序。示例：不能做小程序导航，不能做小程序链接互推，小程序排行榜等
- 14、小程序的页面内容中，存在诱导类行为，包括但不限于诱导分享、诱导添加、诱导关注公众号、诱导下载等，要求用户分享、添加、关注或下载后才可操作的程序，含有明示或暗示用户分享的文案、图片、按钮、浮层、弹窗等的小程序，通过利益诱惑诱导用户分享、传播的小程序，用夸张言语来胁迫、引诱用户分享的小程序，强制或诱导用户添加小程序的，都将会被拒绝；
- 15、 禁止视频、音乐、语音等多媒体的自动播放
- 16、 如果小程序有账户系统，必须提供能正常使用且易于发现的“退出”账户选项
- 17、小程序内容不能包含赌博、竞猜和抽奖的。
- 18、小程序的页面内容中，不能存在测试类内容；示例：算命，抽签，星座运势等。

[其他微信小程序平台常见拒绝情形](https://developers.weixin.qq.com/blogdetail?action=get_post_info&lang=zh_CN&token=1592986236&docid=c53fb90c11590a1b86c109b4006fae27)

#### 五、文档及社区
- 1、[官方文档](https://mp.weixin.qq.com/debug/wxadoc/dev/index.html?t=201715)
- 2、[官方Q&A](https://mp.weixin.qq.com/debug/wxadoc/dev/qa.html?t=201715)
- 3、[官方社区](https://developers.weixin.qq.com)
- 4、[小程序接入指南](https://developers.weixin.qq.com/blogdetail?action=get_post_info&lang=zh_CN&token=1592986236&docid=bb39a3dfd5f9c7070f9e2ec3c0f7f68a)
- 5、[小程序常见FAQ](https://developers.weixin.qq.com/blogdetail?action=get_post_info&lang=zh_CN&token=1592986236&docid=2fcdb7794d48c59f7624f53e94d0ae22)

#### 六、code

- 1、封装微信发起请求接口,登录失效默认自动发起登录请求

```js
/**
 * 发起的是 HTTPS 请求
 * @pram url: 请求地址,协议必须为https
 * @pram data 请求参数请求参数
 * @param success 请求成功回调
 * @param fail 请求失败回调
 * @param complete 请求完成（成功或者失败）回调
 */
function request(url, data, success, fail, complete) {
  var _url = url,
      _data = data,
      _success = success,
      _fail = fail,
      _complete = complete;

  wx.request({
    url: url,
    data: data,
    method: "POST",
    dataType: "json",
    header: {
      'content-type': 'application/x-www-form-urlencoded',
      'Client-Agent': getSystemInfo(),
      'WX-SESSION-ID': wx.getStorageSync(constant['WX-SESSION-ID']) //每次请求带上登录标志
    },
    success: function(res) {
      if(res.data.code == "-9999") { //会话失效重新登录
        requestLogin(function(){
          constant['NUM_TRY_LOGIN'] ++;
          //设置请求上限，防止重复提交并死循环
          if(constant['NUM_TRY_LOGIN'] < constant['LIMIT_NUM_TRY_LOGIN']) {
            request(_url, _data, _success, _fail, _complete);
          }
        });
        return;
      }
      if(res.data.code == "0") {
         if(typeof _success == "function") {
           _success(res.data);
         }
      } else {
         wx.showToast({title: res.data.msg, icon: 'loading', duration: 2000});
         return;
      }
    },
    fail: function(res) {
      if(typeof _fail == "function") {
        _fail(res);
      }
      if(typeof _fail == "string") { //请求失败的弹框提示
        wx.showToast({title: _fail, icon: 'loading', duration: 2000});
      }
    },
    complete: function(res) {
      if(typeof _complete == "function") {
        _complete(res);
      }
    }
  });
}
```
- 2、请求登录接口

```js
/**
 * 请求登录,获取用户相关信息
 * @param callback
 */
function requestLogin(callback) {
  var _callback = callback;
  wx.login({
      success: function (event) {
        // 获取到请求码，继续请求用户的基本信息
        if(event.code) {
          var code = event.code;
          wx.getUserInfo({
              success: function (res) {
                var data = {
                  code: code,
                  encryptedData: res.encryptedData,
                  iv: res.iv,
                  signature: res.signature,
                  rawData: res.rawData
                }
                var url = domain + "/wx_xxx"; //请求登录地址
                request(url, data,
                  function(res){ //success
                    if(res.code == "0") {
                       //此处可以将服务端返回的登录状态保存起来
                       wx.setStorageSync(constant['WX-SESSION-ID'], res.object.sessionId);
                       if(typeof _callback == "function") {
                         _callback();
                       }
                    }
                  },
                  function(res){ //fail
                    wx.showToast({title: '请求登录失败',icon: 'loading', duration: 2000});
                  }
                );
              },
              fail: function(res) {
                //用户拒绝授权
                if(res.errMsg == "getUserInfo:cancel" || res.errMsg == "getUserInfo:fail auth deny") {
                  wx.redirectTo({ //跳转至未授权页面
                    url: '../xxx-page/xxx-page'
                  });
                }
              }
          })
        } else {
          wx.showToast({title: '微信登录失败',icon: 'loading', duration: 2000});
        }
      },
      fail: function(res) {
        wx.showToast({title: '微信登陆失败！',icon: 'loading', duration: 2000});
      }
  });
}
```

![七牛云](http://assets.qiniu.com/v2/qiniu-409x220.png)
