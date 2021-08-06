
#### 当前版本功能支持的功能
>本文链接：https://github.com/alreadyIN/MyDocs/blob/main/ADSHelper.md

>Admob广告官网：https://developers.google.cn/admob/ios/quick-start

>优量汇(腾讯)广告官网：https://developers.adnet.qq.com/doc/ios/guide

**优量汇(腾讯)广告**
-  开屏广告 -    *当前仅支持iPhone竖屏，不支持iPad及iPhone横屏*
-  横幅广告
-  原生广告 -    *当前支持众多原生广告模板，稍后会列举*
-  插屏广告-     *支持横竖屏，类型有：半屏非视频插屏广告，半屏视频插屏广告，全屏视频插屏广告*
-  视频激励广告

**Admob广告**
-  开屏广告-    *目前是用Admob大型原生广告模拟实现*
-  横幅广告
-  原生广告-     *目前提供了两种可用模板，也可自己设计广告展示模板来适应App UI设计*
-  插屏广告-     *Admob插屏广告随机提供视频或非视频型全屏插屏广告*
-  视频激励广告


#### 集成步骤
1. 工程要求及准备
>工程不要是StoryBoard创建的工程，需要手动设置 RootViewController ，如何实现非StoryBoard布局可参考：https://github.com/alreadyIN/MyDocs/blob/main/NonStoryBoard.md

2. 将IAHelper文件夹拖入到主工程中
>注意勾选中 Copy Items if needed / Create Groups/ 链接到主工程Target
>
![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d383961313733333333653063366638302e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128481961-b5735a2a-8958-442a-9a72-124bc9a151ee.png)



3. 利用Pod集成广告sdk
>广告模块并未集成广告SDK，为了方便集成，在此选择的是Pod集成方式，故需要在加入了广告模块的工程中使用Pod集成Admob广告及优量汇(腾讯)广告SDK.

```
target 'ADIntergateDemo' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!
  pod 'Google-Mobile-Ads-SDK'
  pod 'GDTMobSDK'
  # Pods for ADIntergateDemo
end
```

4. 填写Admob广告应用id及相关配置
>以往版本的Admob广告是用 GoogleService.plist文件来配置的广告信息，最新的Admob广告采用的是在info.plist里配置。

 4.1 配置Admob App ID      *(图中只是示例，请填写自己申请的App Id)*
 
![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d643363663035373031386462616561662e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128481868-353ac689-be28-425d-bbd0-2573276bd639.png)


4.2  广告商信息  *（可从Demo工程中直接复制得到，也可在Admob官网获取:https://developers.google.cn/admob/ios/quick-start）*  

![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d313936363335383764353336336465382e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128481838-f98f5b30-af26-48bf-a8f3-6c2398462e4d.png)!


4.3  允许Http请求访问  *(为了更好的方便广告加载，最好是加上下图配置)*

![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d616239356335363765396434636265642e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128482002-d932aea0-8ba2-47ae-b93f-c69b601759a8.png)



5.  代码部分
  5.1  在AppDelegate 文件中配置广告配置
    > 需要配置的有Admob 横幅广告id，Admob原生广告id，Admob插屏广告id，Admob视频激励广告id，Admob开屏广告id。  按需配置，应用中没用到的，可以不用配置 。 需要配置的腾讯广告id也一样，按需配置，特别是腾讯原生广告，类型很多，用到哪些配置哪些即可。

