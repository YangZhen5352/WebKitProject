 WebKit(WKNavigationDelegate)
标签： webkitWKWebView
2015-12-02 15:03 2325人阅读 评论(0) 收藏 举报
 分类： WebKit（7）  
版权声明：本文为博主原创文章，未经博主允许不得转载。
目录(?)[+]
WebKit(展示Web界面)

WebKit(WKScriptMessageHandler)

WebKit(WKUIDelegate)

WebKit(WKNavigationDelegate)

WebKit(刷新)

WebKit(导航)

WebKit(浏览记录)

WebKit(进度条)

使用过UIWebView的朋友都知道UIWebViewDelegate这个协议，它能帮助我们监听网页加载的进度，以及错误。

WebKit框架也有这样的协议WKNavigationDelegate，它的功能比UIWebViewDelegate更强，还能监听服务器跳转、身份认证等。

这篇博文为大家带来关于WKNavigationDelegate的介绍。

1 搭建项目

在这里我们不在使用前面的YJBaseVC，而是使用YJSeniorVC类。因为这里开始使用高级模块了。

下面就是YJSeniorVC.m的源代码。

//
//  YJSeniorVC.m
//  WebKit
//
//  CSDN:http://blog.csdn.net/y550918116j
//  GitHub:https://github.com/937447974/Blog
//
//  Created by yangjun on 15/11/29.
//  Copyright © 2015年 阳君. All rights reserved.
//

#import "YJSeniorVC.h"
#import <WebKit/WebKit.h>

/** 屏幕宽度*/
#define UIScreenWeight [[UIScreen mainScreen] bounds].size.width
/** 屏幕高度*/
#define UIScreenHeight [[UIScreen mainScreen] bounds].size.height

@interface YJSeniorVC () <WKNavigationDelegate>

@property (nonatomic, strong) WKWebView *webView;                      ///< WKWebView

@end

@implementation YJSeniorVC

- (void)viewDidLoad {
    [super viewDidLoad];
    // 刷新界面
    NSURL *url = [NSURL URLWithString:@"https://www.baidu.com"];
    NSURLRequest *urlRequest = [NSURLRequest requestWithURL:url];
    [self.webView loadRequest:urlRequest]; // 加载页面
}

#pragma mark - get方法
- (WKWebView *)webView {
    if (_webView == nil) {
        // WKWebView的配置
        WKWebViewConfiguration *configuration = [[WKWebViewConfiguration alloc] init];
        // 显示网页
        CGRect rect = CGRectZero;
        rect.size.width = UIScreenWeight;
        rect.size.height = UIScreenHeight;
        _webView = [[WKWebView alloc] initWithFrame:rect configuration:configuration];
        _webView.navigationDelegate = self; // 代理设置
        [self.view addSubview:_webView];
    }
    return _webView;
}

@end
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
这里已经使用懒加载的方式为大家创建WKWebView，并加载百度首页。大家运行项目即可看见百度首页，如果看不见请检查自己的相关代码和查阅前面的博文。

这里还指向了WKNavigationDelegate，详见

@interface YJSeniorVC () <WKNavigationDelegate>
1
1
并使用

_webView.navigationDelegate = self; // 代理设置
1
1
让_webView的navigationDelegate指向当前类。只是当前类还没有实现WKNavigationDelegate而已。

2 WKNavigationDelegate协议

WKNavigationDelegate协议有两大核心部分，第一部分是导航部分，第二部分是页面内监听。

2.1 导航监听

#pragma mark - WKNavigationDelegate 页面跳转
#pragma mark 在发送请求之前，决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler;

#pragma mark 身份验证
- (void)webView:(WKWebView *)webView didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *__nullable credential))completionHandler;

#pragma mark 在收到响应后，决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationResponse:(WKNavigationResponse *)navigationResponse decisionHandler:(void (^)(WKNavigationResponsePolicy))decisionHandler;

#pragma mark 接收到服务器跳转请求之后调用
- (void)webView:(WKWebView *)webView didReceiveServerRedirectForProvisionalNavigation:(null_unspecified WKNavigation *)navigation;

#pragma mark WKNavigation导航错误
- (void)webView:(WKWebView *)webView didFailNavigation:(null_unspecified WKNavigation *)navigation withError:(NSError *)error;

