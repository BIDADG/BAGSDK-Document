# iOS - 接入指南 Swift

支持的聚合及平台：Carty，Max 
支持广告类型：横幅，激励视频，插屏，开屏，原生

流程说明：

- 调用BAGSDK加载时，SDK会根据本地配置同时向对多个聚合或广告平台发起加载请求。

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/whiteboard_exported_image.png)

- 调用BAGSDK展示时，SDK会自动对已配置的聚合或广告平台进行本地比价并展示最高价格的广告。

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/whiteboard_exported_image-2.png)


### 一、集成SDK
SDK下载地址：https://github.com/BIDADG/BAGSDK-iOS

- 将 BidAggregator 文件夹整个添加到项目中

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/project.png)

- 在项目的Bridging-Header中import以下头文件

```swift
#import "BidAggregatorSDK.h"
#import "BAGAppOpen.h"
#import "BAGBanner.h"
#import "BAGInterstitial.h"
#import "BAGNative.h"
#import "BAGNativeView.h"
#import "BAGRewardedVideo.h"
```

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/swiftSet.png)

- - 如之前项目没有设置过Bridging-Header，可在build Settings的 Objective-C Bridging Header 中设置文件路径。

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/swiftSet2.png)


- 在项目中集成各平台

如：通过pod集成各平台的SDK

```http
pod 'AppLovinSDK'
pod 'CartySDK'
```

### 二、初始化

```swift
//传入配置
BidAggregatorSDK.sharedInstance().start(with: data, delegate: self);
```

- 配置格式及说明

```json
{
     "platforms":[
         {
             "platform_id":0,//平台ID 可自定义
             "platform_name":"Carty",
             "className":"BAGCartyAdapter",
             "app_id":"a203273261891579" //Carty平台的appid
             
         },
         {
             "platform_id":1,//平台ID 可自定义
             "platform_name":"Max",
             "className":"BAGMaxAdapter",
             "sdk_key":"05TMDQ5tZabpXQ45_UTbmEGNUtVAzSTzT6KmWQc5_CuWdzccS4DCITZoL3yIWUG3bbq60QC_d4WF28tUC4gVTF",//MAX 平台的SDKkey
             "testDevice":true,//是否开启MAX测试模式 开启时会iOS会自动将设备的IDFV添加到MAX的测试设备列表中
             "test_ids": []//测试设备列表
         }
     ]
 }
```

-   回调

```swift

func initFinish(withPlatformID platformID: Int, platformName: String, failedError: (any Error)?) {
    //平台初始化结束
}

func allPlatformFinish(_ error: (any Error)?) {
    //所有平台初始化结束
}

```

### 三、开屏广告

#### 1.  创建对象

```swift
//传入配置
openAd = BAGAppOpen(infoData: data, delegate: self)
openAd.revenueDelegate = self
```

-  配置格式及说明

```json
{
    "name":"appOpen",//广告位名称 可自定义
    "maxWaitTime":3000,//单位：毫秒，最长加载回调等待时长。
                       //例：设置为3000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调。
    "platforms":[
        {
            "platform_id":0,//平台ID 可自定义
            "platform_name":"Carty",
            "className":"BAGCartyAdapter",
            "placement_id":"281217152630",//Carty后台的 placementid
            "ad_type":0,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":1,//平台ID 可自定义
            "platform_name":"Max",
            "className":"BAGMaxAdapter",
            "unit_id":"YOUR_AD_UNIT_ID",//MAX后台的 unitid
            "placement":"",
            "ad_type":0,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        }
    ]
}
```

#### 2. 加载广告

```swift
openAd.loadAd()
```

#### 3. 展示广告

```swift
if(openAd.isReady())
{
    openAd.showAd(self)
}
```

#### 4. 回调

