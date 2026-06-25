# Unity - 接入指南

支持的聚合及平台：Carty，Max 
支持广告类型：激励视频，插屏，开屏, 横幅

流程说明：

- 调用BAGSDK加载时，SDK会根据本地配置同时向对多个聚合或广告平台发起加载请求。

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/whiteboard_exported_image.png)

- 调用BAGSDK展示时，SDK会自动对已配置的聚合或广告平台进行本地比价并展示最高价格的广告。

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/whiteboard_exported_image-2.png)


### 一、集成SDK

SDK下载地址：https://github.com/BIDADG/BAGSDK-Unity

- 将 BAGSDK.unitypackage 导入项目

- 根据需要选择导入聚合平台的 Adapter：

	- MAX ：BAGMaxAdapter.unitypackage
	
	- Admob ：BAGAdmobAdapter.unitypackage

- 集成相关聚合的SDK 或 Unity Plugin

### 二、初始化

```C#
using BAG.Api;
```

```C#
//传入配置
BAGSDK.Instance().InitSDK(infoData);
```

- 配置格式及说明

```json
{
     "platforms":[
         {
             "platform_id":0,//平台ID 可自定义
             "platform_name":"Carty",
             "className":"AdapterClassName",//Adapter类名
             "app_id":"a203273261891579" //Carty平台的appid
             
         },
         {
             "platform_id":1,//平台ID 可自定义
             "platform_name":"Max",
             "className":"AdapterClassName",//Adapter类名
             "sdk_key":"05TMDQ5tZabpXQ45_UTbmEGNUtVAzSTzT6KmWQc5_CuWdzccS4DCITZoL3yIWUG3bbq60QC_d4WF28tUC4gVTF",//MAX 平台的SDKkey
             "testDevice":true,//是否开启MAX测试模式 开启时会iOS会自动将设备的IDFV添加到MAX的测试设备列表中
             "test_ids": []//测试设备列表
         },
         {
             "platform_id":2,//平台ID 可自定义
             "platform_name":"Admob",
             "className":"AdapterClassName",//Adapter类名
             "test_ids": []//测试设备列表
         }
     ]
 }
```

-   回调

```C#
void OnAllPlatformFinish(Dictionary<string, object> error)
{
    //所有平台初始化结束
}

void OnPlatformInitFinish(string platformID, string platformName, Dictionary<string, object> error)
{
    //平台初始化结束 仅iOS
}

private void OnEnable()
{
    BAGSDK.Instance().OnAllPlatformFinish += OnAllPlatformFinish;
    BAGSDK.Instance().OnPlatformInitFinish += OnPlatformInitFinish;
}

private void OnDestroy()
{
    BAGSDK.Instance().OnAllPlatformFinish -= OnAllPlatformFinish;
    BAGSDK.Instance().OnPlatformInitFinish -= OnPlatformInitFinish;
}
```

### 三、开屏广告

#### 1.  创建对象

```C#
//传入配置 
//customName 为自定义名称
BAGAppOpen.Instance().CreateAppOpen(customName, infoData);
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
            "className":"AdapterClassName",//Adapter类名
            "placement_id":"281217152630",//Carty后台的 placementid
            "ad_type":0,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":1,//平台ID 可自定义
            "platform_name":"Max",
            "className":"AdapterClassName",//Adapter类名
            "unit_id":"YOUR_AD_UNIT_ID",//MAX后台的 unitid
            "placement":"",
            "ad_type":0,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":2,//平台ID 可自定义
            "platform_name":"Admob",
            "className":"AdapterClassName",//Adapter类名
            "unit_id":"ca-app-pub-3940256099942544/5575463023",//Admob后台的 unitid
            "ad_type":0,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
    ]
}
```

#### 2. 加载广告

```C#
BAGAppOpen.Instance().LoadAppOpen(customName);
```

#### 3. 展示广告