```
+ (void)initialize {
    
    //配置Admob五大广告单元ID
    [ADSCommon shareInstance].admob_native_unitId = @"原生广告ID"; //原生广告(也用于开屏广告)
    [ADSCommon shareInstance].admob_banner_unitId = @"横幅广告ID";  //横幅广告
    [ADSCommon shareInstance].admob_interstitial_unitId = @"插屏广告ID";  //插屏广告
    [ADSCommon shareInstance].admob_reward_unitId = @"视频激励广告ID";  //视频激励广告
    [ADSCommon shareInstance].admob_appOpenAd_unitId = @"开屏广告ID";  //开屏广告(注意：仅用于从后台进入前台时展示)
    
    //配置优量汇（腾讯）广告普通广告ID
    [ADSCommon shareInstance].tencentAd_banner_placementId = @"横幅广告Placement ID";
    [ADSCommon shareInstance].tencent_interstitial_nonvideo_placementId = @"非视频类插屏广告ID";
    [ADSCommon shareInstance].tencent_interstitial_video_placementId = @"视频类插屏广告ID";
    [ADSCommon shareInstance].tencent_splashad_placementId = @"开屏广告ID";
    
    //配置优量汇（腾讯）广告原生广告ID
    [ADSCommon shareInstance].lImageRText1200x800PlacementId = @"左图右文原生广告ID";
    [ADSCommon shareInstance].threeImages228x150PlacementId = @"三小图原生广告ID";
    //...   腾讯广告有多种原生广告，按需配置使用即可
    
    [ADSCommon shareInstance].isTestMode = YES;//如果希望用测试广告ID测试，可开启该功能
    
    [ADSCommon shareInstance].adType = ADTypeAdmob; //广告类型，为Admob广告还是优量汇广告
    
    //是否开启广告备用策略，例如，Admob广告无法展示了，可展示腾讯广告，该配置只对插屏广告及视频激励广告有效
    [ADSCommon shareInstance].enableAdBackupStrategy = YES;
    
    //开屏广告加载等待时长，默认为5，如不改动等待市场，可不设置
    [ADSCommon shareInstance].fetchDelay = 5;
    
    //应用从后台进入前台，是否弹出开屏广告，默认为YES，如不希望弹出开屏广告，可设置为NO
    [ADSCommon shareInstance].showSplashAdWhenApplicationBecomeActiveFromBackground = YES;
    
    [ADSCommon shareInstance].needPreloadRewardAd = YES; //是否预加载视频激励广告
    [ADSCommon shareInstance].needPreloadInterstitialAd = YES;  //是否预加载插屏广告
    
    //注册优量汇广告 -- 优量汇广告是需要执行注册方法的。 此处的id是腾讯广告App id，请更改为自己申请的。切记千万要修改，否则展示的是测试广告
    [[ADSCommon shareInstance] registerTencentAdIfNeed:@"1105344611"];
    
    //预加载广告 -- 仅对插屏及视频激励广告有效
    [[ADSCommon shareInstance] preloadAdsIfNeed];
}

```

   5.2  开屏广告实现
   >开屏广告加载是需要时间的，这段时间展示的就是开屏广告页原始UI，因为各App风格不一，故广告模块中只是提供了开屏广告加载及展示的具体业务实现，并未设计开屏广告加载过程中UI展示，故需要集成SDK相关人员自行实现开屏广告页UI(这部分UI是在开屏广告加载过程中时展示的)，只需继承开屏广告页基类 ```ADSplashBaseViewController```  来对开屏广告页实现定制，只需负责UI部分，例如：直接给定一张加载预览图。demo中没有处理UI也能有较好的UI效果，因为从启动页过渡到开屏页，大多时候是看不到开屏页原始UI的，当然，建议实现一下最好。

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    
    
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    self.window.backgroundColor = [UIColor whiteColor];
    UIViewController * rootViewController = [[ViewController alloc] init];
    UINavigationController * navi = [[UINavigationController alloc] initWithRootViewController:rootViewController];
    navi.navigationBar.translucent = NO;
    
    
    ADOpenAdViewController * adSplashVC = [[ADOpenAdViewController alloc] initWithNibName:@"ADOpenAdViewController" bundle:NSBundle.mainBundle rootViewController:navi];
    
    self.window.rootViewController = adSplashVC;
    [self.window makeKeyAndVisible];
    
    return YES;
}
```

5.3 开屏广告页底部品牌展示
>可能存在在开屏广告页底部展示品牌Logo及品牌语的需求，这时候开屏广告页底部区域即可派上用场。当然，如果不需要的话，可不作任何操作
```
- (void)viewDidLoad {
    [super viewDidLoad];
    
    //一下是开屏广告底部logo展示，这里是用一张图片代替，当然，也可以自行定制交互，非常灵活，只需高度在 appOpenAdBottomVieHeight 的高度内即可
    CGFloat bottomViewHeight = [self appOpenAdBottomVieHeight];
    UIImageView * bottomView = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0, [UIScreen mainScreen].bounds.size.width, bottomViewHeight)];
    bottomView.image = [UIImage imageNamed:@"SplashLogo"];
    [self.view addSubview:bottomView];
    
    bottomView.translatesAutoresizingMaskIntoConstraints = NO;
    [bottomView.leftAnchor constraintEqualToAnchor:self.view.leftAnchor constant:30].active = YES;
    [bottomView.rightAnchor constraintEqualToAnchor:self.view.rightAnchor constant:-30].active = YES;
    [bottomView.bottomAnchor constraintEqualToAnchor:self.view.bottomAnchor constant:-20].active = YES;
    [bottomView.heightAnchor constraintEqualToConstant:bottomViewHeight-20].active = YES;
    // Do any additional setup after loading the view from its nib.
}