#pragma mark WKWebView终止
- (void)webViewWebContentProcessDidTerminate:(WKWebView *)webView;
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
2.2 网页监听

#pragma mark - WKNavigationDelegate 页面加载
#pragma mark 页面开始加载时调用
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(null_unspecified WKNavigation *)navigation;

#pragma mark 当内容开始返回时调用
- (void)webView:(WKWebView *)webView didCommitNavigation:(null_unspecified WKNavigation *)navigation;

#pragma mark 页面加载完成之后调用
- (void)webView:(WKWebView *)webView didFinishNavigation:(null_unspecified WKNavigation *)navigation;

#pragma mark 页面加载失败时调用
- (void)webView:(WKWebView *)webView didFailProvisionalNavigation:(null_unspecified WKNavigation *)navigation withError:(NSError *)error;
1
2
3
4
5
6
7
8
9
10
11
12
1
2
3
4
5
6
7
8
9
10
11
12
3 实现WKNavigationDelegate

在YJSeniorVC.m中导入如下方法。

#pragma mark - WKNavigationDelegate 页面跳转
#pragma mark 在发送请求之前，决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler {
    NSLog(@"%s",__FUNCTION__);
    /**
     *typedef NS_ENUM(NSInteger, WKNavigationActionPolicy) {
     WKNavigationActionPolicyCancel, // 取消
     WKNavigationActionPolicyAllow,  // 继续
     }
     */
    decisionHandler(WKNavigationActionPolicyAllow);
}

#pragma mark 身份验证
- (void)webView:(WKWebView *)webView didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *__nullable credential))completionHandler {
    NSLog(@"%s",__FUNCTION__);
    // 不要证书验证
    completionHandler(NSURLSessionAuthChallengeUseCredential, nil);
}

#pragma mark 在收到响应后，决定是否跳转
- (void)webView:(WKWebView *)webView decidePolicyForNavigationResponse:(WKNavigationResponse *)navigationResponse decisionHandler:(void (^)(WKNavigationResponsePolicy))decisionHandler {
    NSLog(@"%s",__FUNCTION__);
    decisionHandler(WKNavigationResponsePolicyAllow);
}

#pragma mark 接收到服务器跳转请求之后调用
- (void)webView:(WKWebView *)webView didReceiveServerRedirectForProvisionalNavigation:(null_unspecified WKNavigation *)navigation {
    NSLog(@"%s",__FUNCTION__);
}

#pragma mark WKNavigation导航错误
- (void)webView:(WKWebView *)webView didFailNavigation:(null_unspecified WKNavigation *)navigation withError:(NSError *)error {
    NSLog(@"%s",__FUNCTION__);
}

#pragma mark WKWebView终止
- (void)webViewWebContentProcessDidTerminate:(WKWebView *)webView {
    NSLog(@"%s",__FUNCTION__);
}

#pragma mark - WKNavigationDelegate 页面加载
#pragma mark 页面开始加载时调用
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(null_unspecified WKNavigation *)navigation {
    NSLog(@"%s",__FUNCTION__);
}

#pragma mark 当内容开始返回时调用
- (void)webView:(WKWebView *)webView didCommitNavigation:(null_unspecified WKNavigation *)navigation {
    NSLog(@"%s",__FUNCTION__);
}

#pragma mark 页面加载完成之后调用
- (void)webView:(WKWebView *)webView didFinishNavigation:(null_unspecified WKNavigation *)navigation {
    NSLog(@"%s",__FUNCTION__);
}

#pragma mark 页面加载失败时调用
- (void)webView:(WKWebView *)webView didFailProvisionalNavigation:(null_unspecified WKNavigation *)navigation withError:(NSError *)error {
    NSLog(@"%s",__FUNCTION__);
    NSLog(@"%@", error.localizedDescription);
}
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
再次运行项目，可在控制台看见相应输出。这样我们能更细腻化的把控WKWebView。 
 

其他

源代码

Objective-C

参考资料

WebKit Framework Reference

WKWebView的新特性与使用

WKWeb​View

文档修改记录

时间	描述
2015-12-02	博文完成
2015-12-12	更改链接
版权所有

CSDN：http://blog.csdn.net/y550918116j

GitHub：https://github.com/937447974/Blog