```C#
if(BAGAppOpen.Instance().AppOpenReady(customName))
{
    BAGAppOpen.Instance().ShowAppOpen(customName);
}
```

#### 4. 回调

```C#
void OnAppOpenDidLoad(string customName, Dictionary<string, object> adInfo)
{
    //加载成功   
}

void OnAppOpenLoadFail(string customName, Dictionary<string, object> error)
{
    //所有平台加载失败
}

void OnAppOpenDidShow(string customName, Dictionary<string, object> adInfo)
{
    //广告展示
}

void OnAppOpenShowFail(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> error)
{
    //广告展示失败
}

void OnAppOpenDidClick(string customName, Dictionary<string, object> adInfo)
{
    //广告点击
}

void OnAppOpenDidDismiss(string customName, Dictionary<string, object> adInfo)
{
    //广告关闭
}

void OnAdPayRevenue(string customName, Dictionary<string, object> adInfo)
{
    //收益回调
}


void OnAppOpenAdapterLoadFail(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> error)
{
    //平台加载失败
}

void OnAppOpenLoadAllFinish(string customName)
{
    //所有平台加载结束
}
    
private void OnEnable()
{
    BAGAppOpen.Instance().OnAppOpenDidLoad += OnAppOpenDidLoad;
    BAGAppOpen.Instance().OnAppOpenLoadFail += OnAppOpenLoadFail;
    BAGAppOpen.Instance().OnAppOpenDidShow += OnAppOpenDidShow;
    BAGAppOpen.Instance().OnAppOpenShowFail += OnAppOpenShowFail;
    BAGAppOpen.Instance().OnAppOpenDidClick += OnAppOpenDidClick;
    BAGAppOpen.Instance().OnAppOpenDidDismiss += OnAppOpenDidDismiss;
    BAGAppOpen.Instance().OnAdPayRevenue += OnAdPayRevenue;
    BAGAppOpen.Instance().OnAppOpenAdapterLoadFail += OnAppOpenAdapterLoadFail;
    BAGAppOpen.Instance().OnAppOpenLoadAllFinish += OnAppOpenLoadAllFinish;
}

private void OnDestroy()
{
    BAGAppOpen.Instance().OnAppOpenDidLoad -= OnAppOpenDidLoad;
    BAGAppOpen.Instance().OnAppOpenLoadFail -= OnAppOpenLoadFail;
    BAGAppOpen.Instance().OnAppOpenDidShow -= OnAppOpenDidShow;
    BAGAppOpen.Instance().OnAppOpenShowFail -= OnAppOpenShowFail;
    BAGAppOpen.Instance().OnAppOpenDidClick -= OnAppOpenDidClick;
    BAGAppOpen.Instance().OnAppOpenDidDismiss -= OnAppOpenDidDismiss;
    BAGAppOpen.Instance().OnAdPayRevenue -= OnAdPayRevenue;
    BAGAppOpen.Instance().OnAppOpenAdapterLoadFail -= OnAppOpenAdapterLoadFail;
    BAGAppOpen.Instance().OnAppOpenLoadAllFinish -= OnAppOpenLoadAllFinish;
}
```

### 四、插屏广告

#### 1. 创建对象

```C#
//传入配置 
//customName 为自定义名称
BAGInterstitial.Instance().CreateInterstitial(customName, infoData);
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
            "className":"AdapterClassName",//Adapter类名
            "placement_id":"281217152620",//Carty后台的 placementid
            "ad_type":2,//广告类型（仅iOS使用）
           //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":1,//平台ID 可自定义
            "platform_name":"Max",
            "className":"AdapterClassName",//Adapter类名
            "unit_id":"YOUR_AD_UNIT_ID",//MAX后台的 unitid
            "placement":"",
            "ad_type":2,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":2,//平台ID 可自定义
            "platform_name":"Admob",
            "className":"AdapterClassName",//Adapter类名
            "unit_id":"ca-app-pub-3940256099942544/4411468910",//Admob后台的 unitid
            "ad_type":2,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        }
    ]
}
```