//品牌logo所占高度,该高度内不会有开屏广告展示
- (CGFloat)appOpenAdBottomVieHeight {
    return 60.0;
}

//开屏广告跳过按钮标题。可能需要做词条国际化~
- (NSString *)skipButtonTitle {
    return @"跳过";
}
```

*下图是有品牌logo的开屏页*

![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d373138393336393831653066343331642e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128482542-5f100218-6207-41cf-8012-9d0671057193.png)


*下图是没有品牌logo的开屏页*

![1284564-4d093b15f28f871b](https://user-images.githubusercontent.com/88173886/128482731-56d355fa-6a9c-479a-aa6b-3a8c2d6700df.png)



5.4  横幅广告实现
>将需要展示横幅广告的页面，继承广告基类控制器 ``` ADBaseViewController```
```
#pragma mark ---------  Banner Ads ----------

///横幅广告加载成功，由自己决定展示位置，可能需要重新布局UI
- (void)bannerAdViewLoaded:(UIView *)bannerAdView adType:(ADType)adType {
    
    [self.view addSubview:bannerAdView];
    [bannerAdView mas_makeConstraints:^(MASConstraintMaker *make) {
        if (@available(iOS 11.0, *)) {
            make.centerX.equalTo(self.view);
            make.width.mas_equalTo(bannerAdView.frame.size.width);
            if (adType == ADTypeAdmob) {
                make.bottom.equalTo(self.view.mas_safeAreaLayoutGuideBottom);
            }
            else{
                make.top.equalTo(self.view.mas_safeAreaLayoutGuideTop);
            }
        } else {
            if (adType == ADTypeAdmob) {
                make.left.right.bottom.equalTo(self.view);
            }
            else{
                make.left.right.top.equalTo(self.view);
            }
        }
        make.height.mas_equalTo(bannerAdView.frame.size.height);
    }];
}

/// 原生广告关闭，可能需重新布局UI
/// @param bannerView 被关闭的横幅广告
- (void)bannerAdViewClosed:(UIView *)bannerView {
      //在这里处理横幅广告被关闭后的UI重新布局业务
}


///是否需要展示横幅广告. 如不需要展示横幅广告，则返回NO，默认为YES
- (BOOL)needShowBannerView {
    return YES;
}


///横幅广告宽度. 默认为屏幕宽度. 注意不要设置的太小，否则请求不到何时的广告从而导致无广告可展示
- (CGFloat)bannerAdWidth {
    return [UIScreen mainScreen].bounds.size.width;
}

///需要展示的Admob原生广告类型。当前页面仅支持一种原生广告展示.
///仅针对于Admob广告.默认为适合列表的左图右文本风格原生广告
- (AdmobNativeAdType)needShowAdmobNativeAdType {
    return AdmobNativeAdTypeListItem;
}
```

5.4  原生广告实现
>将需要展示原生广告的页面，继承广告基类控制器 ``` ADBaseViewController```
```
#pragma mark ---------  Native Ads ----------

