# Android - Max 接口对照表

## 1. SDK 初始化

| 功能描述                 | MAX SDK 接口                                                               | BAGSDK 接口                        |
|:---------------------|:-------------------------------------------------------------------------|:---------------------------------|
| 初始化 SDK              | `AppLovinSdk.getInstance(this).initialize(initConfig) { sdkConfig -> }`  | `BAGSdk.init(context, listener)` |
| 设置 UserID            | `AppLovinSdk.getInstance(context).getSettings().setUserIdentifier("id")` | `BAGSdk.setUserId("id")`         |
| 设置隐私合规 (UserConsent) | `AppLovinPrivacySettings.setHasUserConsent(true)`                        | `BAGSdk.setHasUserConsent(true)` |
| 设置隐私合规 (DoNotSell)   | `AppLovinPrivacySettings.setDoNotSell(true)`                             | `BAGSdk.setDoNotSell(true)`      |

## 2. 开屏广告 (App Open)

| 功能描述   | MAX SDK 接口                        | BAGSDK 接口                         |
|:-------|:----------------------------------|:----------------------------------|
| 创建对象   | `MaxAppOpenAd(adUnitId, context)` | `BAGAppOpenAd(context, strategy)` |
| 设置广告监听 | `setListener(listener)`           | `setAdListener(listener)`         |
| 设置收益监听 | `setRevenueListener(listener)`    | `setAdRevenueListener(listener)`  |
| 加载广告   | `loadAd()`                        | `loadAd()`                        |
| 广告就绪状态 | `isReady()`                       | `isReady()`                       |
| 展示广告   | `showAd()/showAd(placement)`      | `showAd(activity, container)`     |

## 3. 插屏广告 (Interstitial)

| 功能描述   | MAX SDK 接口                             | BAGSDK 接口                              |
|:-------|:---------------------------------------|:---------------------------------------|
| 创建对象   | `MaxInterstitialAd(adUnitId, context)` | `BAGInterstitialAd(context, strategy)` |
| 设置监听   | `setListener(listener)`                | `setAdListener(listener)`              |
| 设置收益监听 | `setRevenueListener(listener)`         | `setAdRevenueListener(listener)`       |
| 加载广告   | `loadAd()`                             | `loadAd()`                             |
| 广告就绪状态 | `isReady()`                            | `isReady()`                            |
| 展示广告   | `showAd()/showAd(placement)`           | `showAd(activity)`                     |

## 4. 激励视频 (Rewarded)

| 功能描述   | MAX SDK 接口                                     | BAGSDK 接口                          |
|:-------|:-----------------------------------------------|:-----------------------------------|
| 创建对象   | `MaxRewardedAd.getInstance(adUnitId, context)` | `BAGRewardedAd(context, strategy)` |
| 设置监听   | `setListener(listener)`                        | `setAdListener(listener)`          |
| 设置收益监听 | `setRevenueListener(listener)`                 | `setAdRevenueListener(listener)`   |
| 加载广告   | `loadAd()`                                     | `loadAd()`                         |
| 广告就绪状态 | `isReady()`                                    | `isReady()`                        |
| 展示广告   | `showAd()/showAd(placement)`                   | `showAd(activity)`                 |

## 5. 横幅广告 (Banner)

| 功能描述   | MAX SDK 接口                     | BAGSDK 接口                        |
|:-------|:-------------------------------|:---------------------------------|
| 创建对象   | `MaxAdView(adUnitId, context)` | `BAGBannerAdView(context)`       |
| 设置监听   | `setListener(listener)`        | `setAdListener(listener)`        |
| 设置收益监听 | `setRevenueListener(listener)` | `setAdRevenueListener(listener)` |
| 加载广告   | `loadAd()`                     | `loadAd()`                       |
| 停止自动刷新 | `stopAutoRefresh()`            | `stopAutoRefresh()`              |
| 开始自动刷新 | `startAutoRefresh()`           | `startAutoRefresh()`             |
| 销毁广告   | `destroy()`                    | `destroy()`                      |

## 6. 原生广告 (Native)

| 功能描述   | MAX SDK 接口                             | BAGSDK 接口                                  |
|:-------|:---------------------------------------|:-------------------------------------------|
| 创建加载器  | `MaxNativeAdLoader(adUnitId, context)` | `BAGNativeAdLoader(context, strategy)`     |
| 设置监听   | `setNativeAdListener(listener)`        | `setAdListener(listener)`                  |
| 设置收益监听 | `setRevenueListener(listener)`         | `setAdRevenueListener(listener)`           |
| 加载广告   | `loadAd()`                             | `loadAd()`                                 |
| 渲染广告   | `render(adView, nativeAd)`             | `render(bagNativeAdView, bagNativeRender)` |
| 销毁广告   | `destroy()`                            | `destroy()`                                |

## 7. 广告监听回调 (Listener)

| 功能描述    | MAX SDK 回调                         | BAGSDK 回调                        |
|:--------|:-----------------------------------|:---------------------------------|
| 广告加载成功  | `onAdLoaded(maxAd)`                | `onAdLoaded(bagAd)`              |
| 广告加载失败  | `onAdLoadFailed(adUnitId, error)`  | `onAdLoadFailed(adError)`        |
| 广告展示成功  | `onAdDisplayed(maxAd)`             | `onAdShow(bagAd)`                |
| 广告展示失败  | `onAdDisplayFailed(maxAd, error)`  | `onAdShowFailed(bagAd, adError)` |
| 广告点击    | `onAdClicked(maxAd)`               | `onAdClick(bagAd)`               |
| 广告隐藏/关闭 | `onAdHidden(maxAd)`                | `onAdClosed(bagAd)`              |
| 激励发放    | `onUserRewarded(maxAd, maxReward)` | `onAdRewarded(bagAd, rewardMap)` |
| 收益回调    | `onAdRevenuePaid(maxAd)`           | `onAdRevenuePaid(bagAd)`         |
