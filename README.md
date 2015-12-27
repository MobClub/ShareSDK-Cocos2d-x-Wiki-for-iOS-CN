## ShareSDK-iOS (v3.x) for Cocos2d-x 
* Cocos2d-x开发者方便的集成ShareSDK分享和授权功能。

## Contents
* Getting Started
    * [功能](#Features)
    * [ShareSDK (v3.x) for Cocos2d-x 插件目录结构](#Category structure【ShareSDK (v3.x) for Cocos2d-x Category structure】)
    * [通用集成步骤](#common integrate)
    * [iOS 部分集成步骤](#iOS integrate)
    * [Option Configuration](#Option Configuration)
* common API
    * [ShareSDK.h](#ShareSDK.h)
* Examples
    * [simplyShare](#simplyShare)
    * [showShareEditor](#showShareEditor)
* Notice
    * [各个分享平台参数配置描述](#Social Platform Configuration Item Description)
    切换Cocos2d-x 2.x和3.x

## <a id="Features"></a>功能
* 分享
* 授权

## <a id="Category structure【ShareSDK-iOS (v3.x) for Cocos2d-x Category structure】"></a>ShareSDK (v3.x) for Cocos2d-x 插件目录结构

**1、C2DXShareSDK 包含：**

     1. C2DXShareSDK.h :  
     2. C2DXShareSDK.cpp : 
     3. C2DXShareSSDKTypeDef.h :
    iOS文件夹下包含:
     4. C2DXiOSShareSDK.h :
     5. C2DXiOSShareSDK.mm :

## <a id="common integrate"></a> 通用集成步骤
## Step 1：找到下载的 Sample 中的 C2DXShareSDK，拖拽 C2DXShareSDK 至项目中。

## Step 2：修改 AppDelegate 文件
(1)打开 "AppDelegate.cpp"文件，导入头文件
```cpp
#include "C2DXShareSDK.h"
```
(2)在 AppDelegate::applicationDidFinishLaunching() 函数中添加 ShareSDK 各个平台的初始化函数，例如（新浪微博、QQ、微信、Facebook、Twitter)
```cpp
    //设置平台配置
    //Platforms
    __Dictionary *totalDict = __Dictionary::create();
    
    //新浪微博
    __Dictionary *sinaWeiboConf= __Dictionary::create();
    sinaWeiboConf->setObject(__String::create("568898243"), "app_key");
    sinaWeiboConf->setObject(__String::create("38a4f8204cc784f81f9f0daaf31e02e3"), "app_secret");
    sinaWeiboConf->setObject(__String::create("http://www.sharesdk.cn"), "redirect_uri");
    stringstream sina;
    sina << cn::sharesdk::C2DXPlatTypeSinaWeibo;
    totalDict->setObject(sinaWeiboConf, sina.str());
    
    //微信
    __Dictionary *wechatConf = __Dictionary::create();
    wechatConf->setObject(__String::create("wx4868b35061f87885"), "app_id");
    wechatConf->setObject(__String::create("64020361b8ec4c99936c0e3999a9f249"), "app_secret");
    stringstream wechat;
    wechat << cn::sharesdk::C2DXPlatTypeWechatPlatform;
    totalDict->setObject(wechatConf, wechat.str());
    
    //QQ
    __Dictionary *qqConf = __Dictionary::create();
    qqConf->setObject(__String::create("100371282"), "app_id");
    qqConf->setObject(__String::create("aed9b0303e3ed1e27bae87c33761161d"), "app_key");
    stringstream qq;
    qq << cn::sharesdk::C2DXPlatTypeQQPlatform;
    totalDict->setObject(qqConf, qq.str());
    
    //Facebook
    __Dictionary *fbConf = __Dictionary::create();
    fbConf->setObject(__String::create("107704292745179"), "api_key");
    fbConf->setObject(__String::create("38053202e1a5fe26c80c753071f0b573"), "app_secret");
    stringstream facebook;
    facebook << cn::sharesdk::C2DXPlatTypeFacebook;
    totalDict->setObject(fbConf, facebook.str());
    
    //Twitter
    __Dictionary *twConf = __Dictionary::create();
    twConf->setObject(__String::create("LRBM0H75rWrU9gNHvlEAA2aOy"), "consumer_key");
    twConf->setObject(__String::create("gbeWsZvA9ELJSdoBzJ5oLKX0TU09UOwrzdGfo9Tg7DjyGuMe8G"), "consumer_secret");
    twConf->setObject(__String::create("http://www.mob.com"), "redirect_uri");
    stringstream twitter;
    twitter << cn::sharesdk::C2DXPlatTypeTwitter;
    totalDict->setObject(twConf, twitter.str());
```
Note:加链接 各个平台初始化的详细配置参数，请参考。

## Step 3：分享
(1)构造分享参数，示例如下：
```cpp
    reqID += 1; // 分享计数
    
    __Dictionary *content = __Dictionary::create();
    content -> setObject(__String::create("分享文本"), "text");  // 分享文本
    content -> setObject(__String::create("HelloWorld.png"), "image");// 分享图片
    content -> setObject(__String::create("测试标题"), "title"); // 分享标题
    content -> setObject(__String::create("http://www.mob.com"), "url"); // 分享url
    content -> setObject(__String::createWithFormat("%d", cn::sharesdk::C2DXContentTypeWebPage), "type"); // 分享类型
```
(2)调用分享方法：
```cpp
    C2DXShareSDK::showShareMenu(reqID,NULL,content,100,100,shareContentResultHandler); // 第4，5个参数传入iPad 视图要显示的坐标点
```
(3)设置分享回调方法 shareContentResultHandler，示例如下：
```cpp
//分享回调
void shareContentResultHandler(int seqId, cn::sharesdk::C2DXResponseState state, cn::sharesdk::C2DXPlatType platType, __Dictionary *result)
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
            __Array *allKeys = result->allKeys();
            allKeys->retain();
            for (int i = 0; i < allKeys-> count(); i++)
            {
                __String *key = (__String*)allKeys->getObjectAtIndex(i);
                Ref *obj = result->objectForKey(key->getCString());
                
                log("key = %s", key -> getCString());
                if (dynamic_cast<__String *>(obj))
                {
                    log("value = %s", dynamic_cast<__String *>(obj) -> getCString());
                }
                else if (dynamic_cast<__Integer *>(obj))
                {
                    log("value = %d", dynamic_cast<__Integer *>(obj) -> getValue());
                }
                else if (dynamic_cast<__Double *>(obj))
                {
                    log("value = %f", dynamic_cast<__Double *>(obj) -> getValue());
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

## Step 4：授权
(1)调用授权方法
```cpp
    reqID += 1;
    C2DXShareSDK::getUserInfo(reqID, cn::sharesdk::C2DXPlatTypeSinaWeibo, getUserResultHandler);
```

(2)设置获取用户数据回调 getUserResultHandler，代码如下：
```cpp
void getUserResultHandler(int reqID, C2DXResponseState state, C2DXPlatType platType, __Dictionary *result)
{
    switch (state)
    {
        case cn::sharesdk::C2DXResponseStateSuccess:
        {
            log("Success");
            
            //输出信息
            try
            {
                __Array *allKeys = result -> allKeys();
                allKeys->retain();
                for (int i = 0; i < allKeys -> count(); i++)
                {
                    __String *key = (__String *)allKeys -> getObjectAtIndex(i);
                    Ref *obj = result -> objectForKey(key -> getCString());
                    
                    log("key = %s", key -> getCString());
                    if (dynamic_cast<__String *>(obj))
                    {
                        log("value = %s", dynamic_cast<__String *>(obj) -> getCString());
                    }
                    else if (dynamic_cast<__Integer *>(obj))
                    {
                        log("value = %d", dynamic_cast<__Integer *>(obj) -> getValue());
                    }
                    else if (dynamic_cast<__Double *>(obj))
                    {
                        log("value = %f", dynamic_cast<__Double *>(obj) -> getValue());
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
            __Array *allKeys = result->allKeys();
            allKeys->retain();
            for (int i = 0; i < allKeys-> count(); i++)
            {
                __String *key = (__String*)allKeys->getObjectAtIndex(i);
                Ref *obj = result->objectForKey(key->getCString());
                
                log("key = %s", key -> getCString());
                if (dynamic_cast<__String *>(obj))
                {
                    log("value = %s", dynamic_cast<__String *>(obj) -> getCString());
                }
                else if (dynamic_cast<__Integer *>(obj))
                {
                    log("value = %d", dynamic_cast<__Integer *>(obj) -> getValue());
                }
                else if (dynamic_cast<__Double *>(obj))
                {
                    log("value = %f", dynamic_cast<__Double *>(obj) -> getValue());
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
## <a id="iOS integrate"></a>iOS 部分集成步骤
