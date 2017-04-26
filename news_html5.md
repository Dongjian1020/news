# 信息流（Html5版）
## 简介
卓易信息流SDK是卓易科技针对开发者，快速展示信息流开发的一套基于Android的程序接口，开发者可以通过调用SDK的接口，轻松的访问聚合新闻资讯相关的数据服务，SDK除了聚合新闻数据之外还融入了商业广告，满足商业化需求。

以下就数据服务开发相关技术区分成不同小节来描述如何开发：
* [](#Init):描述初始化
* [JS广告注入(#JsAD):描述如何获取置顶新闻
* [获取新闻列表数据](#NewsList):描述获取新闻列表数据
* [新闻统计](#Collection):描述采集新闻相关数据
* [显示广告](#Ad):描述如何调用接口显示广告详情页

## <a id="Init"></a>初始化（必接）

```java
HashMap<Integer,String> ids = new HashMap<>();
ids.put(0,"sd69b9896");
ids.put(1,"s04e3d9fc");
ids.put(2,"s04e3d9fc");

AdView.preLoad(context);
AdView.setAppId(context, MEDIA_ID);
AdView.setDebug(isDebugMode);
AdView.setLogSwitch(isLog);
AdView.setJsAdId(AdSize.NativeAd,mediaId,ids);

```
开发者在调用初始化接口的时候，需要传入五个参数，除isDebugMode外其他四个参数从开放平台申请，参数如下：

参数名 | 说明 |
---|---
isDebugMode | true为调试模式false为发布模式 
isLog | ture 为打印本地log false则不打印
MEDIA_ID | 广告ID
SLOT_ID_SMALL_IMAGE| 小图广告位ID
SLOT_ID_BIG_IMAGE| 大图广告位ID
SLOT_ID_TRI_IMAGE| 三图广告位ID


## <a id="JsAD"></a>获取新闻列表数据（必接）
webview js注入
```java
WebView webView = (WebView) findViewById(R.id.webview);
WebSettings settings = webView.getSettings();
settings.setDomStorageEnabled(true);
settings.setJavaScriptEnabled(true);//设置可以运行JS脚本
webView.addJavascriptInterface(new JavascriptInterface(getApplicationContext()),"ADROI");
webView.loadUrl(url);

```
本地与js通信方法

``` java
	// js通信接口
    public class JavascriptInterface {

    private Context context;

    public JavascriptInterface(Context context) {
        this.context = context;
     }

    @android.webkit.JavascriptInterface
    public String doAction(String appid,String adslotId,String slot_type) {
       int type = Integer.parseInt(slot_type);
       Log.e("slot_type","=====>"+slot_type);
       String res = AdView.getJSRequest(context,null,null,type);
       Log.e("doAction","=====>"+res);
       return res;
    }

    @android.webkit.JavascriptInterface
    public void doLog(String request,String response){
        AdView.writeJsLog(context,request,response);
    }
   }
    
参数名称 | 类型 | 说明|是否允许为空
---|---|---|---
act | int |获取方式 1初始化数据2上拉加载更多3下拉刷新|否
size | int|请求新闻个数|是
size | JSONArray|分类，可传多个分类|是


开发者在调用获取新闻列表接口后，会返回请求数据的结果，DroiError为请求的结果是否成功，和erroCode，List为请求成功返回的数据。



