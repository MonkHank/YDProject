# 组件化封装思想实战 
## fragment的几种切换模式
    - add+show/hide，replace，attach/detach
    - attach/detach很少用到，不会销毁fragment，而是会销毁fragment的view

##Charles，网络请求组件
- 异步图片加载， Universal-Image-Loader
    - ImageLoaderConfiguration ：为ImageLoader配置参数
    - DisplayImageOptions：为我们显示图片的时候进行配置
    - ImageLoader：使用displayImage去加载图片
    - 就算高级工程师，也达不到设计第三方框架能力，能读懂就已经很NB了
    - 平常心吧，多了解些东西，基础东西很重要
 

## HomeFragment一些思路   
- ListView中如何使用一个二维的数据结构？
- ViewPager如何实现无限循环滑动？
```xml
// 设置 ImageView 背景为下列文件，android:oneshot = false，开启无限循环 
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="true">
    <item android:drawable="@drawable/rocket_thrust1" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust2" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust3" android:duration="200" />
</animation-list>

// 开启帧动画
AnimationDrawable ad = (AnimationDrawable) rocketImage.getBackground();
ad.start();
```
- ListView
    - int getItemViewType()
    - int getItemCount(int position)

- zxing扫码拷贝
    - res资源拷贝
    - src拷贝 (CaptureActivity)
    
- Intent打开可提供选项相册
```java
// Intent createChooser(Intent target, CharSequence title);方法
 Intent innerIntent = new Intent(Intent.ACTION_GET_CONTENT);
 innerIntent.setType("image/*");
 Intent wrapperIntent = Intent.createChooser(innerIntent, "选择二维码图片");
 startActivityForResult(wrapperIntent, REQUEST_CODE);
```

## 视屏播放器
- 一些实现方式
    - VideoView
    - MediaPlayer + SurfaceView
    - MediaPlayer + TextureView（采取方案）
    - 视频播放器适配应该算得上是最复杂的
![](/png/视频播放器的几种方式.png)
    
- 自定义一个适配播放器
    - 播放器生命周期
    - 那些坑
    
![](/png/播放器常见的坑.png)
   
   
- 核心类
    - MediaPlayer视频播放核心类 
    - TextureView显示帧数据核心类
    
- 回调函数处理
```java
// view
void onVisibilityChanged(View changedView,int visibility)
boolean onTouchEvent(MontionEvent ev)
void onClick(View v)
// MediaPlayer
void onCompletion(MediaPlayer mp)
boolean onError(MediaPlayer mp,int what,int extra)
boolena onInfo(MediaPlayer mp,int what,int extra)
void onPrepared(MediaPlayer mp)
void onBufferingUpdate(MediaPlayer mp,int percent)
// TextureView
void onSurfaceTextureAvailable(SurfaceTexture surface,int width, int height)
void onSurfaceTextureSizeChanged(SurfaceTexture surface,int width, int height)
boolean onSurfaceTextureDestroyed(SurfaceTexture surface)
void onSurfaceTextureUpdated(SurfaceTexture surface)
```

- 播放器View实现流
![](/png/播放器实现流.png)

- 播放器View业务流
![](/png/播放器业务流.png)

- 思路点拨
    - 计算播放器在屏幕中出现的百分比
    - 小屏播放到全屏播放能复用一个播放器，这个功能试过很多次了，即使高级开发工程师也不一定一眼看出来某些功能一眼就能实现与否
    - 监听播放器产生的各种事件


互相创建彼此对象，进行彼此之间的方法调用，耦合度百分比，不可取，采用接口回调机制。

```java
// 部分手机可以用下面这种方式暂停视频播放，但有些不确定机型这种方式暂停不了，
// 所以还是使用setOnSeekCompleteListener回调方式，这个方式适配了全部机型。
mediaPlayer.pause();
```
- 小屏到全屏功能开发
    - 启动新Activity，需要加载两次，资源消耗较大
    - 创建一个全屏显示Dialog显示，资源消耗比较小
    
Dialog生命周期相关函数，onCreate、onWindowFocusChanged、dismiss

接口回调三步曲，接口回调的核心是先从接口层使用接口引用调用方法，其次是以对象的形式实现该方法做自己具体的逻辑处理

- CustomVideoView
     - <font face="黑体">TextureView + MediaPlayer</font>
     - <font face="黑体">ADVideoPlayerListener</font>
     - 加载的view显示隐藏处理，帧动画处理
     ```java
     // 获取到帧动画，对mLoadingBar控件background的 animation-list 进行播放，
     // 可以设置 animaition-list oneShot="false" 进行循环
     AnimationDrawable anim = (AnimationDrawable) mLoadingBar.getBackground();
    anim.start();
     ```
     - 加载失败后的再加载次数(mCurrentCount)
     - <font face="黑体">mp.preparedAsync()</font>成功后接口回调<font face="黑体">onAdVideoLoadSuccess()</font>
     - 自动播放条件，当前View在屏幕中占比大于一半；获取view的屏幕占比
     ```java
      // 获取到当前view在屏幕中出现的一个矩形
      Rect rect = new Rect();
      pView.getGlobalVisibleRect(rect);
     ```
     - <font face="黑体">mp.start()</font>后开启播放
     - 正常播放结束后<font face="黑体">onCompletion(MP)</font>的回调<font face="黑体">onAdVideoLoadComplete()</font>
     - 播放异常 <font face="黑体">onError(MP..)</font> 的回调 <font face="黑体">onAdVideoLoadFailed()</font>
    - <font face="黑体">mp.reset()、mp.pause()、mp.stop()、mp.release()</font> 方法运用
    -  <font color="de97">Activity转后台后的处理</font> <font face="黑体">onVisibilityChanged(View，int)</font>
    - <font face="黑体"> onTouchEvent()自己处理</font>
    - 静音处理
    - <font face="黑体">不自动播放，点击播放按钮播放，回调 onClickPlay()</font>
    - 全屏播放策略、小屏播放策略、跳转Activity后续播策略
