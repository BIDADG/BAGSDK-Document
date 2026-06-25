# Android - 接入指南

支持的聚合及平台：Carty，Max
支持广告类型：横幅，激励视频，插屏，开屏，原生

流程说明：

- 调用BAGSDK加载时，SDK会根据本地配置同时向对多个聚合或广告平台发起加载请求。

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/whiteboard_exported_image.png)

- 调用BAGSDK展示时，SDK会自动对已配置的聚合或广告平台进行本地比价并展示最高价格的广告。

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/whiteboard_exported_image-2.png)

### 一、集成SDK

SDK下载地址：https://github.com/BIDADG/BAGSDK-Android

- 将adapter和bagsdk文件夹添加到项目中

![](https://github.com/BIDADG/BAGSDK-Document/blob/main/Image/androidProject.png)

- setting.gradle

```groovy
include ':bagsdk'
include ':adapter:carty'
include ':adapter:max'
include ':adapter:admob'
```

- app下build.gradle添加依赖

```groovy
dependencies {
    //.....app其他依赖
    
    implementation project(":bagsdk")
    implementation project(":adapter:carty")
    implementation project(":adapter:max")
    implementation project(":adapter:admob")
}
```

- AndroidManifest.xml

```xml
<application>
    
    <!-- admob APPLICATION_ID， 若未接入admob  无需配置 -->
    <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="ca-app-pub-xxxx" />

</application>
```

### 二、初始化

```Kotlin
BAGSdk.setInitMediationStrategy(strategy)
BAGSdk.init(application, object : BAGInitListener {
    override fun onInitFinish(result: BAGInitResult?) {
        showToast("onInitFinish")
        Log.e(
            "onInitFinish",
            "successMediationIdList:${result?.successMediationIdList} failedMediationList:${result?.failedMediationList}"
        )
    }
})
```

- 配置格式及说明

```json
{
  "platforms": [
    {
      "platform_id": 0,
      //平台ID 可自定义
      "platform_name": "Carty",
      // 平台名称
      "className": "com.bagsdk.mediation.carty.CartyAppOpenAdapter",
      // 适配器包路径
      "app_id": "a104796136113885"
      //Carty平台的appid
    },
    {
      "platform_id": 1,
      //平台ID 可自定义
      "platform_name": "Max",
      // 平台名称
      "className": "com.bagsdk.mediation.max.MaxAppOpenAdapter",
      // 适配器包路径
      "sdk_key": "05TMDQ5tZabpXQ45_UTbmEGNUtVAzSTzT6KmWQc5_CuWdzccS4DCITZoL3yIWUG3bbq60QC_d4WF28tUC4gVTF",
      //MAX 平台的SDKkey
      "testDevice": true,
      //是否开启MAX测试模式
      "test_ids": [
        //测试设备gaid列表
      ]
    },
    {
      "platform_id": 2,
      //平台ID 可自定义
      "platform_name": "AdMob",
      // 平台名称
      "className": "com.bagsdk.mediation.admob.AdMobAppOpenAdapter"
      // 适配器包路径
    }
  ]
}
```

### 三、开屏广告

#### 1. 创建对象

```Kotlin
var appOpenAd: BAGAppOpenAd? = null

appOpenAd = BAGAppOpenAd(context, strategy).apply {
    setAdListener(object : BAGAppOpenAdListener {
        override fun onAdLoaded(bagAd: BAGAd?) {
            // 广告加载成功
        }

        override fun onAdLoadFailed(adError: BAGError?) {
            // 所有广告加载失败
        }

        override fun onMediationLoadFailed(mediationError: BAGMediationError?) {
            // 各平台加载失败
        }

        override fun onAdClosed(bagAd: BAGAd?) {
            // 广告关闭
        }

        override fun onAdShow(bagAd: BAGAd?) {
            // 广告展示
        }

        override fun onAdShowFailed(bagAd: BAGAd?, adError: BAGError?) {
            // 广告展示失败
        }

        override fun onAdClick(bagAd: BAGAd?) {
            // 广告点击
        }
    })

    setAdRevenueListener(object : BAGAdRevenueListener {
        override fun onAdRevenuePaid(bagAd: BAGAd?) {
            // 广告收益
        }
    })

    // 可选
    setAdExListener(object : BAGAdExListener {
        override fun beforeLoadAd(mediationAdObject: Any?) {
            // 各平台广告请求对象
        }
    })
}
```

- 配置格式及说明

```json
{
  "name": "bagAppOpen",
  //广告位名称 可自定义
  "maxWaitTime": 5000,
  //单位：毫秒，最长加载回调等待时长 例：设置为5000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调
  "platforms": [
    {
      "platform_id": 0,
      // 平台id 可自定义
      "platform_name": "Carty",
      // 平台名称
      "className": "com.bagsdk.mediation.carty.CartyAppOpenAdapter",
      // 适配器包路径
      "placement_id": "281217152630"
      // 广告位id
    },
    {
      "platform_id": 1,
      // 平台id 可自定义
      "platform_name": "Max",
      // 平台名称
      "className": "com.bagsdk.mediation.max.MaxAppOpenAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      //MAX后台的 unitid
      "placement": "maxAppOpen"
    },
    {
      "platform_id": 2,
      // 平台id 可自定义
      "platform_name": "AdMob",
      // 平台名称
      "className": "com.bagsdk.mediation.admob.AdMobAppOpenAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      // admob后台的 unitid
    }
  ]
}
```

#### 2. 加载广告

```Kotlin
appOpenAd?.loadAd()
```

#### 2. ready

```Kotlin
appOpenAd?.isReady
```

#### 3. 展示广告

```Kotlin
appOpenAd?.showAd(activity, container)
```

### 四、插屏广告

#### 1. 创建对象

```Kotlin
var interstitialAd: BAGInterstitialAd? = null

interstitialAd = BAGInterstitialAd(context, strategy).apply {
    setAdListener(object : BAGInterstitialAdListener {
        override fun onAdLoaded(bagAd: BAGAd?) {
            // 广告加载成功
        }

        override fun onAdLoadFailed(adError: BAGError?) {
            // 所有广告加载失败
        }

        override fun onMediationLoadFailed(mediationError: BAGMediationError?) {
            // 各平台加载失败
        }

        override fun onAdClosed(bagAd: BAGAd?) {
            // 广告关闭
        }

        override fun onAdShow(bagAd: BAGAd?) {
            // 广告展示
        }

        override fun onAdShowFailed(bagAd: BAGAd?, adError: BAGError?) {
            // 广告展示失败
        }

        override fun onAdClick(bagAd: BAGAd?) {
            // 广告点击
        }
    })

    setAdRevenueListener(object : BAGAdRevenueListener {
        override fun onAdRevenuePaid(bagAd: BAGAd?) {
            // 广告收益
        }
    })

    // 可选
    setAdExListener(object : BAGAdExListener {
        override fun beforeLoadAd(mediationAdObject: Any?) {
            // 各平台广告请求对象
        }
    })
}
```

- 配置格式及说明

```json
{
  "name": "bagInterstitial",
  //广告位名称 可自定义
  "maxWaitTime": 5000,
  //单位：毫秒，最长加载回调等待时长 例：设置为5000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调
  "platforms": [
    {
      "platform_id": 0,
      // 平台id 可自定义
      "platform_name": "Carty",
      // 平台名称
      "className": "com.bagsdk.mediation.carty.CartyInterstitialAdapter",
      // 适配器包路径
      "placement_id": "281217152620"
      // Carty后台广告位id
    },
    {
      "platform_id": 1,
      // 平台id 可自定义
      "platform_name": "Max",
      // 平台名称
      "className": "com.bagsdk.mediation.max.MaxInterstitialAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      //MAX后台的 unitid
      "placement": "maxInterstitial"
    },
    {
      "platform_id": 2,
      // 平台id 可自定义
      "platform_name": "AdMob",
      // 平台名称
      "className": "com.bagsdk.mediation.admob.AdMobInterstitialAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      // admob后台的 unitid
    }
  ]
}
```

#### 2. 加载广告

```Kotlin
interstitialAd?.loadAd()
```

#### 3. ready

```Kotlin
interstitialAd?.isReady
```

#### 4. 展示广告

```Kotlin
interstitialAd?.showAd(activity)
```

### 五、激励视频

#### 1. 创建对象

```Kotlin
var rewardedAd: BAGRewardedAd? = null

rewardedAd = BAGRewardedAd(context, strategy).apply {
    setAdListener(object : BAGRewardedAdListener {
        override fun onAdLoaded(bagAd: BAGAd?) {
            // 广告加载成功
        }

        override fun onAdLoadFailed(adError: BAGError?) {
            // 所有广告加载失败
        }

        override fun onMediationLoadFailed(mediationError: BAGMediationError?) {
            // 各平台加载失败
        }

        override fun onAdClosed(bagAd: BAGAd?) {
            // 广告关闭
        }

        override fun onAdShow(bagAd: BAGAd?) {
            // 广告展示
        }

        override fun onAdShowFailed(bagAd: BAGAd?, adError: BAGError?) {
            // 广告展示失败
        }

        override fun onAdClick(bagAd: BAGAd?) {
            // 广告点击
        }

        override fun onAdRewarded(bagAd: BAGAd?, rewardMap: Map<String?, Any?>?) {
            // 广告奖励
        }

        override fun onRewardedVideoStarted(bagAd: BAGAd?) {
            // 视频开始播放
        }

        override fun onRewardedVideoCompleted(bagAd: BAGAd?) {
            // 视频播放完成
        }
    })

    setAdRevenueListener(object : BAGAdRevenueListener {
        override fun onAdRevenuePaid(bagAd: BAGAd?) {
            // 广告收益
        }
    })

    // 可选
    setAdExListener(object : BAGAdExListener {
        override fun beforeLoadAd(mediationAdObject: Any?) {
            // 各平台广告请求对象
        }
    })
}
```

- 配置格式及说明

```json
{
  "name": "bagRewarded",
  //广告位名称 可自定义
  "maxWaitTime": 5000,
  //单位：毫秒，最长加载回调等待时长 例：设置为5000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调
  "platforms": [
    {
      "platform_id": 0,
      // 平台id 可自定义
      "platform_name": "Carty",
      // 平台名称
      "className": "com.bagsdk.mediation.carty.CartyRewardedAdapter",
      // 适配器包路径
      "placement_id": "281217152650"
      // Carty后台广告位id
    },
    {
      "platform_id": 1,
      // 平台id 可自定义
      "platform_name": "Max",
      // 平台名称
      "className": "com.bagsdk.mediation.max.MaxRewardedAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      //MAX后台的 unitid
      "placement": "maxRewarded"
    },
    {
      "platform_id": 2,
      // 平台id 可自定义
      "platform_name": "AdMob",
      // 平台名称
      "className": "com.bagsdk.mediation.admob.AdMobRewardedAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      // admob后台的 unitid
      "rewarded_interstitial": true
      // 是否是admob插页式激励广告
    }
  ]
}
```

#### 2. 加载广告

```Kotlin
rewardedAd?.loadAd()
```

#### 3. ready

```Kotlin
rewardedAd?.isReady
```

#### 4. 展示广告

```Kotlin
rewardedAd?.showAd(activity)
```

### 六、横幅广告

#### 1. 创建对象

```Kotlin
var bannerView: BAGBannerAdView? = null

bannerView = BAGBannerAdView(context)
bannerView?.apply {
    setStrategy(strategy)
    val params = FrameLayout.LayoutParams(
        ViewGroup.LayoutParams.WRAP_CONTENT,
        ViewGroup.LayoutParams.WRAP_CONTENT
    )
    params.gravity = Gravity.CENTER

    setAdListener(object : BAGBannerAdListener {
        override fun onAdLoaded(bagAd: BAGAd?) {
            // 广告加载成功
        }

        override fun onAdLoadFailed(adError: BAGError?) {
            // 所有广告加载失败
        }

        override fun onMediationLoadFailed(mediationError: BAGMediationError?) {
            // 各平台加载失败
        }

        override fun onAdClosed(bagAd: BAGAd?) {
            // 广告关闭
        }

        override fun onAdShow(bagAd: BAGAd?) {
            // 广告展示
        }

        override fun onAdShowFailed(bagAd: BAGAd?, adError: BAGError?) {
            // 广告展示失败
        }

        override fun onAdClick(bagAd: BAGAd?) {
            // 广告点击
        }
    })

    setAdRevenueListener(object : BAGAdRevenueListener {
        override fun onAdRevenuePaid(bagAd: BAGAd?) {
            // 广告收益
        }
    })

    // 可选
    setAdExListener(object : BAGAdExListener {
        override fun beforeLoadAd(mediationAdObject: Any?) {
            // 各平台广告请求对象
        }
    })

    adContainer.addView(
        this,
        params
    )
}
```

- 配置格式及说明

```json
{
  "name": "bagBanner",
  //广告位名称 可自定义
  "maxWaitTime": 5000,
  //单位：毫秒，最长加载回调等待时长 例：设置为5000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调
  "platforms": [
    {
      "platform_id": 0,
      // 平台id 可自定义
      "platform_name": "Carty",
      // 平台名称
      "className": "com.bagsdk.mediation.carty.CartyBannerAdapter",
      // 适配器包路径
      "placement_id": "281217152640",
      // Carty后台广告位id
      "banner_format": 0
      //横幅类型 0=320*50，1=320*100，2=300*250
    },
    {
      "platform_id": 1,
      // 平台id 可自定义
      "platform_name": "Max",
      // 平台名称
      "className": "com.bagsdk.mediation.max.MaxBannerAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      //MAX后台的 unitid
      "placement": "maxBanner",
      "banner_format": 0,
      //横幅类型 0=320*50，1=320*100，2=300*250
      "banner_width": 320,
      //设置MAX Banner宽
      "banner_height": 50
      //设置MAX Banner高
    },
    {
      "platform_id": 2,
      // 平台id 可自定义
      "platform_name": "AdMob",
      // 平台名称
      "className": "com.bagsdk.mediation.admob.AdMobBannerAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      // admob后台的 unitid
      "banner_format": 0
      //横幅类型 0=320*50，1=320*100，2=300*250
    }
  ]
}
```

#### 2. 加载广告

默认加载成功后自动展示

```Kotlin
bannerView?.loadAd()
```

#### 3. 销毁

```Kotlin
bannerView?.destroy()
```

#### 4. 其他

- 横幅默认加载成功后马上展示广告。如需要手动控制展示可以如下设置。

```Kotlin

//关闭自动展示
bannerView?.setCloseAutoShow()

//在需要时手动调用showAd
bannerView?.showBanner()
```

- 停止MAX的横幅刷新

```Kotlin
bannerView?.stopAutoRefresh()
```

- 开始MAX的横幅刷新

```Kotlin
bannerView?.startAutoRefresh()
```

### 七、原生广告

#### 1. 创建对象

```Kotlin
var nativeAdLoader: BAGNativeAdLoader? = null

nativeAdLoader = BAGNativeAdLoader(this, strategy).apply {
    setAdListener(object : BAGNativeAdListener {
        override fun onAdLoaded(bagAd: BAGAd?) {
            // 广告加载成功
        }

        override fun onAdLoadFailed(adError: BAGError?) {
            // 所有广告加载失败
        }

        override fun onMediationLoadFailed(mediationError: BAGMediationError?) {
            // 各平台加载失败
        }

        override fun onAdShow(bagAd: BAGAd?) {
            // 广告展示
        }

        override fun onAdShowFailed(bagAd: BAGAd?, adError: BAGError?) {
            // 广告展示失败
        }

        override fun onAdClick(bagAd: BAGAd?) {
            // 广告点击
        }
    })

    setAdRevenueListener(object : BAGAdRevenueListener {
        override fun onAdRevenuePaid(bagAd: BAGAd?) {
            // 广告收益
        }
    })

    // 可选
    setAdExListener(object : BAGAdExListener {
        override fun beforeLoadAd(mediationAdObject: Any?) {
            // 各平台广告请求对象
        }
    })
}
```

- 配置格式及说明

```json
{
  "name": "bagNative",
  //广告位名称 可自定义
  "maxWaitTime": 5000,
  //单位：毫秒，最长加载回调等待时长 例：设置为5000毫秒，加载成功回调将在3秒后或所有平台加载结束时回调
  "platforms": [
    {
      "platform_id": 0,
      // 平台id 可自定义
      "platform_name": "Carty",
      // 平台名称
      "className": "com.bagsdk.mediation.carty.CartyNativeAdapter",
      // 适配器包路径
      "placement_id": "281217152610"
      // Carty后台广告位id
    },
    {
      "platform_id": 1,
      // 平台id 可自定义
      "platform_name": "Max",
      // 平台名称
      "className": "com.bagsdk.mediation.max.MaxNativeAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID",
      //MAX后台的 unitid
      "placement": "maxNative"
    },
    {
      "platform_id": 2,
      // 平台id 可自定义
      "platform_name": "AdMob",
      // 平台名称
      "className": "com.bagsdk.mediation.admob.AdMobNativeAdapter",
      // 适配器包路径
      "unit_id": "YOUR_AD_UNIT_ID"
      // admob后台的 unitid
    }
  ]
}
```

#### 2. 加载广告

```Kotlin
nativeAdLoader?.loadAd()
```

#### 3. 展示广告

```Kotlin
val bagNativeAdView = BAGNativeAdView(this, getBAGNativeAdViewBinder())
nativeAdLoader?.render(bagNativeAdView, object : BAGNativeAdRender() {
    override fun loadImage(imageView: ImageView?, url: String?) {
        imageView?.load(url)
    }
})
binding.adContainer.removeAllViews()
binding.adContainer.addView(bagNativeAdView)

private fun getBAGNativeAdViewBinder(): BAGNativeAdViewBinder {
    return BAGNativeAdViewBinder.Builder()
        .setLayoutResourceId(R.layout.layout_native)
        .setTitleTextViewId(R.id.title_tv)
        .setBodyTextViewId(R.id.sub_title_tv)
        .setSponserViewId(R.id.sponsored_tv)
        .setIconImageViewId(R.id.icon_iv)
        .setMediaContentViewGroupId(R.id.media_view_container)
        .setCallToActionButtonId(R.id.cta_btn)
        .build()
}
```

### 八、其他

#### 1. 设置 DoNotSell

```Kotlin
BAGSdk.setDoNotSell(false)
```

#### 2. 设置 COPPA

```Kotlin
 BAGSdk.setCoppa(false)
```

#### 3. 设置UserID

```Kotlin
BAGSdk.setUserId("YOUR_USER_ID")
```

#### 4. 回调中的 BAGAd 对象属性说明

| 属性                                  | 说明                                        |
|-------------------------------------|-------------------------------------------|
| int getAdType()                     | 广告类型：开屏 = 0,横幅 = 1,插屏 = 2,原生 = 3,激励视频 = 4 |
| int getMediationId()                | 配置中设置的platform_id                         |
| String getMediationName()           | 配置中设置的platform_name                       |
| String getMediationPId()            | 各平台配置中设置的广告位ID                            |
| String getPlacementName()           | 聚合返回平台的networkPlacement                   |
| double getECpm()                    | 每千次展示的有效收益                                |
| BAGNativeAd getNativeAd()           | 原生广告对象                                    |
| String getRevenuePrecision()        | 聚合返回平台的revenuePrecision                   |
| Object getMediationOriginAdObject() | 各平台返回的原始对象                                |