## <span id=3>组件化封装思想实战 <font size=1>[回到目录](#0)
- fragment的几种切换模式
    - add+show/hide，replace，attach/detach
    - attach/detach很少用到，不会销毁fragment，而是会销毁fragment的view

- Charles，网络请求组件
- 异步图片加载， Universal-Image-Loader
    - ImageLoaderConfiguration ：为ImageLoader配置参数
    - DisplayImageOptions：为我们显示图片的时候进行配置
    - ImageLoader：使用displayImage去加载图片
    - 就算高级工程师，也达不到设计第三方框架能力
    - 平常心吧，多了解些东西，基础东西很重要
    
- ListView中如何使用一个二维的数据结构？
- ViewPager如何实现无限循环滑动？
```xml
<!-- 设置 ImageView 背景为下列文件，android:oneshot = false，开启无限循环 --> 
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="true">
    <item android:drawable="@drawable/rocket_thrust1" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust2" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust3" android:duration="200" />
</animation-list>

<!-- 开启帧动画 --> 
AnimationDrawable ad = (AnimationDrawable) rocketImage.getBackground();
ad.start();
```
- ListView
    - int getItemViewType()
    - int getItemCount(int position)