/// 原生广告加载成功，由自己决定展示位置，可能需要重新布局UI
/// @param nativeAdViews 加载完成的原生广告列表
/// @param adType 广告类型。Admob 还是 Tencent 广告等
- (void)nativeAdViewsLoaded:(NSArray<UIView *> *)nativeAdViews adType:(ADType)adType {
    
    if (adType == ADTypeAdmob) {
        dispatch_async(dispatch_get_main_queue(), ^{
            GADTTemplateView * nativeAdView = (GADTTemplateView *)nativeAdViews.firstObject;
            if (nativeAdView) {
                nativeAdView.layer.borderColor = [UIColor lightGrayColor].CGColor;
                nativeAdView.layer.borderWidth = 1.0f;
                [self.view addSubview:nativeAdView];
                [nativeAdView mas_makeConstraints:^(MASConstraintMaker *make) {
                    make.centerX.equalTo(self.view);
                    make.centerY.equalTo(self.view).with.offset(-40);
                    make.width.mas_equalTo(self.view.frame.size.width-2*20);
                    make.height.mas_equalTo(nativeAdView.mas_width).multipliedBy(1/nativeAdView.ratio);
                }];
            }
        });
    }
    else if (adType == ADTypeTencent) {
        UIView * nativeAdView = nativeAdViews.firstObject;
        if (nativeAdView) {
            [self.view addSubview:nativeAdView];
            [nativeAdView mas_makeConstraints:^(MASConstraintMaker *make) {
                make.left.right.equalTo(self.view);
                make.bottom.equalTo(self.view.mas_centerY).with.offset(-150);
                make.height.mas_equalTo(nativeAdView.frame.size.height);
            }];
        }
    }
}


/// 原生广告关闭，可能需重新布局UI
/// @param nativeAdViews 被关闭的原生广告
- (void)nativeAdViewsClosed:(NSArray<UIView *> *)nativeAdViews {
    DLog(@"原生广告关闭了！！！");
    //请在此处处理UI重新布局相关业务
}


///是否需要展示原生广告，不需要展示原生广告请返回NO，默认为YES
- (BOOL)needShowNativeAdView {
    return YES;
}


///需要展示的腾讯原生广告类型。 当前单页面仅支持一种原生广告展示.
///仅针对于腾讯原生广告.默认为适合列表的左图右文本风格原生广告
- (TencentNativeAdType)needShowTencentNativeAdType {
    return TencentNativeAdTypeLImageRText1200x800;
}