```swift
func appOpenDidLoad(_ ad: BAGAdObject) {
        //加载成功
}

func appOpenLoadFailWithError(_ error: any Error) {
       //所有平台加载失败
}

func appOpenDidShow(_ ad: BAGAdObject) {
     //广告展示
}

func appOpenShowFail(_ ad: BAGAdObject?, withError error: BAGError) {
    //广告展示失败
}

func appOpenDidClick(_ ad: BAGAdObject) {
    //广告点击
}

func appOpenDidDismiss(_ ad: BAGAdObject) {
    //广告关闭
}

func adPayRevenue(_ ad: BAGAdObject!) {
    //收益回调
}

//可选
func appOpenWillStartLoad(_ ad: BAGAdObject, load loadObject: Any?) {
    //平台开始加载，loadObject返回各平台加载对象
}


func appOpenAdapterLoadFail(_ ad: BAGAdObject, withError error: BAGError) {
    //平台加载失败
}

func appOpenLoadAllFinish() {
    //所有平台加载结束
}
```

### 四、插屏广告

#### 1. 创建对象

```swift
//传入配置
interstitialAd = BAGInterstitial(infoData: data, delegate: self)
interstitialAd.revenueDelegate = self
```

- 配置格式及说明


```json
{
    "name":"interstitial",//广告位名称 可自定义
    "maxWaitTime":0,//单位：毫秒，最长加载回调等待时长 可选。
                    //例：设置为3000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调。
    "platforms":[
        {
            "platform_id":0,//平台ID 可自定义
            "platform_name":"Carty",
            "className":"BAGCartyAdapter",
            "placement_id":"281217152620",//Carty后台的 placementid
            "ad_type":2,//广告类型（仅iOS使用）
           //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":1,//平台ID 可自定义
            "platform_name":"Max",
            "className":"BAGMaxAdapter",
            "unit_id":"YOUR_AD_UNIT_ID",//MAX后台的 unitid
            "placement":"",
            "ad_type":2,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        }
    ]
}
```

#### 2. 加载广告

```swift
interstitialAd.loadAd()
```

#### 3. 展示广告

```swift
if(interstitialAd.isReady())
{
    interstitialAd.showAd(self)
}
```

#### 4. 回调

```swift
func interstitialDidLoad(_ ad: BAGAdObject) {
    //加载成功
}

func interstitialLoadFailWithError(_ error: any Error) {
    //所有平台加载失败
}

func interstitialDidShow(_ ad: BAGAdObject) {
    //广告展示
}

func interstitialShowFail(_ ad: BAGAdObject?, withError error: BAGError) {
    //广告展示失败
}

func interstitialDidClick(_ ad: BAGAdObject) {
    //广告点击
}

func interstitialDidDismiss(_ ad: BAGAdObject) {
    //广告关闭
}

func adPayRevenue(_ ad: BAGAdObject!) {
    //收益回调
}

//可选

func interstitialWillStartLoad(_ ad: BAGAdObject, load loadObject: Any?) {
    //平台开始加载，loadObject返回各平台加载对象
}

func interstitialAdapterLoadFail(_ ad: BAGAdObject, withError error: BAGError) {
     //平台加载失败
}

func interstitialLoadAllFinish() {
    //所有平台加载结束
}
```

### 五、激励视频

#### 1. 创建对象

```swift
//传入配置
rewardedVideoAd = BAGRewardedVideo(infoData: data! as Data, delegate: self)
rewardedVideoAd.revenueDelegate = self
```

- 配置格式及说明

```json
{
    "name":"rewardedVideo",//广告位名称 可自定义
    "maxWaitTime":3000,//单位：毫秒，最长加载回调等待时长，可选。
                       //例：设置为3000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调。
    "platforms":[
        {
            "platform_id":0,//平台ID 可自定义
            "platform_name":"Carty",
            "className":"BAGCartyAdapter",
            "placement_id":"281217152650",//Carty后台的 placementid
            "ad_type":4,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":1,//平台ID 可自定义
            "platform_name":"Max",
            "className":"BAGMaxAdapter",
            "unit_id":"YOUR_AD_UNIT_ID",//MAX后台的 unitid
            "placement":"",
            "ad_type":4,//广告类型 （仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        }
    ]
}
```

#### 2. 加载广告

```swift
rewardedVideoAd.loadAd()
```

#### 3. 展示广告

```swift
if(rewardedVideoAd.isReady())
{    
    //设置自定义参数 可选
    rewardedVideoAd.customRewardString = "customRewardString"
    rewardedVideoAd.showAd(self)
}
```

#### 4. 回调