#### 2. 加载广告

```C#
BAGInterstitial.Instance().LoadInterstitial(customName);
```

#### 3. 展示广告

```C#
if(BAGInterstitial.Instance().InterstitialReady(customName))
{
    BAGInterstitial.Instance().ShowInterstitial(customName);
}
```

#### 4. 回调

```C#
void OnInterstitialDidLoad(string customName, Dictionary<string, object> adInfo)
{
    //加载成功
}

void OnInterstitialLoadFail(string customName, Dictionary<string, object> error)
{
    //所有平台加载失败
}

void OnInterstitialDidShow(string customName, Dictionary<string, object> adInfo)
{
    //广告展示
}

void OnInterstitialShowFail(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> error)
{
    //广告展示失败
}

void OnInterstitialDidClick(string customName, Dictionary<string, object> adInfo)
{
    //广告点击
}

void OnInterstitialDidDismiss(string customName, Dictionary<string, object> adInfo)
{
    //广告关闭
}

void OnAdPayRevenue(string customName, Dictionary<string, object> adInfo)
{
   //收益回调
}

void OnInterstitialAdapterLoadFail(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> error)
{
    //平台加载失败
}

void OnInterstitialLoadAllFinish(string customName)
{
   //所有平台加载结束
}
    
private void OnEnable()
{
    BAGInterstitial.Instance().OnInterstitialDidLoad += OnInterstitialDidLoad;
    BAGInterstitial.Instance().OnInterstitialLoadFail += OnInterstitialLoadFail;
    BAGInterstitial.Instance().OnInterstitialDidShow += OnInterstitialDidShow;
    BAGInterstitial.Instance().OnInterstitialShowFail += OnInterstitialShowFail;
    BAGInterstitial.Instance().OnInterstitialDidClick += OnInterstitialDidClick;
    BAGInterstitial.Instance().OnInterstitialDidDismiss += OnInterstitialDidDismiss;
    BAGInterstitial.Instance().OnAdPayRevenue += OnAdPayRevenue;
    //可选
    BAGInterstitial.Instance().OnInterstitialAdapterLoadFail += OnInterstitialAdapterLoadFail;
    BAGInterstitial.Instance().OnInterstitialLoadAllFinish += OnInterstitialLoadAllFinish;
}

private void OnDestroy()
{
    BAGInterstitial.Instance().OnInterstitialDidLoad -= OnInterstitialDidLoad;
    BAGInterstitial.Instance().OnInterstitialLoadFail -= OnInterstitialLoadFail;
    BAGInterstitial.Instance().OnInterstitialDidShow -= OnInterstitialDidShow;
    BAGInterstitial.Instance().OnInterstitialShowFail -= OnInterstitialShowFail;
    BAGInterstitial.Instance().OnInterstitialDidClick -= OnInterstitialDidClick;
    BAGInterstitial.Instance().OnInterstitialDidDismiss -= OnInterstitialDidDismiss;
    BAGInterstitial.Instance().OnAdPayRevenue -= OnAdPayRevenue;
    BAGInterstitial.Instance().OnInterstitialAdapterLoadFail -= OnInterstitialAdapterLoadFail;
    BAGInterstitial.Instance().OnInterstitialLoadAllFinish -= OnInterstitialLoadAllFinish;
}
```

### 五、激励视频

#### 1. 创建对象

