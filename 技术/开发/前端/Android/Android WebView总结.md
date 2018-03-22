# 1.简介
WebView是一个基于webkit引擎、展现web页面的控件。

Android的Webview在低版本和高版本采用了不同的webkit版本内核，4.4之后直接使用了Chrome。

# 2.作用
显示和渲染Web页面；

直接使用html文件(网络上或本地assets中)作为布局；

可和JavaScript交互调用；

# 3. 常用方法
## 3.1 状态
激活WebView为活跃状态，能正常执行网页的响应
```
webView.onResume() ；
```
当页面被失去焦点被切换到后台不可见状态，需要执行onPause。通过onPause动作通知内核暂停所有的动作，比如DOM的解析、plugin的执行、JavaScript执行。
```
webView.onPause()；
```
当应用程序(存在webview)被切换到后台时，这个方法不仅仅针对当前的webview而是全局的全应用程序的webview。它会暂停所有webview的layout，parsing，javascripttimer。降低CPU功耗。
```
webView.pauseTimers()
```
恢复pauseTimers状态
```
webView.resumeTimers()；
```
在关闭了Activity时，如果Webview的音乐或视频，还在播放。就必须销毁Webview。但是注意：webview调用destory时,webview仍绑定在Activity上。这是由于自定义webview构建时传入了该Activity的context对象。因此需要先从父容器中移除webview,然后再销毁webview:
```
rootLayout.removeView(webView);
webView.destroy();
```
## 3.2 网页前进和后退
是否可以后退
```
Webview.canGoBack()
```
后退网页
```
Webview.goBack()
```
是否可以前进
```
Webview.canGoForward()
```
前进网页
```
Webview.goForward()
```
以当前的index为起始点前进或者后退到历史记录中指定的steps，如果steps为负数则为后退，正数则为前进
```
Webview.goBackOrForward(intsteps)
```
在不做任何处理前提下，WebView浏览网页时点击系统的“Back”键，所在Activity会调用finish()而结束。如果网页内有其他链接，在需要点击“Back”键并不退出当前Activity而是回退到上一个浏览的页面时，需要在Activity中处理并消费掉Back事件
```
@Override
public void onBackPressed() {

    if (mWebView !=null &&mWebView.canGoBack()) {
        mWebView.goBack();
    }else {
        finish();
    }
}
```
## 3.3 缓存数据
清除网页访问留下的缓存，由于内核缓存是全局的，因此这个方法不仅仅针对webview而是针对整个应用程序.
```
Webview.clearCache(true);
```
清除当前webview访问的历史记录，不会清楚webview访问历史记录里的所有记录除了当前访问记录
```
Webview.clearHistory()；
```
这个api仅仅清除自动完成填充的表单数据，并不会清除WebView存储到本地的数据
```
Webview.clearFormData()；
```
# 4. 常用类
## 4.1 WebSettings类
WebSettings是对WebView进行配置和管理的类，需要提供访问网络的权限
```
<uses-permission android:name="android.permission.INTERNET" />
```
声明WebSettings子类
```
WebSettings webSettings = webView.getSettings();
```
如果访问的页面中要与Javascript交互，则webview必须设置支持
```
JavascriptwebSettings.setJavaScriptEnabled(true);
```
支持插件
```
webSettings.setPluginsEnabled(true);
```
设置自适应屏幕，两者合用
```
webSettings.setUseWideViewPort(true);             //将图片调整到适合webview的大小 webSettings.setLoadWithOverviewMode(true);          //缩放至屏幕的大小
```
缩放操作
```
webSettings.setSupportZoom(true);       //支持缩放，默认为true，是下面那个的前提
webSettings.setBuiltInZoomControls(true);            //设置内置的缩放控件。若为false，则该WebView不可缩放
webSettings.setDisplayZoomControls(false);          //隐藏原生的缩放控件
```
其他细节操作
```
webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);          //关闭webview中缓存
webSettings.setAllowFileAccess(true);        //设置可以访问文件
webSettings.setJavaScriptCanOpenWindowsAutomatically(true);             //支持通过JS打开新窗口
webSettings.setLoadsImagesAutomatically(true);             ///支持自动加载图片
webSettings.setDefaultTextEncodingName("utf-8");             //设置编码格式
```
当加载 html 页面时，WebView会在/data/data/包名目录下生成 database 与 cache 两个文件夹，请求的 URL记录保存在 WebViewCache.db，而 URL的内容是保存在 WebViewCache 文件夹下

