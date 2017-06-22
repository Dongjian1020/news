# 信息流SDK
##  简介
卓易信息流SDK是卓易科技针对开发者，快速展示信息流开发的一套基于Android的程序接口，开发者可以通过调用SDK的接口，轻松的访问聚合新闻资讯相关的数据服务，SDK除了聚合新闻数据之外还融入了商业广告，满足商业化需求。

* [功能说明](#FunctionDec):描述SDK的功能
* [广告接入](#Ad):描述广告接入
* [集成方法](#Integration):描述SDK的集成步骤
* [接口使用](#Interface):描述接口的使用方法以及参数

## <a id="FunctionDec"></a>功能说明
卓易信息流可以拉取到卓易新闻资讯相关的内容，内容包含新闻，娱乐，财经，科技，体育，汽车，时尚等。SDK可根据拉取到的数据维度，生成新闻频道列表页和详情页。

### 列表页
* 根据不同第三方内容范围，可读取对应范围内容数据进行列表呈现
* 采用信息流行业经典布局，左文右图模式
* 列表中单挑内容显示标题，缩略图，来源，发布时间，标注等
* 支持点击或滑动切换浏览多个频道内容列表
* 支持对频道进行排序等管理操作
* 支持上下滑动可刷新或加载更多内容

  
### 详情页
* 点击任一内容后进入对应内容详情页浏览
* 详情页中将会根据本文内容推荐其他相关内容，点击进行浏览
* 支持点击返回跳转至上一页


## <a id="Ad"></a>广告接入
* SDK支持列页的广告数据拉取与展示。(目前只支持Adroi广告)
* 列表页广告样式目前包括：native大图样式，native小图样式。

## <a id="Integration"></a>集成方法
a.在libs中添加news-1.0.001.aar</br>
b.在Gradle中添加如下依赖库</br>

``` groovy
repositories {
    mavenCentral()
    maven {
        url "https://github.com/DroiBaaS/DroiBaaS-SDK-Android/raw/master/"
    }

    flatDir {
        dirs "libs"
    }
}

dependencies {
    XXXXXXXXX........//工程原有依赖库
    
    compile(name: "news-1.0.001", ext: "aar")
    compile 'com.droi.sdk:Core:+'
    compile 'com.squareup.picasso:picasso:2.5.1'
    compile 'com.squareup.okhttp3:okhttp:3.0.1'
}
```
## <a id="Interface"></a>接口使用
a.继承AppApplication在Application的onCreate方法中初始化SDK：
``` java 
//初始化一（有广告）
HashMap<Integer,String> ids = new HashMap<>();
ids.put(0,id1);
ids.put(1,id2);
DroiNews.initialize(context, MEDIA_ID, isDebugMode,ids,isNoAd);//isNoAd设置为false
DroiNews.registerAdSlot(DroiNews.AD_STYLE_SMALL_IMAGE, SLOT_ID_SMALL_IMAGE);
DroiNews.registerAdSlot(DroiNews.AD_STYLE_BIG_IMAGE, SLOT_ID_BIG_IMAGE);

//初始化二（无广告）
DroiNews.initialize(context, null, isDebugMode,null,true);
```
</br>
** 注：**MEDIA_ID,SLOT_ID_BIG,SLOT_ID_SMALL，ids 需要在控制台提交工单申请,isDebugMode为是否调试模式，isNoAd为列表页是否显示广告， 请参考DEMO里对应的值。

b.加载新闻列表页：</br>
目前News SDK提供封装好的新闻列表页面: 
com.droi.sdk.news.DroiNewsFragment
主程序可以把该fragment集成到相关的activity中。
``` java
FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();

DroiNewsFragment f = new DroiNewsFragment();

transaction.add(R.id.myfragment, f, "flag");

transaction.commit();
```
c.设置主题颜色：</br>
在res->values目录下的colors.xml文件中增加“customer_color”设置颜色，如下：</br>
**<color name="customer_color">#303F9F</color>**