```C#
//传入配置 
//customName 为自定义名称
BAGRewardVideo.Instance().CreateRewardVideo(customName, infoData);
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
            "className":"AdapterClassName",//Adapter类名
            "placement_id":"281217152650",//Carty后台的 placementid
            "ad_type":4,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":1,//平台ID 可自定义
            "platform_name":"Max",
            "className":"AdapterClassName",//Adapter类名
            "unit_id":"YOUR_AD_UNIT_ID",//MAX后台的 unitid
            "placement":"",
            "ad_type":4,//广告类型 （仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4
        },
        {
            "platform_id":2,//平台ID 可自定义
            "platform_name":"Admob",
            "className":"AdapterClassName",//Adapter类名
            "unit_id":"ca-app-pub-3940256099942544/1712485313",//Admob后台的 unitid
            "ad_type":4,//广告类型（仅iOS使用）
            //AppOpen = 0，Banner = 1,Interstitial = 2,Native = 3,RewardedVideo = 4,RewardedInterstitial = 5
            "rewarded_interstitial":false // 是否是admob插页式激励广告（仅Android使用）
        },
    ]
}
```

#### 2. 加载广告

```C#
BAGRewardVideo.Instance().LoadRewardVideo(customName);
```

#### 3. 展示广告

```C#
if(BAGRewardVideo.Instance().RewardVideoReady(customName))
{
    BAGRewardVideo.Instance().ShowRewardVideo(customName,"customRewardString");
}
```

#### 4. 回调

```C#
void OnRewardVideoDidLoad(string customName, Dictionary<string, object> adInfo)
{
    //加载成功
}

void OnRewardVideoLoadFail(string customName, Dictionary<string, object> error)
{
    //所有平台加载失败
}

void OnRewardVideoDidShow(string customName, Dictionary<string, object> adInfo)
{
    //广告展示
}

void OnRewardVideoShowFail(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> error)
{
    //广告展示失败
}

void OnRewardVideoDidClick(string customName, Dictionary<string, object> adInfo)
{
    //广告点击
}

void OnRewardVideoDidDismiss(string customName, Dictionary<string, object> adInfo)
{
     //广告关闭
}

void OnRewardVideoDidRewarded(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> rewardInfo)
{
    //奖励回调
}

void OnAdPayRevenue(string customName, Dictionary<string, object> adInfo)
{
    //收益回调
}

void OnRewardVideoAdapterLoadFail(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> error)
{
   //平台加载失败
}

void OnRewardVideoLoadAllFinish(string customName)
{
    //所有平台加载结束
}

private void OnEnable()
{
    BAGRewardVideo.Instance().OnRewardVideoDidLoad += OnRewardVideoDidLoad;
    BAGRewardVideo.Instance().OnRewardVideoLoadFail += OnRewardVideoLoadFail;
    BAGRewardVideo.Instance().OnRewardVideoDidShow += OnRewardVideoDidShow;
    BAGRewardVideo.Instance().OnRewardVideoShowFail += OnRewardVideoShowFail;
    BAGRewardVideo.Instance().OnRewardVideoDidClick += OnRewardVideoDidClick;
    BAGRewardVideo.Instance().OnRewardVideoDidDismiss += OnRewardVideoDidDismiss;
    BAGRewardVideo.Instance().OnAdPayRevenue += OnAdPayRevenue;
    BAGRewardVideo.Instance().OnRewardVideoDidRewarded += OnRewardVideoDidRewarded;
    //可选
    BAGRewardVideo.Instance().OnRewardVideoAdapterLoadFail += OnRewardVideoAdapterLoadFail;
    BAGRewardVideo.Instance().OnRewardVideoLoadAllFinish += OnRewardVideoLoadAllFinish;
}

private void OnDestroy()
{
    BAGRewardVideo.Instance().OnRewardVideoDidLoad -= OnRewardVideoDidLoad;
    BAGRewardVideo.Instance().OnRewardVideoLoadFail -= OnRewardVideoLoadFail;
    BAGRewardVideo.Instance().OnRewardVideoDidShow -= OnRewardVideoDidShow;
    BAGRewardVideo.Instance().OnRewardVideoShowFail -= OnRewardVideoShowFail;
    BAGRewardVideo.Instance().OnRewardVideoDidClick -= OnRewardVideoDidClick;
    BAGRewardVideo.Instance().OnRewardVideoDidDismiss -= OnRewardVideoDidDismiss;
    BAGRewardVideo.Instance().OnAdPayRevenue -= OnAdPayRevenue;
    BAGRewardVideo.Instance().OnRewardVideoDidRewarded -= OnRewardVideoDidRewarded;
    BAGRewardVideo.Instance().OnRewardVideoAdapterLoadFail -= OnRewardVideoAdapterLoadFail;
    BAGRewardVideo.Instance().OnRewardVideoLoadAllFinish -= OnRewardVideoLoadAllFinish;
}
```

