# iOS - 接入指南

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


- **注意**：BidAggregator文件夹下的Adpater目录中是各平台的桥接代码文件。
- 可根据项目需求进行删减，比如：只使用Admob聚合平台，则可以移除其他聚合平台。
![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/adapter.jpeg)

- 根据实际需要在项目中集成各平台

相关平台的Pod配置：

```http
//MAX
pod 'AppLovinSDK'

//Carty
pod 'CartySDK'

//Admob
pod 'Google-Mobile-Ads-SDK'

```

### 二、初始化

```objc
#import "BidAggregatorSDK.h"
//传入配置
[[BidAggregatorSDK sharedInstance] startWithData:data delegate:self];
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
         },
         {
             "platform_id":2,//平台ID 可自定义
             "platform_name":"Admob",
             "className":"BAGAdmobAdapter",
             "test_ids": []//测试设备列表
         }
     ]
 }
```

-   回调

```objc
#pragma mark - BAGSDKDelegate

- (void)initFinishWithPlatformID:(NSInteger)platformID platformName:(NSString *)platformName failedError:(nullable NSError *)failedError
{
   //平台初始化结束
}

- (void)allPlatformFinish:(NSError *)error
{
    //所有平台初始化结束
}
```

### 三、开屏广告

#### 1.  创建对象

```objc
#import "BAGAppOpen.h"
//传入配置
self.open = [[BAGAppOpen alloc] initWithInfoData:data delegate:self];
self.open.revenueDelegate = self;
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
        },
        {
            "platform_id":2,//平台ID 可自定义
            "platform_name":"Admob",
            "className":"BAGAdmobAdapter",
            "unit_id":"ca-app-pub-3940256099942544/5575463023",//Admob后台的 unitid
            "ad_type":0,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
    ]
}
```


#### 2. 加载广告

```objc
[self.open loadAd];
```

#### 3. 展示广告

```objc
if([self.open isReady])
{
    [self.open showAd:self];
}
```

#### 4. 回调

```objc
#pragma mark - BAGPayRevenueDelegate
- (void)adPayRevenue:(BAGAdObject *)ad
{
    //收益回调
}

#pragma mark - BAGAppOpenDelegate
- (void)appOpenDidLoad:(nonnull BAGAdObject *)ad
{
    //加载成功
}

- (void)appOpenLoadFailWithError:(nonnull NSError *)error
{
   //所有平台加载失败
}

- (void)appOpenDidShow:(nonnull BAGAdObject *)ad
{
   //广告展示
}

- (void)appOpenShowFail:(nullable BAGAdObject *)ad withError:(nonnull BAGError *)error
{
   //广告展示失败
}

- (void)appOpenDidClick:(nonnull BAGAdObject *)ad
{
    //广告点击
}

- (void)appOpenDidDismiss:(nonnull BAGAdObject *)ad 
{
   //广告关闭
}

//可选

- (void)appOpenWillStartLoad:(BAGAdObject *)ad loadObject:(nullable id)loadObject
{
    //平台开始加载，loadObject返回各平台加载对象
}

- (void)appOpenAdapterLoadFail:(BAGAdObject *)ad withError:(BAGError *)error
{
   //平台加载失败
}

- (void)appOpenLoadAllFinish
{
    //所有平台加载结束
}
```

### 四、插屏广告

#### 1. 创建对象

```objc
#import "BAGInterstitial.h"
//传入配置
self.interstitial = [[BAGInterstitial alloc] initWithInfoData:data delegate:self]; self.interstitial.revenueDelegate = self;
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
        },
        {
            "platform_id":2,//平台ID 可自定义
            "platform_name":"Admob",
            "className":"BAGAdmobAdapter",
            "unit_id":"ca-app-pub-3940256099942544/4411468910",//Admob后台的 unitid
            "ad_type":2,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        }
    ]
}
```

#### 2. 加载广告

```objc
[self.interstitial loadAd];
```

#### 3. 展示广告

```objc
if([self.interstitial isReady])
{
    [self.interstitial showAd:self];
}
```

#### 4. 回调

```objc
#pragma mark - BAGPayRevenueDelegate

- (void)adPayRevenue:(BAGAdObject *)ad
{
    //收益回调
}

#pragma mark - BAGInterstitialDelegate
- (void)interstitialDidLoad:(nonnull BAGAdObject *)ad
{
    //加载成功
}

- (void)interstitialLoadFailWithError:(nonnull NSError *)error
{
   //所有平台加载失败
}

- (void)interstitialDidShow:(nonnull BAGAdObject *)ad
{
   //广告展示
}

- (void)interstitialShowFail:(nullable BAGAdObject *)ad withError:(nonnull BAGError *)error
{
   //广告展示失败
}

- (void)interstitialDidClick:(nonnull BAGAdObject *)ad
{
    //广告点击
}

- (void)interstitialDidDismiss:(nonnull BAGAdObject *)ad
{
   //广告关闭
}

//可选

- (void)interstitialWillStartLoad:(BAGAdObject *)ad loadObject:(nullable id)loadObject
{
    //平台开始加载，loadObject返回各平台加载对象
}

- (void)interstitialAdapterLoadFail:(BAGAdObject *)ad withError:(BAGError *)error
{
   //平台加载失败
}

- (void)interstitialLoadAllFinish
{
    //所有平台加载结束
}
```

