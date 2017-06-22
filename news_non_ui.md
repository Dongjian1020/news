# 信息流（无UI版）
## 简介
卓易信息流SDK是卓易科技针对开发者，快速展示信息流开发的一套基于Android的程序接口，开发者可以通过调用SDK的接口，轻松的访问聚合新闻资讯相关的数据服务，SDK除了聚合新闻数据之外还融入了商业广告，满足商业化需求。

以下就数据服务开发相关技术区分成不同小节来描述如何开发：
* [初始化](#Init):描述初始化
* [获取置顶新闻数据](#TopNews):描述如何获取置顶新闻
* [获取新闻列表数据](#NewsList):描述获取新闻列表数据
* [新闻统计](#Collection):描述采集新闻相关数据
* [详情页显示js广告](#Ad):描述如何调用接口显示广告详情页

## <a id="Init"></a>初始化（必接）

```java
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
>id1,id2,SLOT_ID_SMALL_IMAGE,SLOT_ID_BIG_IMAGE这4个参数需要开发者在控制台提交工单申请。</br>
开发者在调用初始化接口的时候，需要传入对应参数，参数如下：

参数名 | 说明 |类型
---|---|---
isDebugMode | true为调试模式false为发布模式 |boolean
MEDIA_ID | 广告ID |String
SLOT_ID_SMALL_IMAGE| 小图广告位ID |String
SLOT_ID_BIG_IMAGE| 大图广告位ID |String
ids| 详情页js广告位ID(参看详情页显示js广告) |Map
isNoAd| true为不显示列表页广告false则显示广告 |boolean
## <a id="TopNews"></a>获取置顶新闻数据

```java
DroiNews.requestTopNews(new JSONObject(), new OnLoadNewsListListener() {
    @Override
    public void onLoadNewsListResult(final DroiError droiError, List<NewsObject> list) {
     
    }
});
```
开发者在调用获取置顶新闻数据接口后，会返回请求数据的结果，DroiError为请求的结果是否成功，和erroCode，List为请求成功返回的数据。


名称 | 类型 |说明
---|---|---
droiError.isOk() | bool | true为请求成功，false为请求失败
droiError.getCode() | int | 返回的错误码
droiError.getAppendedMessage() | String | 返回的错信息

## <a id="NewsList"></a>获取新闻列表数据（必接）

```java
JSONArray ca = new JSONArray();
ca.put("新闻");
		
JSONObject requestObj = new JSONObject();
requestObj.put("act", 1);
requestObj.put("size", 7);
requestObj.put("category", ca);

DroiNews.requestTopNews(requestObj, new OnLoadNewsListListener() {
    @Override
    public void onLoadNewsListResult(final DroiError droiError, List<NewsObject> list) {

    }
});

```
开发者在调用获取新闻列表接口前需要传入的参数如下：
请求示例：

```json
{
    "act": 1,
    "index": 1,
    "size": 20,
    "category": [
        "新闻"
    ]
}

```

参数名称 | 类型 | 说明|是否允许为空
---|---|---|---
act | int |获取方式 1初始化数据2上拉加载更多3下拉刷新|否
size | int|请求新闻个数|是
category | JSONArray|分类，可传多个分类|是


开发者在调用获取新闻列表接口后，会返回请求数据的结果，DroiError为请求的结果是否成功，和erroCode，List为请求成功返回的数据。


名称 | 类型 |说明
---|---|---
droiError.isOk() | bool | true为请求成功，false为请求失败
droiError.getCode() | int | 返回的错误码
droiError.getAppendedMessage() | String | 返回的错信息

成功返回数据示例：

```json
{
    "originalurl": "http://op.inews.qq.com/m/DIG2017030302362700?refer=100000119&chl_code=digi&h=0",
    "pk": "d528642e-ffe1-11e6-9463-5254006552a3",
    "crawler": 1,
    "category": "科技",
    "contentid": "0df4ee2e8395490be3a8c801",
    "pubtime": 1488525218,
    "channel": "",
    "source": "腾讯数码",
    "adtype": 0,
    "url": "http://news-src.droibaas.com/stage/d528642e-ffe1-11e6-9463-5254006552a3/d528642e-ffe1-11e6-9463-5254006552a3.html",
    "title": "比WiFi更安全 LiFi使LED灯具成为无线网络新节点",
    "display": 4,
    "images": [
        "http://news-src.droibaas.com/stage/d528642e-ffe1-11e6-9463-5254006552a3/d53a9fea-ffe1-11e6-9463-5254006552a3-s0.jpg",
        "http://news-src.droibaas.com/stage/d528642e-ffe1-11e6-9463-5254006552a3/d53aa92c-ffe1-11e6-9463-5254006552a3-s0.jpg",
        "http://news-src.droibaas.com/stage/d528642e-ffe1-11e6-9463-5254006552a3/d53ab07a-ffe1-11e6-9463-5254006552a3-s0.jpg"
    ]
}

```

参数名称 | 类型 |说明
---|---|---
originalurl | String | 原始地址
pk | String | 爬虫uuid，东方头条pk值，主要用来去重，此值加索引
crawler | int | 0：第三方（绝对URL），1：自有爬虫（相对URL，需要客户端拼接）
category | String | 分类
contentid | String | 新闻详情页ID
pubtime | int | 发表时间
channel | String | 已弃用
source | String | 新闻来源
adtype | int | 广告类型 0：非广告，1：展示广告，2：下载广告 SDK拼装时增加此字段
url | String | 详情页
title | String | 标题
display | int | 展示样式
images | String[] | 列表图片地址

category分类目前有14类：

1 | 2 |3 | 4 |5 | 6 |7 | 8 |9 | 10 |11 | 12 |13 |14
---|---|---|---|---|---|---|---|---|---|---|---|---|---
推荐 | 新闻 | 娱乐 | 财经 | 科技 | 体育 | 汽车 | 军事 | 房产 | 时尚 | 国际 | 动漫 | 文化 | 生活

display展示样式如下：

 0x01 | 0x02 |0x04 | 0x08 |0x10 | 0x20 |0x40 | 0x80 |0x100
---|---|---|---|---|---|---|---|---
无图，纯文字 | 小图 | 三图 | 大图 | 热词、田字 | 四图 | 多行多文本，主要用于段子 | 视频列表页播放 | 专题、大图

**<font color=red>广告数据和新闻数据以adtype字段作区分，0为新闻1为广告</font>**
如果返回的数据为广告类型示例如下：

```json
{
    "extraimgs": [
        "http://adroi.cdn.bcebos.com/cdn/res/zya_certificate_material/1/INTERSTITIAL_IMAGE/6071935425443721_2060955722_cd514d0b.jpg"
    ],
    "originalurl": "",
    "crawler": 0,
    "pubtime": 0,
    "likecount": 0,
    "pk": "",
    "display": 2,
    "clickcount": 0,
    "socialscore": 0,
    "source": "",
    "summarys": [
        "品牌客户解决方案"
    ],
    "titles": [
        "ADroi"
    ],
    "channel": "",
    "adtype": 1,
    "logourls": [
        "http://adroi.cdn.bcebos.com/cdn/res/zya_certificate_material/1/ICON_IMAGE/9937056706217115_1305223314_fb1eb0d1.png"
    ]
}
```
获取广告链接接口：

```
List<NewsObject> list;
NewsObject obj = list.get(i);
String adUrl = obj.onAdClick(View v) //在主线程中执行
```
显示广告上报接口：**<font color=red>(注意如果未调用此接口会导致后台无法统计广告展示，影响广告收益)</font>**

```java
obj.onAdShow(View v);//在广告展示在可视区域后调用
```
## <a id="Collection"></a>新闻统计（可选）

```
//统计新闻点击事件
DroiNews.uploadNewsClickEvent(newsId,"title");//参数newsId为新闻id，String类型，title是新闻标题，String类型。
//统计新闻获取条数
DroiNews.uploadNewsLoadCount(count);//count为新闻条数，int类型。
//统计列表页停留时间
DroiNews.uploadNewsListDwell(time);//time为时间，int类型，单位为秒
//统计详情页停留时间
DroiNews.uploadNewsDetailDwell(newsId, time);//参数newsId为新闻id，String类型，time是时间，int类型。
```
## <a id="Ad"></a>详情页显示js广告（必接）

初始化的时候传入js原生广告id：**<font color=red>(id需要开发者在控制台提交工单申请)**</font>
```
HashMap<Integer,String> ids = new HashMap<>();//此为js广告id
ids.put(0,id1);
ids.put(1,id2);
DroiNews.initialize(context, MEDIA_ID, isDebugMode,ids,"-adroi");
*****其他初始化代码******
```
在webview中加入以下设置：

```java
settings.setJavaScriptEnabled(true);//设置可以运行JS脚本
webView.addJavascriptInterface(new AdroiJSInterface(getApplicationContext()),"ADROI");//ADROI为固定字段，不可更改。
```






