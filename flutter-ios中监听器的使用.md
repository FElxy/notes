在开发flutter账号功能插件时，需要分别对接公司的安卓和ios账号的SDK。

ios的SDK在接入后，调用login功能发现没有回调，因为登录页面是通过present方式弹出的新页面，这个页面包含了登录方式选择、输入账号密码登录等功能，一些奇怪的规则导致callback没有执行。

账号SDK同时提供了一个监听登录和退出的方法，因此需要对flutter插件的ios部分做一些修改。

在/ios/Classes/目录下找到xxxPlugin.h文件先添加一个声明，用来缓存一下flutter调用的callback的方法
```objc
#import <Flutter/Flutter.h>

@interface AccountPlugin : NSObject<FlutterPlugin>
// 添加如下声明
@property (nonatomic, strong) FlutterResult loginResult;

@end

```

在xxxPlugin.m文件中新增Notification的逻辑
```objc

@implementation AccountPlugin
// 新增 start
@synthesize loginResult;

// 其他代码

- (instancetype)init {
    self = [super init];
    if (self) {
        // 在初始化方法中添加监听器
        [self setupListeners];
    }
    return self;
}

- (void)dealloc {
    // 在对象销毁时移除监听器，以避免内存泄漏
    [self removeListeners];
}

- (void)setupListeners {
    // 在这里设置监听器
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(handleLoginCallback:) name:@"UserLoginDidCompleteNotification" object:nil];
    
}
- (void)removeListeners {
    // 在这里移除监听器，以避免内存泄漏
    [[NSNotificationCenter defaultCenter] removeObserver:self name:@"UserLoginDidCompleteNotification" object:nil];
}

// 为NSNotificationCenter设置监听回调函数
- (void)handleLoginCallback:(NSNotification *)notification {
    // 在这里处理登录完成通知
    NSString *notificationName = notification.name;
    NSLog(@"Received notification with name: %@", notificationName);
    if(self.loginResult) {
        NSString *response = [self JSONStringFromDictionary: @{
            @"code": @(0),
            @"message": @"success",
            @"data": [NSNull null]
        }];

        self.loginResult(response);
        self.loginResult = nil;
    }
}

- (void)handleMethodCall:(FlutterMethodCall*)call result:(FlutterResult)result {
    if ([@"login" isEqualToString:call.method]) {
        NSLog(@"登录调用>>>>>>>");
        // 保存result函数
        self.loginResult = result;
        [[VVUserManager sharedInstance] loginBlock:^(UserAccount * _Nonnull user, NSError * _Nullable error) {
            NSLog(@"登录中>>>>>>");
            if (error) {
                NSLog(@"调用失败");
                // 调用失败处理
            }
        }];
        
    }
}

// 新增 end
```

主要思路是：
1. 插件初始化时注册notification的消息通知和处理函数handleLoginCallback；
2. flutter通过method channel调用方法时，保存result回调函数到变量loginResult中；
3. 拉起SDK中的登录页面，登录完成后发送notification通知；
4. handleLoginCallback中处理好数据后，调用loginResult返回。
 