```swift
func rewardedVideoDidLoad(_ ad: BAGAdObject) {
    //加载成功
}

func rewardedVideoLoadFailWithError(_ error: any Error) {
    //所有平台加载失败
}

func rewardedVideoDidShow(_ ad: BAGAdObject) {
    //广告展示
}

func rewardedVideoShowFail(_ ad: BAGAdObject?, withError error: BAGError) {
    //广告展示失败
}

func rewardedVideoDidClick(_ ad: BAGAdObject) {
    //广告点击
}

func rewardedVideoDidDismiss(_ ad: BAGAdObject) {
    //广告关闭
}

func rewardedVideoDidEarnReward(_ ad: BAGAdObject, rewardInfo: [AnyHashable : Any]) {
    //奖励回调
}

func adPayRevenue(_ ad: BAGAdObject!) {
    //收益回调
}

//可选

func rewardedVideoWillStartLoad(_ ad: BAGAdObject, load loadObject: Any?) {
    //平台开始加载，loadObject返回各平台加载对象
}

func rewardedVideoAdapterLoadFail(_ ad: BAGAdObject, withError error: BAGError) {
    //平台加载失败
}

func rewardedVideoLoadAllFinish() {
     //所有平台加载结束
}
```

### 六、横幅广告

#### 1. 创建对象

```swift
//传入配置
bannerAd = BAGBanner(infoData: data! as Data, delegate: self)
bannerAd.revenueDelegate = self
bannerAd.frame = CGRectMake(0, 0, 320, 50)
bannerAd.rootViewController = self;
adView.addSubview(bannerAd)
```

-  配置格式及说明

```json
{
    "name":"banner",//广告位名称 可自定义
    "maxWaitTime":3000,//单位：毫秒，最长加载回调等待时长，可选。
                       //例：设置为3000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调。
    "platforms":[
        {
            "platform_id":0,//平台ID 可自定义
            "platform_name":"Carty",
            "className":"BAGCartyAdapter",
            "placement_id":"281217152640",//Carty后台的 placementid
            "banner_format":0,//横幅类型 0=320*50，1=320*100，2=300*250
            "banner_width":320,//设置Carty Banner宽
            "banner_height":50,//设置Carty Banner高
            "ad_type":1,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":1,//平台ID 可自定义
            "platform_name":"Max",
            "className":"BAGMaxAdapter",
            "unit_id":"YOUR_AD_UNIT_ID",//MAX后台的 unitid
            "placement":"",
            "banner_format":0,//横幅类型 0=320*50，1=320*100，2=300*250
            "banner_width":320,//设置MAX Banner宽
            "banner_height":50,//设置MAX Banner高
            "ad_type":1,//广告类型 （仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        }
    ]
}
```

#### 2. 加载广告

默认加载成功后自动展示

```swift
bannerAd.loadAd()
```

#### 3. 回调

```swift
func bannerDidLoad(_ ad: BAGAdObject) {
    //加载成功
}

func bannerLoadFailWithError(_ error: any Error) {
    //所有平台加载失败
}

func bannerDidShow(_ ad: BAGAdObject) {
    //广告展示
}

func bannerShowFail(_ ad: BAGAdObject?, withError error: BAGError) {
    //广告展示失败
}

func bannerDidClick(_ ad: BAGAdObject) {
    //广告点击
}

func bannerDidClose(_ ad: BAGAdObject) {
    //广告关闭
}

func adPayRevenue(_ ad: BAGAdObject!) {
    //收益回调
}

//可选

func bannerWillStartLoad(_ ad: BAGAdObject, load loadObject: Any?) {
    //平台开始加载
}

func bannerAdapterLoadFail(_ ad: BAGAdObject, withError error: BAGError) {
    //平台加载失败
}

func bannerLoadAllFinish() {
    //所有平台加载结束
}
```

#### 4. 其他

- 横幅默认加载成功后马上展示广告。如需要手动控制展示可以如下设置。

```swift

//关闭自动展示
bannerAd.closeAutoShow = true;

//在需要时手动调用showAd
bannerAd?.showAd()

```

- 停止MAX的横幅刷新

```swift
bannerAd.stopAutoRefresh()
```

- 开始MAX的横幅刷新