### 六、横幅广告

#### 1. 创建对象

```C#
//传入配置 
//customName 为自定义名称
var config = new BAGBannerConfiguration(BAGBannerPosition.BottomCenter);
BAGBanner.Instance().CreateBanner(customName, infoData, config);
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
            "className":"AdapterClassName",//Adapter类名
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
            "className":"AdapterClassName",//Adapter类名
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
            "className":"AdapterClassName",//Adapter类名
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

```C#
BAGBanner.Instance().LoadBanner(customName);
```

#### 3. 展示广告

```C#
BAGBanner.Instance().ShowBanner(customName);
```

#### 4. 隐藏广告

```C#
BAGBanner.Instance().HideBanner(customName);
```

#### 5. 销毁广告

```C#
BAGBanner.Instance().DestroyBanner(customName);
```

#### 6. 停止MAX横幅刷新

```C#
BAGBanner.Instance().StopBannerRefresh(customName);
```

#### 7. 开始MAX横幅刷新

```C#
BAGBanner.Instance().StartBannerRefresh(customName);
```

#### 8. 回调

```C#
void OnBannerDidLoad(string customName, Dictionary<string, object> adInfo)
{
    //加载成功
}

void OnBannerLoadFail(string customName, Dictionary<string, object> error)
{
    //所有平台加载失败
}

void OnBannerDidShow(string customName, Dictionary<string, object> adInfo)
{
    //广告展示
}

void OnBannerShowFail(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> error)
{
    //广告展示失败
}

void OnBannerDidClick(string customName, Dictionary<string, object> adInfo)
{
    //广告点击
}

void OnBannerDidClose(string customName, Dictionary<string, object> adInfo)
{
    //广告关闭
}

void OnAdPayRevenue(string customName, Dictionary<string, object> adInfo)
{
   //收益回调
}

void OnBannerAdapterLoadFail(string customName, Dictionary<string, object> adInfo, Dictionary<string, object> error)
{
    //平台加载失败
}

void OnBannerLoadAllFinish(string customName)
{
     //所有平台加载结束
}

private void OnEnable()
{
    BAGBanner.Instance().OnBannerDidLoad += OnBannerDidLoad;
    BAGBanner.Instance().OnBannerLoadFail += OnBannerLoadFail;
    BAGBanner.Instance().OnBannerDidShow += OnBannerDidShow;
    BAGBanner.Instance().OnBannerShowFail += OnBannerShowFail;
    BAGBanner.Instance().OnBannerDidClick += OnBannerDidClick;
    BAGBanner.Instance().OnBannerDidClose += OnBannerDidClose;
    BAGBanner.Instance().OnAdPayRevenue += OnAdPayRevenue;
    //可选
    BAGBanner.Instance().OnBannerAdapterLoadFail += OnBannerAdapterLoadFail;
    BAGBanner.Instance().OnBannerLoadAllFinish += OnBannerLoadAllFinish;
}