### 五、激励视频

#### 1. 创建对象

```objc
#import "BAGRewardedVideo.h"
//传入配置
self.rewardedVideo = [[BAGRewardedVideo alloc] initWithInfoData:data delegate:self];
self.rewardedVideo.revenueDelegate = self;
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
        },
        {
            "platform_id":2,//平台ID 可自定义
            "platform_name":"Admob",
            "className":"BAGAdmobAdapter",
            "unit_id":"ca-app-pub-3940256099942544/1712485313",//Admob后台的 unitid
            "ad_type":4,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4,RewardedInterstitial = 5
        },
    ]
}
```

#### 2. 加载广告

```objc
[self.rewardedVideo loadAd];
```

#### 3. 展示广告

```objc
if([self.rewardedVideo isReady])
{
    //设置自定义参数 可选
    self.rewardedVideo.customRewardString = @"TestCustomRewardString";
    [self.rewardedVideo showAd:self];
}
```

#### 4. 回调

```objc
#pragma mark - BAGPayRevenueDelegate

- (void)adPayRevenue:(BAGAdObject *)ad
{
    //收益回调
}

#pragma mark - BAGRewardedVideoDelegate
- (void)rewardedVideoDidLoad:(nonnull BAGAdObject *)ad
{
    //加载成功
}

- (void)rewardedVideoLoadFailWithError:(nonnull NSError *)error
{
   //所有平台加载失败
}

- (void)rewardedVideoDidShow:(nonnull BAGAdObject *)ad
{
   //广告展示
}

- (void)rewardedVideoShowFail:(nullable BAGAdObject *)ad withError:(nonnull BAGError *)error
{
   //广告展示失败
}

- (void)rewardedVideoDidClick:(nonnull BAGAdObject *)ad
{
    //广告点击
}

- (void)rewardedVideoDidDismiss:(nonnull BAGAdObject *)ad 
{
   //广告关闭
}

- (void)rewardedVideoDidEarnReward:(nonnull BAGAdObject *)ad rewardInfo:(nonnull NSDictionary *)rewardInfo
{
    //奖励回调
}

//可选

- (void)rewardedVideoWillStartLoad:(BAGAdObject *)ad loadObject:(nullable id)loadObject
{
    //平台开始加载，loadObject返回各平台加载对象
}

- (void)rewardedVideoAdapterLoadFail:(BAGAdObject *)ad withError:(BAGError *)error
{
   //平台加载失败
}

- (void)rewardedVideoLoadAllFinish
{
    //所有平台加载结束
}
```

### 六、横幅广告

#### 1. 创建对象

```objc
#import "BAGBanner.h"
//传入配置
self.banner = [[BAGBanner alloc] initWithInfoData:data delegate:self];
self.banner.revenueDelegate = self;
self.banner.frame = CGRectMake(0, 0, 320, 50);
self.banner.rootViewController = self;
[self.adView addSubview:self.banner];
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
        },
        {
            "platform_id":2,//平台ID 可自定义
            "platform_name":"Admob",
            "className":"BAGAdmobAdapter",
            "unit_id":"ca-app-pub-3940256099942544/2934735716",//Admob后台的 unitid
            "banner_format":0,//横幅类型 0=320*50，1=320*100，2=300*250
            "banner_width":320,//设置Admob Banner宽
            "banner_height":50,//设置Admob Banner高
            "ad_type":1,//广告类型 （仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        }
    ]
}
```

#### 2. 加载广告

默认加载成功后自动展示

```objc
[self.banner loadAd];
```

#### 3. 回调

```objc
#pragma mark - BAGPayRevenueDelegate

- (void)adPayRevenue:(BAGAdObject *)ad
{
    //收益回调
}

#pragma mark - BAGBannerDelegate
- (void)bannerDidLoad:(nonnull BAGAdObject *)ad
{
    //加载成功
}

- (void)bannerLoadFailWithError:(nonnull NSError *)error
{
   //所有平台加载失败
}

- (void)bannerDidShow:(nonnull BAGAdObject *)ad
{
   //广告展示
}

- (void)bannerShowFail:(nullable BAGAdObject *)ad withError:(nonnull BAGError *)error
{
   //广告展示失败
}

- (void)bannerDidClick:(nonnull BAGAdObject *)ad
{
    //广告点击
}

- (void)bannerDidClose:(nonnull BAGAdObject *)ad 
{
   //广告关闭
}

//可选

- (void)bannerWillStartLoad:(BAGAdObject *)ad loadObject:(nullable id)loadObject
{
    //平台开始加载
}

- (void)bannerAdapterLoadFail:(BAGAdObject *)ad withError:(BAGError *)error
{
   //平台加载失败
}

- (void)bannerLoadAllFinish
{
    //所有平台加载结束
}
```