```swift
bannerAd.stopAutoRefresh()
```

### 七、原生广告

#### 1. 创建对象

```swift
//传入配置
native = BAGNative(infoData: data! as Data, delegate: self)
native.revenueDelegate = self
native.rootViewController = self
```

- 配置格式及说明

```json
{
    "name":"native",//广告位名称 可自定义
    "maxWaitTime":3000,//单位：毫秒，最长加载回调等待时长，可选。
                       //例：设置为3000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调。
    "platforms":[
        {
            "platform_id":0,//平台ID 可自定义
            "platform_name":"Carty",
            "className":"BAGCartyAdapter",
            "placement_id":"281217152610",//Carty后台的 placementid
            "ad_type":3,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":1,//平台ID 可自定义
            "platform_name":"Max",
            "className":"BAGMaxAdapter",
            "unit_id":"YOUR_AD_UNIT_ID",//MAX后台的 unitid
            "placement":"",
            "ad_type":3,//广告类型 （仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        }
    ]
}
```

#### 2. 加载广告

```swift
native.loadAd()
```

#### 3. 展示广告

```swift
if(!native!.isReady()){
    return;
}
//新建一个BAGNativeView
nativeAdView = BAGNativeView();
nativeAdView.frame = adView.bounds;
//绑定自定义AdView的各元素
nativeAdView.adView = customView;
nativeAdView.mainImageView = customView.mainImageView;
nativeAdView.iconImageView = customView.iconImageView;
nativeAdView.adChoiceImageView =  customView.adChoiceImageView;
nativeAdView.titleLabel = customView.titleLabel;
nativeAdView.textLabel = customView.textLabel;
nativeAdView.ctaButton = customView.ctaButton
//设置可点击元素 可选
//nativeAdView.clickArray = [customView.titleLabel,customView.ctaButton];
adView.addSubview(nativeAdView)
//调用渲染方法
native.render(with:nativeAdView)
```

#### 4. 回调

```swift
func nativeDidLoad(_ ad: BAGAdObject) {
    //加载成功
}

func nativeLoadFailWithError(_ error: any Error) {
    //所有平台加载失败
}

func nativeDidShow(_ ad: BAGAdObject) {
    //广告展示
}

func nativeShowFail(_ ad: BAGAdObject?, withError error: BAGError) {
     //广告展示失败
}

func nativeDidClick(_ ad: BAGAdObject) {
    //广告点击
}

func adPayRevenue(_ ad: BAGAdObject!) {
     //收益回调
}

//可选
func nativeWillStartLoad(_ ad: BAGAdObject, load loadObject: Any?) {
    //平台开始加载，loadObject返回各平台加载对象
}

func nativeAdapterLoadFail(_ ad: BAGAdObject, withError error: BAGError) {
    //平台加载失败
}

func nativeLoadAllFinish() {
    //所有平台加载结束
}
```

### 八、其他

#### 1. 设置 DoNotSell

```swift
//用户未选择退出
BidAggregatorSDK.sharedInstance().setDoNotSell(false)

//用户选择退出
BidAggregatorSDK.sharedInstance().setDoNotSell(true)
```


#### 2. 设置 COPPA

```swift
//用户非儿童
BidAggregatorSDK.sharedInstance().setCOPPAStatus(false)

//用户是儿童
BidAggregatorSDK.sharedInstance().setCOPPAStatus(true)
```


#### 3. 设置UserID

```swift
BidAggregatorSDK.sharedInstance().setUserID("YOUR_USER_ID")
```

#### 4. 回调中的 BAGAdObject 对象属性说明
|属性|说明|
|---|---|
|CGFloat ecpm|每千次展示的有效收益|
|BAGAdType adType|广告类型：开屏 = 0,横幅 = 1,插屏 = 2,原生 = 3,激励视频 = 4|
|NSInteger platformID|配置中设置的platform_id|
|NSString *platformName|配置中设置的platform_name|
|NSString *mediationPlacementId|各平台配置中设置的广告位ID |
|NSString *networkPlacement|聚合返回平台的networkPlacement |
|NSString *networkName|聚合返回平台的networkName |
|NSString *revenuePrecision|聚合返回平台的revenuePrecision |