private void OnDestroy()
{
    BAGBanner.Instance().OnBannerDidLoad -= OnBannerDidLoad;
    BAGBanner.Instance().OnBannerLoadFail -= OnBannerLoadFail;
    BAGBanner.Instance().OnBannerDidShow -= OnBannerDidShow;
    BAGBanner.Instance().OnBannerShowFail -= OnBannerShowFail;
    BAGBanner.Instance().OnBannerDidClick -= OnBannerDidClick;
    BAGBanner.Instance().OnBannerDidClose -= OnBannerDidClose;
    BAGBanner.Instance().OnAdPayRevenue -= OnAdPayRevenue;
    BAGBanner.Instance().OnBannerAdapterLoadFail -= OnBannerAdapterLoadFail;
    BAGBanner.Instance().OnBannerLoadAllFinish -= OnBannerLoadAllFinish;
}
```


### 七、其他

#### 1. 各广告类型配置说明
- iOS 需按下表设置 className & ad_type 两个参数

|平台和类型| className | ad_type|备注|
|---|---|---|---|
|Carty 初始化| BAGCartyAdapter | 无需设置||
|Carty 开屏| BAGCartyAdapter | 0||
|Carty 插屏| BAGCartyAdapter | 2||
|Carty 激励视频| BAGCartyAdapter|4||
|Carty 横幅| BAGCartyAdapter |1||
||||
|MAX 初始化| BAGMaxAdapter | 无需设置||
|MAX 开屏| BAGMaxAdapter |0||
|MAX 插屏| BAGMaxAdapter |2||
|MAX 激励视频| BAGMaxAdapter |4||
|MAX 横幅| BAGMaxAdapter |1||
||||
|Admob 初始化| BAGAdmobAdapter | 无需设置||
|Admob 开屏| BAGAdmobAdapter |0||
|Admob 插屏| BAGAdmobAdapter |2||
|Admob 激励视频| BAGAdmobAdapter |4||
|Admob 横幅| BAGAdmobAdapter |1||
|Admob 插页式激励| BAGAdmobAdapter |5|可配置在激励视频|


- Android 需按下表设置 className

|平台和类型| className |备注|
|---|---|---|
|Carty 初始化| com.bagsdk.mediation.carty.CartyAppOpenAdapter ||
|Carty 开屏| com.bagsdk.mediation.carty.CartyAppOpenAdapter ||
|Carty 插屏| com.bagsdk.mediation.carty.CartyInterstitialAdapter||
|Carty 激励视频| com.bagsdk.mediation.carty.CartyRewardedAdapter||
|Carty 横幅| com.bagsdk.mediation.carty.CartyBannerAdapter||
|||
|MAX 初始化| com.bagsdk.mediation.max.MaxAppOpenAdapter ||
|MAX 开屏| com.bagsdk.mediation.max.MaxAppOpenAdapter||
|MAX 插屏| com.bagsdk.mediation.max.MaxInterstitialAdapter||
|MAX 激励视频| com.bagsdk.mediation.max.MaxRewardedAdapter||
|MAX 横幅| com.bagsdk.mediation.max.MaxBannerAdapter||
|||
| Admob 初始化| com.bagsdk.mediation.admob.AdMobAppOpenAdapter ||
| Admob 开屏| com.bagsdk.mediation.admob.AdMobAppOpenAdapter ||
| Admob 插屏| com.bagsdk.mediation.admob.AdMobInterstitialAdapter ||
| Admob 激励视频| com.bagsdk.mediation.admob.AdMobRewardedAdapter ||
| Admob 插页式激励|com.bagsdk.mediation.admob.AdMobRewardedAdapter | rewarded_interstitial = true|
| Admob 横幅| com.bagsdk.mediation.admob.AdMobBannerAdapter ||

#### 2. 设置 DoNotSell

```C#
BAGSDK.Instance().SetDoNotSell(bool);
```


#### 3. 设置 COPPA

```C#
BAGSDK.Instance().SetCOPPAStatus(bool);
```


#### 4. 设置UserID

```C#
BAGSDK.Instance().SetUserID("YOUR_USER_ID");
```

### 八、常见问题
#### （iOS）ISO C99 and later do not support implicit function declarations
iOS导出Xcode项目后编译时报错

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/ios_error1.png)

解决方法如下：
在UnitFramework 的 Build Settings 中 找到 C Language Dialect 选择 GNU17 

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/ios_error_set1.png)