#### 4. 其他

- 横幅默认加载成功后马上展示广告。如需要手动控制展示可以如下设置。

```objc

//关闭自动展示
self.banner.closeAutoShow = YES;

//在需要时手动调用showAd
[self.banner showAd];

```

- 停止MAX的横幅刷新

```objc
[self.banner stopAutoRefresh];
```

- 开始MAX的横幅刷新


```objc
[self.banner startAutoRefresh];
```

### 七、原生广告

#### 1. 创建对象

```objc
#import "BAGNative"
//传入配置
self.native = [[BAGNative alloc] initWithInfoData:data delegate:self];
self.native.revenueDelegate = self;
self.native.rootViewController = self;
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
        },
        {
            "platform_id":2,//平台ID 可自定义
            "platform_name":"Admob",
            "className":"BAGAdmobAdapter",
            "unit_id":"ca-app-pub-3940256099942544/3986624511",//Admob后台的 unitid
            "ad_type":3,//广告类型 （仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
            "adChoicesPosition":1,
            "requestMultipleImages":false,
            "mediaAspectRatio":0
        }
    ]
}
```

#### 2. 加载广告

```objc
[self.native loadAd];
```

#### 3. 展示广告

```objc
#import "BAGNativeView.h"
if(![self.native isReady]){
    return;
}
//新建一个BAGNativeView
self.nativeView = [[BAGNativeView alloc] init];
self.nativeView.frame = self.adView.bounds;
//绑定自定义AdView的各元素
self.nativeView.adView = self.nativeTemplate;
self.nativeView.mainImageView = self.nativeTemplate.mainImageView;
self.nativeView.iconImageView = self.nativeTemplate.iconImageView;
self.nativeView.adChoiceImageView =  self.nativeTemplate.adChoiceImageView;
self.nativeView.titleLabel = self.nativeTemplate.titleLabel;
self.nativeView.textLabel = self.nativeTemplate.textLabel;
self.nativeView.ctaButton = self.nativeTemplate.ctaButton;
//设置可点击元素 可选
//self.nativeView.clickArray = @[self.nativeTemplate.textLabel,self.nativeTemplate.ctaButton];
[self.adView addSubview:self.nativeView];
//调用渲染方法
[self.native renderWithNativeView:self.nativeView];
```

#### 4. 回调

```objc
#pragma mark - BAGPayRevenueDelegate

- (void)adPayRevenue:(BAGAdObject *)ad
{
    //收益回调
}

#pragma mark - BAGNativeDelegate
- (void)nativeDidLoad:(nonnull BAGAdObject *)ad
{
    //加载成功
}

- (void)nativeLoadFailWithError:(nonnull NSError *)error
{
   //所有平台加载失败
}

- (void)nativeDidShow:(nonnull BAGAdObject *)ad
{
   //广告展示
}

- (void)nativeShowFail:(nullable BAGAdObject *)ad withError:(nonnull BAGError *)error
{
   //广告展示失败
}

- (void)nativeDidClick:(nonnull BAGAdObject *)ad
{
    //广告点击
}

//可选

- (void)nativeWillStartLoad:(BAGAdObject *)ad loadObject:(nullable id)loadObject
{
    //平台开始加载，loadObject返回各平台加载对象
}

- (void)nativeAdapterLoadFail:(BAGAdObject *)ad withError:(BAGError *)error
{
   //平台加载失败
}

- (void)nativeLoadAllFinish
{
    //所有平台加载结束
}
```

### 八、其他

#### 1. 设置 DoNotSell

```objc
#import "BidAggregatorSDK.h"
//用户未选择退出
[[BidAggregatorSDK sharedInstance] setDoNotSell:NO];

//用户选择退出
[[BidAggregatorSDK sharedInstance] setDoNotSell:YES];
```


#### 2. 设置 COPPA

```objc
#import "BidAggregatorSDK.h"
//用户非儿童
[[BidAggregatorSDK sharedInstance] setCOPPAStatus:NO];

//用户是儿童
[[BidAggregatorSDK sharedInstance] setCOPPAStatus:YES];
```


#### 3. 设置UserID

```objc
#import "BidAggregatorSDK.h"

[[BidAggregatorSDK sharedInstance] setUserID:@"YOUR_USER_ID"];
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