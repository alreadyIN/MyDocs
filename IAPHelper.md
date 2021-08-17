
## IAPHelper 集成文档 
>本文链接 https://github.com/alreadyIN/MyDocs/blob/main/IAPHelper.md

#### 当前版本功能支持的功能
- 内购商品列表请求-基于Block回调风格返回商品请求结果
- 内购商品预加载- 应用启动后自动向苹果内购服务器请求内购商品列表
- 支持自动订阅
- 支持非消耗型内购商品购买 如移除广告，功能解锁等
- 支持本地内购订单持久化
- 支持本地会员有效期查询
- 支持已购商品恢复购买
- 支持不同权益档次会员，不同档次权益有效期互不干扰
*(如需实现高级会员过期后才生效普通会员，可根据各档次会员有效期自行重新叠加计算有效期)*

#### 当前版本暂不支持的功能
- 新用户折扣优惠购买
- 老用户折扣优惠购买


#### 集成步骤及模块初始化
1.将IAHelper工程拖入到想要集成内购模块的 Workspace 中

![1284564-5b7444dced020f4e](https://user-images.githubusercontent.com/88173886/128484783-f9f03df2-9a75-4336-9866-1d8a207d2846.png)


2.将 IAPHelper.framework 添加到项目工程中

![1284564-457b308a18c8f104](https://user-images.githubusercontent.com/88173886/128484814-e142e4b4-ded5-4571-a302-77f127d7d407.png)



3. 在需要用到 IAPHeper 模块的地方，导入 IAPHelper 模块
```
#import <IAPHelper/IAPHelper.h>
```

4.在工程的AppDelegate的初始化方法中 设置程序支持的内购项目。
*注意：无论内购项是否在售卖状态，只要曾经有用户购买过，均需添加，因为其将会决定用户的相关权益有效期计算*
```
+(void)initialize{
    //NOTE: 商品信息录入必须在内购模块启动前
    //自动订阅组
    IARenewalProductInfo * autoWeekProductInfo = [[IARenewalProductInfo alloc] initWithProductId:Group_First_Auto_Renew_First_Level periodType:ProductPeriodPerWeek probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kVIPGroupId isAutoRenewType:YES];
    
    IARenewalProductInfo * autoMonthProductInfo = [[IARenewalProductInfo alloc] initWithProductId:Group_First_Auto_Renew_Second_Level periodType:ProductPeriodPerMonth probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kVIPGroupId isAutoRenewType:YES];
    
    IARenewalProductInfo * autoYearProductInfo = [[IARenewalProductInfo alloc] initWithProductId:Group_First_Auto_Renew_Third_Level periodType:ProductPeriodPerYear probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kVIPGroupId isAutoRenewType:YES];
    
    //订阅组A
    IARenewalProductInfo * monthProductInfoA = [[IARenewalProductInfo alloc] initWithProductId:Program_A_First_Level_Original periodType:ProductPeriodPerMonth probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kVIPGroupId isAutoRenewType:NO];
    
    IARenewalProductInfo * yearProductInfoA = [[IARenewalProductInfo alloc] initWithProductId:Program_A_Second_Level_Original periodType:ProductPeriodPerYear probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kVIPGroupId isAutoRenewType:NO];
    
    IARenewalProductInfo * foreverProductInfoA = [[IARenewalProductInfo alloc] initWithProductId:Program_A_Third_Level_Original periodType:ProductPeriodForever probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kVIPGroupId isAutoRenewType:NO];
    
    //订阅组B
    IARenewalProductInfo * monthProductInfoB = [[IARenewalProductInfo alloc] initWithProductId:Program_B_First_Level_Original periodType:ProductPeriodPerMonth probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kSVIPGroupId isAutoRenewType:NO];
    
    IARenewalProductInfo * yearProductInfoB = [[IARenewalProductInfo alloc] initWithProductId:Program_B_Second_Level_Original periodType:ProductPeriodPerYear probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kSVIPGroupId isAutoRenewType:NO];
    
    IARenewalProductInfo * foreverProductInfoB= [[IARenewalProductInfo alloc] initWithProductId:Program_B_Third_Level_Original periodType:ProductPeriodForever probabtionPeriodType:ProductProbationPeriodTypeNone groupId:kSVIPGroupId isAutoRenewType:NO];
    
    //配置会员权益等计时型商品内购项。录入黄金月度会员，黄金年度会员，周会员，永久会员等
    [[IAPaymentCommon shareInstance] configRenewalProductInfosFromDeveloper:@[
        autoWeekProductInfo,autoMonthProductInfo,autoYearProductInfo,
        monthProductInfoA,yearProductInfoA,foreverProductInfoA,
        monthProductInfoB,yearProductInfoB,foreverProductInfoB
    ]];
    
    //配置普通非消耗型内购项。例如：移除广告内购，解锁关卡内购
//    IABaseProductInfo * removeAdsProduct = [[IABaseProductInfo alloc] initWithProductId:@"removeAds"];
//    IABaseProductInfo * unlockMoreFuncProduct = [[IABaseProductInfo alloc] initWithProductId:@"unlockMoreFuncs"];
//    [[IAPaymentCommon shareInstance] configNormalProductInfosFromDeveloper:@[removeAdsProduct,unlockMoreFuncProduct]];
}
```

5. 在应用启动时，调用 IAPaymentCommon 的 load 方法来完成内购模块加载
*注意：需要传入的参数为该Apple开发者账号下的共享密钥，该密钥将影响本地购买凭据验证，进而影响相关权益有效期计算*
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    //内购通用模块加载
    [[IAPaymentCommon shareInstance] load:IAPSecretKey];
    
//    未正常完成的交易
//    NSArray * unfinishedTransactions = [[IAPaymentCommon shareInstance] getUnFinishedPaymentTransactions];
//    if (unfinishedTransactions.count) {
//
//    }
    
    return YES;
}
```

#### 商品请求、购买、恢复购买
###### 商品请求
*商品请求是后台静默执行的，不包含UI显示（系统弹出的除外）,故为了交互友好，请在具体业务中添加相关等待提示框，状态提示框UI显示*
```
[[IAPaymentCommon shareInstance] requestAllPreloadProductsWithCompletion:^(NSArray<IABaseProductInfo *> * _Nullable productArray, IAProductRequestStatus status, NSError * _Nullable error) {
        if (!error && productArray) {
            self.allProductInfos = [NSMutableArray arrayWithArray:productArray];
        }
        
        dispatch_async(dispatch_get_main_queue(), ^{
            [self.refreshControl endRefreshing];
            [self.indicatorView stopAnimating];
            self.indicatorView.hidden = YES;
            [self.tableView reloadData];
        });
    }];
```

*另一种商品请求方式，可精确请求指定商品列表*

```
[[IAPaymentCommon shareInstance] requestMultipleProductIds:[IAPaymentCommon shareInstance].allProductIds complateHandler:^(NSArray<IABaseProductInfo *> * _Nullable productArray, IAProductRequestStatus status, NSError * _Nullable error) {
        //code here to refresh UI
    }];

```

###### 商品购买
*商品购买是后台静默执行的，不包含UI显示（系统弹出的除外）,故为了交互友好，请在具体业务中添加相关等待提示框，状态提示框UI显示*

```
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
    hud.label.text = NSLocalizedString(@"购买中...", @"HUD loading title");
    
    IABaseProductInfo * productInfo = self.allProductInfos[indexPath.row];
    [[IAPaymentCommon shareInstance] purchaseProductByProductId:productInfo.productId purchasing:^(NSString * _Nonnull productId, IAPurchasingState purchasingState, SKPaymentTransaction * _Nonnull transaction) {
        
        dispatch_async(dispatch_get_main_queue(), ^{
            if (purchasingState == IAPurchasingStateOrderGenerating) {
                hud.label.text = @"正在生成支付订单，请稍后...";
            }
            else{
                hud.label.text = @"订单已生成，正在获取订单信息...";
            }
        });
        
    } purchaseComplete:^(NSString * _Nonnull productId, SKPaymentTransaction * _Nullable paymentTransaction, BOOL isSuccess, NSError * _Nullable error) {
        
        dispatch_async(dispatch_get_main_queue(), ^{
            
            [hud hideAnimated:YES];
            
            NSString * message = isSuccess ? @"购买成功！" : @"购买失败！";
            
            MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

            // Set the custom view mode to show any view.
            hud.mode = MBProgressHUDModeCustomView;
            // Set an image view with a checkmark.
            UIImage *image = [[UIImage imageNamed:@"Checkmark"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
            hud.customView = [[UIImageView alloc] initWithImage:image];
            // Looks a bit nicer if we make it square.
            hud.square = YES;
            // Optional label text.
            hud.label.text = message;

            [hud hideAnimated:YES afterDelay:2.f];
            
            if (isSuccess) {
                [self updateExpireDateInfo];
            }
        });
    } purchaseCancelled:^(NSString * _Nonnull productId, NSError * _Nullable error) {
        
        [hud hideAnimated:YES];
        
        NSString * message = @"购买已取消";
        
        MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

        // Set the custom view mode to show any view.
        hud.mode = MBProgressHUDModeCustomView;
        // Set an image view with a checkmark.
        UIImage *image = [[UIImage imageNamed:@"Checkmark"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
        hud.customView = [[UIImageView alloc] initWithImage:image];
        // Looks a bit nicer if we make it square.
        hud.square = YES;
        // Optional label text.
        hud.label.text = message;

        [hud hideAnimated:YES afterDelay:2.f];
    }];
```


###### 恢复购买

*商品恢复购买是后台静默执行的，不包含UI显示（系统弹出的除外）,故为了交互友好，请在具体业务中添加相关等待提示框，状态提示框UI显示*

>恢复购买提供有两个 api 来实现，分别是

*调用苹果提供的恢复内购Api实现*

```
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
    hud.label.text = NSLocalizedString(@"恢复购买中，请稍后...", @"HUD loading title");
    
    [[IAPaymentCommon shareInstance] restorePurchaseWithComplete:^(NSArray * _Nonnull productIds, BOOL isSuccess, NSError * _Nullable error) {
        
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
        [self onRestoreComplete:productIds restoreState:isSuccess error:error];
    }];
```
*调用苹果提供的刷新购买凭据Api实现*

```
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
    hud.label.text = NSLocalizedString(@"恢复购买中，请稍后...", @"HUD loading title");
    
    [[IAPaymentCommon shareInstance] refreshReceiptWithComplete:^(NSArray * _Nullable productIds, BOOL isSuccess, NSError * _Nullable error) {
        
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
        [self onRestoreComplete:productIds restoreState:isSuccess error:error];
    }];
```
```
//恢复购买完成
- (void)onRestoreComplete:(NSArray *)productIds restoreState:(BOOL)isSuccess error:(NSError *)error{
    
    dispatch_async(dispatch_get_main_queue(), ^{
        
        NSString * message = isSuccess ? @"恢复购买成功！" : @"恢复购买失败！";
        
        MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

        // Set the custom view mode to show any view.
        hud.mode = MBProgressHUDModeCustomView;
        // Set an image view with a checkmark.
        UIImage *image = [[UIImage imageNamed:@"Checkmark"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
        hud.customView = [[UIImageView alloc] initWithImage:image];
        // Looks a bit nicer if we make it square.
        hud.square = YES;
        // Optional label text.
        hud.label.text = message;

        [hud hideAnimated:YES afterDelay:2.f];
        
        if (isSuccess) {
            [self updateExpireDateInfo];
        }
    });
}
```

*目前并未发现两种方式有质的区别*

######  刷新会员有效期
>刷新普通会员有效期
```
//更新VIP有效期
- (void)updateVIPExpireInfo{
    
    //普通vip
    ProductGroupInfo * vipProductGroupInfo =  [[IAPaymentCommon shareInstance] getProductGroupWithGroupId:kVIPGroupId];
    
    NSTimeInterval timeInterval = vipProductGroupInfo.expireDateMs;
    NSDate * expireDate = [NSDate dateWithTimeIntervalSince1970:timeInterval];
    NSDateFormatter * formatter = [[NSDateFormatter alloc] init];
    formatter.dateFormat = @"yyyy-MM-dd";
    NSString * expireDateString = [formatter stringFromDate:expireDate];
    
    NSDate * relativetime = [[IATimeManager defaultManager] getRelativetime];//获取当前时间
    NSTimeInterval relativeTimeinterval = [relativetime timeIntervalSince1970];
    
    self.vipExpireDateLabel.textColor = [UIColor systemBlueColor];
    //当前是否有已购买且在有效期内的自动订阅内购项
    if (vipProductGroupInfo.hasActiveAutoRenewOrder) {
        self.vipExpireDateLabel.text = @"VIP到期时间：自动续订中";
    }else{
        if (relativeTimeinterval > timeInterval) {
            self.vipExpireDateLabel.textColor = [UIColor systemRedColor];
            self.vipExpireDateLabel.text = [NSString stringWithFormat:@"VIP会员已于%@过期",expireDateString];
            if (timeInterval == 0) {
                self.vipExpireDateLabel.text = @"未开通";
            }
        }
        else{
            if (timeInterval >= LONG_MAX) {
                self.vipExpireDateLabel.textColor = [UIColor systemPinkColor];
                self.vipExpireDateLabel.text = @"VIP到期时间：永不过期";
            }else{
                self.vipExpireDateLabel.text = [NSString stringWithFormat:@"VIP会员到期时间：%@",expireDateString];
            }
        }
    }
}
```
>刷新超级会员有效期(没有可不处理)
```
//更新SVIP有效期
- (void)updatesSVIPExpireInfo{
    
    //超级vip
    ProductGroupInfo * svipProductGroupInfo =  [[IAPaymentCommon shareInstance] getProductGroupWithGroupId:kSVIPGroupId];
    
    NSTimeInterval timeInterval = svipProductGroupInfo.expireDateMs;
    NSDate * expireDate = [NSDate dateWithTimeIntervalSince1970:timeInterval];
    NSDateFormatter * formatter = [[NSDateFormatter alloc] init];
    formatter.dateFormat = @"yyyy-MM-dd";
    NSString * expireDateString = [formatter stringFromDate:expireDate];
    
    NSDate * relativetime = [[IATimeManager defaultManager] getRelativetime];//获取当前时间
    NSTimeInterval relativeTimeinterval = [relativetime timeIntervalSince1970];
    
    self.svipExpireDateLabel.textColor = [UIColor systemBlueColor];
    //当前是否有已购买且在有效期内的自动订阅内购项
    if (svipProductGroupInfo.hasActiveAutoRenewOrder) {
        self.svipExpireDateLabel.text = @"SVIP到期时间：自动续订中";
    }else{
        if (relativeTimeinterval > timeInterval) {
            self.svipExpireDateLabel.textColor = [UIColor systemRedColor];
            self.svipExpireDateLabel.text = [NSString stringWithFormat:@"SVIP会员已于%@过期",expireDateString];
            if (timeInterval == 0) {
                self.svipExpireDateLabel.text = @"未开通";
            }
        }
        else{
            if (timeInterval >= LONG_MAX) {
                self.svipExpireDateLabel.textColor = [UIColor systemPinkColor];
                self.svipExpireDateLabel.text = @"SVIP到期时间：永不过期";
            }else{
                self.svipExpireDateLabel.text = [NSString stringWithFormat:@"SVIP会员到期时间：%@",expireDateString];
            }
        }
    }
}
```

###### 凭据验证成功通知
*因为本内购模块中权益有效期计算是基于本地购买凭据验证得来的，所以最真实的有效期信息应该来源于凭据验证，故建议在需要展示vip或记录vip有效期信息的地方注册本地凭据验证成功的通知， 并于业务中刷新权益有效期信息*

```
//已购订单凭据验证完成通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onReceiptValidCompleteNotification:) name:kReceiptValidCompleteNotification object:nil];
```
```
/* 内购商品凭据本地校验完成通知 */
- (void)onReceiptValidCompleteNotification:(NSNotification *)notification{
    [self updateExpireDateInfo];;
}
```

###### 商品请求成功通知
```
//商品请求完成通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onProductsRequestCompleteNotification:) name:kProductInfosRequestSucceedNotification object:nil];
```

###### 来自AppStore商品购买通知
```
//来自AppStore商品购买通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onAppStorePaymentNotification:) name:kPaymentFromAppStoreShouldHandleNotification object:nil];
```