//原生广告请求数量.默认为1个  如需为列表请求多个原生广告，则设置为想要的个数即可，默认是1个
- (NSInteger)needShowNativeAdCount {
    return 1;
}
```

5.5  弹出插屏广告
>将需要弹出插屏广告的页面，继承广告基类控制器 ``` ADBaseViewController```  调用 ```showInterstitialAd ```方法，即可弹出插屏广告，当然，前提是插屏广告已准备好
```
[self showInterstitialAd];
```

5.6  弹出视频广告
>将需要弹出视频激励广告的页面，继承广告基类控制器 ``` ADBaseViewController```  调用 ```showRewardAd ```方法，即可弹出视频激励广告，当然，前提是视频激励广告已准备好。 该方法提供了一个回调参数，会通知当前用户是否获得了奖励，可根据此参数来决定是否发放奖励给用户，例如，一次视频剪辑的机会。
```
[self showRewardAd:^(BOOL rewarded) {
        if (rewarded) {
            UIAlertController * alertVC = [UIAlertController alertControllerWithTitle:@"提示信息" message:@"已获得奖励！" preferredStyle:UIAlertControllerStyleAlert];
            [alertVC addAction:[UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:nil]];
            [self presentViewController:alertVC animated:YES completion:nil];
        }
}];
```

6.0  Admob 原生广告自定义模板定制
>为了让原生广告更契合我们的App UI，允许对Admob原生广告UI进行自定义。具体可参考广告模块内已有案例

>*下图中 GADTSmallTemplateView 就是继承自 GADTTemplateView 实现的适合列表展示的小型原生广告模板，GADTMediumTemplateView 是继承自 GADTTemplateView 实现的适合结果页空白处或开屏广告的中大型原生广告模板, 将 xib中outlet链接到GADTTemplateView 的各 outlet 即可, UI风格即是你自定义的 xib 中设计的那样，具体展示则是由 GADTTemplateView 处理的,我们只需关心 xib 中各控件的摆放位置及大小约束即可*

![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d373564383038653130316465666665612e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128482904-5ca3fa88-1c5f-4059-8356-3e034724c10b.png)


![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d373562366630373936373166626462302e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128482967-9f7e5b40-4117-4cce-be93-566212081b8f.png)


![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d346139326333633661656439666638622e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128483019-571b43d6-0b6d-4571-a911-72ddd13659ab.png)


*如下两张图，分别是Admob中大型原生广告及小型列表式原生广告*

![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d666135323637376339613134616365362e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128483043-6d32b42b-9959-4a83-a3a8-254e1bf2a3e2.png)


![1284564-ca0647d0fcc2740b](https://user-images.githubusercontent.com/88173886/128483149-e0154bbd-245f-41ee-a370-2a3208f07fce.png)



6.1  Admob原生广告宽高比
>注意：一定要根据原生广告自定义模板中的宽高比返回正确的宽高比，因为我们在布局Admob原生广告时，并不知道其具体尺寸，只能参照宽高比对其进行布局，以免广告被拉伸的变形。

![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d363632346432663632373435326335622e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128483243-febc15a4-7077-417e-be0f-c4a2f1d217a2.png)

![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d346664333634666632306465623133352e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128483267-e898a546-a365-4203-bec8-dc9acc58bb2c.png)

![68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f313238343536342d643961393162396263323536373166652e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f](https://user-images.githubusercontent.com/88173886/128483291-e4157e4c-f7f6-4a92-9bdc-d9143eba10d4.png)


7.0 优量汇(腾讯)原生广告样式
  *上图下文(图片尺寸1280x720)*

![C87F4141-E321-47E9-86AE-EA6D219D4E53_1_105_c](https://user-images.githubusercontent.com/88173886/128484139-43bcaf09-d4b9-4476-bc1a-7639e7d5f217.jpeg)


  *上文下图(图片尺寸1280x720)*

![A6CB28A9-1E23-4E43-86D7-E0BDA1F8F747_1_105_c](https://user-images.githubusercontent.com/88173886/128484191-83cca2b6-22fb-4798-9fff-79188f4f7150.jpeg)


  *左图右文(图片尺寸1200x800)*

![63A4C15A-3C10-4B0D-9726-C2DCDCA43E36_1_105_c](https://user-images.githubusercontent.com/88173886/128484203-b4e7bf71-9242-45be-bda5-9a34ef094152.jpeg)


  *左文右图(图片尺寸1200x800)*

![55BB923E-A6F0-4DC0-8E45-8FB101328EBC_1_105_c](https://user-images.githubusercontent.com/88173886/128484222-25c92c90-23ba-48f7-a022-740a6b0d6df7.jpeg)


  *双图双文(图片尺寸1280x720)*

![3B40A314-5D47-4D2E-BA3F-5AFB1CCA212A_1_105_c](https://user-images.githubusercontent.com/88173886/128484241-81a40f8b-6bd9-400c-b6de-69be86d3ab23.jpeg)

 
  *纯图片(图片尺寸1280x720)*

![0AED3458-9B0A-42BE-86DB-8ED33DCC53FE_1_105_c](https://user-images.githubusercontent.com/88173886/128484299-b63d7322-7740-4ce1-bc06-581f52e30d5b.jpeg)


  *三小图(图片尺寸228x150)*

![7C883DA3-3D4F-400A-9031-D4982F8BB7B1_1_105_c](https://user-images.githubusercontent.com/88173886/128484314-01557b94-3d84-49e6-9a45-9bb97674f333.jpeg)


  *文字符层(上文下图1280x720)*

![86BD0A8F-91F7-45E3-9555-63FC765B6306_1_105_c](https://user-images.githubusercontent.com/88173886/128484329-dffdc1bc-c42a-476f-97e4-db60e981ad84.jpeg)


  *文字符层(上图下文1280x720)*

![BF98CB7F-D766-4CF0-8B4C-ABAC28EDE5EE_1_105_c](https://user-images.githubusercontent.com/88173886/128484351-06ae06e3-f460-4e98-82e8-777218d80c4c.jpeg)


  *文字符层(单图1280x720)*

![E8E13F5B-D568-4D6E-B908-5F8B34349B6E_1_105_c](https://user-images.githubusercontent.com/88173886/128484376-98c159e1-33ec-4bd9-951f-b2e86ec1902f.jpeg)


  *纯图片(图片尺寸1080x1920或800x1200)*

![13C77FC1-4F3D-401E-9325-B24BDDA752AB_1_105_c](https://user-images.githubusercontent.com/88173886/128484387-c58ab01f-d55a-4608-b84d-e1a3eefe1e92.jpeg)


>优量汇(腾讯)原生视频广告样式与上述某些是一致的，只不过展示的视频，暂不予展示


8.0  移除广告
> 广告模块内支持一键移除所有广告，注意在广告被成功移除后，需要在广告移除回调方法中重新布局UI，以免因广告被移除而导致UI显示有大量空白区域。
```
[[ADSCommon shareInstance] removeAds];
```