可以根据当前网络状况决定是否启用缓存，每个 Application 只调用一次 WebSettings.setAppCachePath()，WebSettings.setAppCacheMaxSize()
```
if (NetStatusUtil.isConnected(getApplicationContext())) {
    webSettings.setCacheMode(WebSettings.LOAD_DEFAULT);         //根据cache-control决定是否从网络上取数据。
} else{
    webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);        //没网，则从本地获取，即离线加载}
    webSettings.setDomStorageEnabled(true);          // 开启 DOM storage API 功能webSettings.setDatabaseEnabled(true);              //开启 database storage API 功能webSettings.setAppCacheEnabled(true);           //开启 Application Caches 功能
    String cacheDirPath = getFilesDir().getAbsolutePath() + APP_CACAHE_DIRNAME;
    webSettings.setAppCachePath(cacheDirPath);           //设置  Application Caches 缓存目录
}
```
## 4.2 WebViewClient类
WebViewClient类是处理各种通知和请求的类

loadUrl方法里加载的网页中的所有其他子页面或链接如果需要在本WebView中显示，需要重写shouldOverrideUrlLoading方法，在该网页上的所有加载都经过这个方法

onPageStarted方法是开始载入页面调用的，例如loading页面的初始化

onPageFinished方法时在页面加载结束时调用，如关闭loading页面，切换程序等

onLoadResource方法时在加载页面资源时调用，每个资源(比如图片)的加载都会调用一次

onReceiveError方法时在加载页面的服务器出现错误(如404)时调用，App里面使用webview控件的时候遇到了诸如404这类的错误的时候，若也显示浏览器里面的那种错误提示页面就显得很丑陋了，那么这个时候我们的app就需要加载一个本地的错误提示页面，即webview如何加载一个本地的页面
<pre>
步骤1：写一个html文件（error_handle.html），用于出错时展示给用户看的提示页面

步骤2：将该html文件放置到代码根目录的assets文件夹下

步骤3：复写WebViewClient的onRecievedError方法
</pre>
该方法传回了错误码，根据错误类型可以进行不同的错误分类处理
```
webView.setWebViewClient(new WebViewClient(){
      @Override
      public void onReceivedError(WebView view, int errorCode, String description, String failingUrl){
        switch(errorCode) {

            case HttpStatus.SC_NOT_FOUND:
                view.loadUrl("file:///android_assets/error_handle.html");
                break;
        }
      }
});
```
onReceiveSslError方法作用是处理https请求，WebView默认是不处理https请求的，页面显示空白，需要进行如下设置
```
webView.setWebViewClient(new WebViewClient() {
        @Override
        public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) {

            handler.proceed();    //表示等待证书响应
            // handler.cancel();      //表示挂起连接，为默认方式
            // handler.handleMessage(null);    //可做其他处理
        }
});
```
## 4.3 WebChromeClient类
WebChromeClient类作用是辅助WebView处理JavaScript的对话框、网站图标和网站标题等

onProgressChanged方式可以获取网页的加载进度并显示

onReceivedTitle方法可以获取Web页中的标题

# 5. 与JS交互


# 6. 内存泄漏问题
不在xml中定义WebView，而是在需要的时候在Activity中创建，并且Context使用getApplicationContext方法
```
LinearLayout.LayoutParams params = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT);
mWebView = new WebView(getApplicationContext());
mWebView.setLayoutParams(params);
mLayout.addView(mWebView);
```
在Activity销毁WebView的时候，先让WebView加载null内容，然后移除WebView，再销毁WebView，最后置空
```
@Override
protected void onDestroy() {
    if (mWebView != null) {
        mWebView.loadDataWithBaseURL(null, "", "text/html", "utf-8", null);
        mWebView.clearHistory();
        ((ViewGroup) mWebView.getParent()).removeView(mWebView);
        mWebView.destroy();
        mWebView = null;
    }
        super.onDestroy();
}
```
# 7.问题解析：
1. 当使用webview加载网页时我们会遇到网页加载出错，出现NET::ERR_CACHE_MISS的错误，解决方法只需要在你工程的AndroidManifest.xml文件中加入联网的权限 即可

2. net::ERR_CONNECTION_REFUSED，可能原因是服务未启动或者挂掉，无法访问

3. Scripts may close only the windows that were opened by it. 可能原因是由于加载的网页资源中有弹出窗口的链接或者按钮跳转，因此需要设置WebView支持多窗口，并且实现自己的WebChromeClient类，重写其中的onWindowCreate和onWindowClose方法

# 8. 参考
1. http://blog.csdn.net/carson_ho/article/details/52693322

2. https://stackoverflow.com/questions/7485850/how-to-handle-facebook-like-with-confirm-in-android-webview

