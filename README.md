## ShareSDK-iOS (v3.x) for Cocos2d-x 
* Cocos2d-x开发者方便的集成ShareSDK分享和授权功能。

## 目录
* Getting started
    * [1、新建项目并下载 ShareSDK](#Download)
    * [2、初始化ShareSDK并设置社交平台](#init)
    * [3、ShareSDK接口的调用](#interface)
* Notice
    * [各个分享平台参数配置描述](#SocialConfiguration)
    * [构造分享内容字段参数值](#ContentConfiguration)
    


## <a id="Download"></a>1、新建项目并下载 ShareSDK
* 1、Cocos2d-x项目环境搭建，不会的童鞋自行面壁哈: [网页链接](http://www.jianshu.com/p/676f2e9096d4).
* 2、ShareSDK iOS版本的 Cocos2d-x 插件是在ShareSDK iOS版本基础上对接口做个C++接口的封装，是依赖ShareSDK for iOS的。所以下载ShareSDK就包含了以下2部分，下载下来的文件目录截图如下：

  （1）ShareSDK iOS版本的下载：[网页链接](http://www.mob.com/#/downloadDetail/ShareSDK/ios)
  
  （2）Cocos2d-x插件的下载: ：[网页链接](https://github.com/MobClub/New-C2DX-For-ShareSDK)
（包含demo，需要的是C2DXShareSDK）

![初始化](http://wiki.mob.com/wp-content/uploads/2015/12/tmp7f2f3116.png)

* 将以上2个红色方框内的文件拖到新建的Cocos2d-x项目中。


## <a id="init"></a> 2、初始化ShareSDK并设置社交平台
## iOS 部分

ShareSDK v4.0.0及 需要在Info.plist 中设置MOBAppKey 和 MOBAppSecret
![(setappkey)](http://wiki.mob.com/wp-content/uploads/2017/06/appkey.jpeg)

 * 1、下载的 ShareSDK 文件夹拖拽进项目
   
  
  ![img](http://wiki.mob.com/wp-content/uploads/2015/11/ShareSDK2.jpg)
  注意：请务必在上述步骤中选择“Create groups for any added folders”单选按钮组。如果你选择“Create folder references for any added folders”，一个蓝色的文件夹引用将被添加到项目并且将无法找到它的资源。


* 2、添加必须的依赖库

 必须添加的依赖库如下 ( Xcode 7 下 *.dylib库后缀名更改为*.tbd )：
 
  ```objc
  libicucore.dylib
  libz.dylib
  libstdc++.dylib
  JavaScriptCore.framework
  ```

 以下依赖库根据社交平台添加：
  
  ```objc
  新浪微博SDK依赖库
  
  ImageIO.framework
  libsqlite3.dylib
  ```
  
  ```objc
  微信SDK依赖库 
  
  libsqlite3.dylib
  ```
  
  ```objc
  QQ好友和QQ空间SDK依赖库 
  
  libsqlite3.dylib
  ```
  
  ```objc
  短信和邮件需要依赖库 
  
  MessageUI.framework
  ```
  
  ```objc
  Google＋SDK依赖库 
  
  CoreMotion.framework
  CoreLocation.framework
  MediaPlayer.framework
  AssetsLibrary.framework
  ```

 添加依赖库的方法如下:

 ![img](http://wiki.mob.com/wp-content/uploads/2015/09/233D16A0-E241-4D4B-ACF2-4C03259F995A.png)

* 3、各个社交平台需要的配置（url schemes 等）可以参考文档中的可选配置项：[网页链接](http://wiki.mob.com/ios%E7%AE%80%E6%B4%81%E7%89%88%E5%BF%AB%E9%80%9F%E9%9B%86%E6%88%90/)

## Cocos2d-x 部分

* 1、选择需要的平台SDK和Cocos2d-x环境

    打开 C2DXShareSDK / iOS / C2DXiOSShareSDK.mm ，按需注释掉已导入的原生SDK库

  ```
#define IMPORT_SINA_WEIBO_LIB               //导入新浪微博库，如果不需要新浪微博客户端分享可以注释此行
#define IMPORT_QZONE_QQ_LIB                 //导入腾讯开发平台库，如果不需要QQ空间分享、SSO或者QQ好友分享可以注释此行
#define IMPORT_RENREN_LIB                   //导入人人库，如果不需要人人SSO，可以注释此行
#define IMPORT_GOOGLE_PLUS_LIB              //导入Google+库，如果不需要Google+分享可以注释此行
#define IMPORT_WECHAT_LIB                   //导入微信库，如果不需要微信分享可以注释此行
//#define IMPORT_ALIPAY_LIB                   //导入支付宝分享库，如果不需要支付宝分享可以注释此行
//#define IMPORT_KAKAO_LIB                    //导入Kakao库，如果不需要Kakao分享可以注释此行
```

   ~~打开 C2DXShareSDK / C2DXShareSDKTypeDef.h ，按需选择要使用的 Cocos2d-x的版本（切换适配Cocos2d-x 2.x 或者 3.x 版本）~~
   
   上面这一步已经不再需要，C2DXShareSDKTypeDef.h里面会根据`COCOS2D_VERSION`自动判断Cocos2d-x的版本。
    

* 2、修改 "AppDelegate" 进行初始化
  
  a、打开 “AppDelegate.cpp”文件，导入头文件

  ```cpp
   #include "C2DXShareSDK.h"
  ```

  b、在 AppDelegate::applicationDidFinishLaunching() 函数中添加 ShareSDK 各个平台的初始化函数，例如（新浪微博、QQ、微信、Facebook、Twitter)

  ```cpp
    //设置平台配置
    //Platforms
    C2DXDictionary *totalDict = C2DXDictionary::create();
    
    //新浪微博
    C2DXDictionary *sinaWeiboConf= C2DXDictionary::create();
    sinaWeiboConf->setObject(C2DXString::create("568898243"), "app_key");
    sinaWeiboConf->setObject(C2DXString::create("38a4f8204cc784f81f9f0daaf31e02e3"), "app_secret");
    sinaWeiboConf->setObject(C2DXString::create("http://www.sharesdk.cn"), "redirect_uri");
    stringstream sina;
    sina << cn::sharesdk::C2DXPlatTypeSinaWeibo;
    totalDict->setObject(sinaWeiboConf, sina.str());
    
    //微信
    C2DXDictionary *wechatConf = C2DXDictionary::create();
    wechatConf->setObject(C2DXString::create("wx4868b35061f87885"), "app_id");
    wechatConf->setObject(C2DXString::create("64020361b8ec4c99936c0e3999a9f249"), "app_secret");
    stringstream wechat;
    wechat << cn::sharesdk::C2DXPlatTypeWechatPlatform;
    totalDict->setObject(wechatConf, wechat.str());
    
    //QQ
    C2DXDictionary *qqConf = C2DXDictionary::create();
    qqConf->setObject(C2DXString::create("100371282"), "app_id");
    qqConf->setObject(C2DXString::create("aed9b0303e3ed1e27bae87c33761161d"), "app_key");
    stringstream qq;
    qq << cn::sharesdk::C2DXPlatTypeQQPlatform;
    totalDict->setObject(qqConf, qq.str());
    
    //Facebook
    C2DXDictionary *fbConf = C2DXDictionary::create();
    fbConf->setObject(C2DXString::create("107704292745179"), "api_key");
    fbConf->setObject(C2DXString::create("38053202e1a5fe26c80c753071f0b573"), "app_secret");
    stringstream facebook;
    facebook << cn::sharesdk::C2DXPlatTypeFacebook;
    totalDict->setObject(fbConf, facebook.str());
    
    //Twitter
    C2DXDictionary *twConf = C2DXDictionary::create();
    twConf->setObject(C2DXString::create("LRBM0H75rWrU9gNHvlEAA2aOy"), "consumer_key");
    twConf->setObject(C2DXString::create("gbeWsZvA9ELJSdoBzJ5oLKX0TU09UOwrzdGfo9Tg7DjyGuMe8G"), "consumer_secret");
    twConf->setObject(C2DXString::create("http://www.mob.com"), "redirect_uri");
    stringstream twitter;
    twitter << cn::sharesdk::C2DXPlatTypeTwitter;
    totalDict->setObject(twConf, twitter.str());
    
    //在 ShareSDK 官网后台注册应用并获取Appkey，并填入此方法的第一个参数中
    cn::sharesdk::C2DXShareSDK::registerAppAndSetPlatformConfig("8e3320a36606", totalDict); 
```

    [以上平台的app_key、app_secret等字段不同分享平台可能不同，详情可参考](#SocialConfiguration)


## <a id="interface"></a>3、ShareSDK接口的调用
 
## 分享

* 1、在需要分享操作的代码块中进行构造分享参数，示例如下：

  ```cpp
   // reqID += 1; // 分享计数.  现在已经不需要了，这个字段现在封装在了C2DXShareSDK.cpp中。
    
    C2DXDictionary *content = C2DXDictionary::create();
    content -> setObject(C2DXString::create("分享文本"), "text");  // 分享文本
    content -> setObject(C2DXString::create("HelloWorld.png"), "image");// 分享图片
    content -> setObject(C2DXString::create("测试标题"), "title"); // 分享标题
    content -> setObject(C2DXString::create("http://www.mob.com"), "url"); // 分享url
    content -> setObject(C2DXString::createWithFormat("%d", cn::sharesdk::C2DXContentTypeWebPage), "type"); // 分享类型
```

* 2、调用分享方法：

  ```cpp
    C2DXShareSDK::showShareMenu(NULL,content,100,100,shareContentResultHandler); // 第4，5个参数传入 iPad 视图要显示的坐标点，详见API说明
```

* 3、设置分享回调方法 shareContentResultHandler，示例如下：

  ```cpp
//分享回调
void shareContentResultHandler(int seqId, cn::sharesdk::C2DXResponseState state, cn::sharesdk::C2DXPlatType platType, C2DXDictionary *result)
{
    switch (state)
    {
        case cn::sharesdk::C2DXResponseStateSuccess:
        {
            log("Success");
        }
            break;
        case cn::sharesdk::C2DXResponseStateFail:
        {
            log("Fail");
            //回调错误信息
            C2DXArray *allKeys = result->allKeys();
            allKeys->retain();
            for (int i = 0; i < allKeys-> count(); i++)
            {
                C2DXString *key = (C2DXString*)allKeys->getObjectAtIndex(i);
                Ref *obj = result->objectForKey(key->getCString());
                
                log("key = %s", key -> getCString());
                if (dynamic_cast<C2DXString *>(obj))
                {
                    log("value = %s", dynamic_cast<C2DXString *>(obj) -> getCString());
                }
                else if (dynamic_cast<C2DXInteger *>(obj))
                {
                    log("value = %d", dynamic_cast<C2DXInteger *>(obj) -> getValue());
                }
                else if (dynamic_cast<C2DXDouble *>(obj))
                {
                    log("value = %f", dynamic_cast<C2DXDouble *>(obj) -> getValue());
                }
            }
        }
            break;
        case cn::sharesdk::C2DXResponseStateCancel:
        {
            log("Cancel");
        }
            break;
        default:
            break;
    }
}
```

## 授权

* 1、调用授权方法

   ```cpp
   // reqID += 1;  // 现在已经不需要了，这个字段现在封装在了C2DXShareSDK.cpp中
    
   C2DXShareSDK::getUserInfo(cn::sharesdk::C2DXPlatTypeSinaWeibo, getUserResultHandler);
   ```



* 2、设置获取用户数据回调 getUserResultHandler，代码如下：

  ```cpp
void getUserResultHandler(int reqID, C2DXResponseState state, C2DXPlatType platType, C2DXDictionary *result)
{
    switch (state)
    {
        case cn::sharesdk::C2DXResponseStateSuccess:
        {
            log("Success");
            
            //输出信息
            try
            {
                C2DXArray *allKeys = result -> allKeys();
                allKeys->retain();
                for (int i = 0; i < allKeys -> count(); i++)
                {
                    C2DXString *key = (C2DXString *)allKeys -> getObjectAtIndex(i);
                    Ref *obj = result -> objectForKey(key -> getCString());
                    
                    log("key = %s", key -> getCString());
                    if (dynamic_cast<C2DXString *>(obj))
                    {
                        log("value = %s", dynamic_cast<C2DXString *>(obj) -> getCString());
                    }
                    else if (dynamic_cast<C2DXInteger *>(obj))
                    {
                        log("value = %d", dynamic_cast<C2DXInteger *>(obj) -> getValue());
                    }
                    else if (dynamic_cast<C2DXDouble *>(obj))
                    {
                        log("value = %f", dynamic_cast<C2DXDouble *>(obj) -> getValue());
                    }
                }
                allKeys->release();
            }
            catch(...)
            {
                log("==============error");
            }
        }
            break;
        case cn::sharesdk::C2DXResponseStateFail:
        {
            log("Fail");
            //回调错误信息
            C2DXArray *allKeys = result->allKeys();
            allKeys->retain();
            for (int i = 0; i < allKeys-> count(); i++)
            {
                C2DXString *key = (C2DXString*)allKeys->objectAtIndex(i);
                Ref *obj = result->objectForKey(key->getCString());
                
                log("key = %s", key -> getCString());
                if (dynamic_cast<C2DXString *>(obj))
                {
                    log("value = %s", dynamic_cast<C2DXString *>(obj) -> getCString());
                }
                else if (dynamic_cast<C2DXInteger *>(obj))
                {
                    log("value = %d", dynamic_cast<C2DXInteger *>(obj) -> getValue());
                }
                else if (dynamic_cast<C2DXDouble *>(obj))
                {
                    log("value = %f", dynamic_cast<C2DXDouble *>(obj) -> getValue());
                }
            }
        }
            break;
        case cn::sharesdk::C2DXResponseStateCancel:
        {
            log("Cancel");
        }
            break;
        default:
            break;
    }
}
```

## <a id="SocialConfiguration"></a>各个分享平台参数配置描述
各个社交平台在初始化时 app_key、app_secret等字段不同分享平台可能不同，可参考下表进行调整。

平台                 | 通用字段           | 通用字段               |通用字段             | iOS 特需           | Android 特需  |
--------------------|------------------|-----------------------|--------------------|------------------|-----------------------|
新浪微博              | app_key          | app_secret           |redirect_uri         | auth_type        |                      |
腾讯微博              | app_key          | app_secret           |redirect_uri         | ––               |                      |
豆瓣                 | api_key          | secret               | redirect_uri        | ––               |    | 
QQ系列               | app_id           | app_key              | ––                  | auth_type        |    |
人人网               |app_id            | app_key              |secret_key           | auth_type        |    |
开心网               | api_key          | secret_key           |redirect_uri         | ––               |    |
Facebook            | api_key          | app_secret           |  ––                 | auth_type        |    |
Twitter             | consumer_key     | consumer_secret      |redirect_uri         |  ––              |    |
GooglePlus          |client_id         | client_secret        |redirect_uri         | auth_type        |    |
微信系列              | app_id          | app_secret           | ––                   |  ––             |    |
Pocket              | consumer_key     | ––                   |redirect_uri         | auth_type        |    |
Instragram          | client_id        | client_secret        |redirect_uri         |  ––              |    |
LinkedIn            | api_key          | secret_key           |redirect_uri         |  ––              |    |
Tumblr              | consumer_key     | consumer_secret      |callback_url         |  ––              |    |
Flicker             | api_key          | api_secret           | ––                  |  ––              |    |
有道                 | consumer_key     | consumer_secret      |oauth_callback       |  ––              |    |
印象笔记Evernote      | consumer_key     |consumer_secret       |––                   | ––               |    |
支付宝好友            | app_id           | ––                   | ––                  | ––               |    |
Pinterest           | client_id         | ––                   |––                   | ––               |    |
Kakao系列            | app_key          | rest_api_key         |redirect_uri         | auth_type        |     |
Dropbox             | app_key           | app_secret           |oauth_callback      |  ––              |     |
Vkontakte           | application_id    | secret_key           |––                   | ––              |     |
明道                 | app_key           | app_secret           |redirect_uri        | ––              |     |
易信                 | app_id            | app_secret           |redirect_uri        | auth_type        |     |
Instapaper           |consumer_key      | consumer_secret      |––                 |––               |    |
