# iOS 适配方案

[TOC]

## 背景

在 iOS 实际开发中，经常要适配不同尺寸的屏幕，目前主要适配的屏幕尺寸有4种：
1. `iphone5，5S，SE`的4英寸；
2. `iphone6，7，8`的4.7英寸；
3. `iphone Plus`系列的5.5英寸；
4. `iphone X` 的5.8英寸。
这四种尺寸的跨度还是有点大的，所以需要有个良好的适配方案，才能产出高还原 UI 设计图的APP。

## 基础适配

### xib or 纯代码

1. xib 进行自动布局会比较快和比较直观，当然面对比较复杂的界面，并且布局完成后大量修改布局就很痛苦了；维护非常不方便。

2. 纯代码布局的话可以使用第三方库 `Masonry`，使用纯代码布局方案的话修改复杂布局会比较快，但是开发阶段要耗费的时间也会比较多。

综合以上两种方案的描述，最优解应该是综合 xib和纯代码的优点进行配合适配：

1. 拿到设计图先分析大体结构，按需进行拆分。
2. 拆分得到的每部分视图用 xib 进行布局；xib 布局使用 `AutoLayout` 尽量使用百分比设置控件大小。以保证不同屏幕的显示效果相同。
3. 用 xib 布局好视图在控制器中可以利用 `Masonry` 进行拼装。

这样做的好处是既保证了开发速度，又便于维护代码，即使布局多变也可以快速修改。

### 细节处理

#### 字体适配

* 字体适配现在采用的方案是利用第三方框架`LayoutTool`动态对字体进行适配。

* 使用`LayoutTool`只需把该框架拖进项目 Lib 文件夹即可，因为它的源码文件是利用 runtime 实现字体适配的。

## 针对适配

### 设备（iPhoneX）

#### 导航栏适配

导航栏的偏移问题: 在iOS7之后新增的半透明属性translucent，默认为YES，在iPhoneX上会把导航栏顶到流海上，要解决需要把它设成NO，并且内部控制器要做相应的设配。

```objc
UINavigationBar *bar = [UINavigationBar appearanceWhenContainedIn:self, nil];
[bar setBarTintColor:[UIColor whiteColor]]; // 导航栏颜色
bar.translucent = NO; // 取消半透明
```

iPhone X中NavigationBar高度和TabBar高度都发生了变化：

1. 非iPhone X：StatusBar 高20pt，NavigationBar 高44pt，底部TabBar高49px；
2. iPhone X：StatusBar 高44pt，NavigationBar 高44pt，底部TabBar高83pt；
3. 如果以前项目用的是宏定义64和49，那直接修改宏就可以了，否则只能全局修改这些常量为宏了。

#### iPhone X适配相关宏

适配顶部导航栏和底部TabBar

```objc
// 是否iphoneX
#define isIphoneX ([[UIApplication sharedApplication] 
statusBarFrame].size.height == 44 ? YES : NO)
// 状态栏高度
#define kStatusBarHeight [[UIApplication sharedApplication] statusBarFrame].size.height
// 导航栏高度
#define kNavBarHeight 44
// 顶部栏高度（状态栏+导航栏）
#define kTopHeight (kStatusBarHeight + kNavBarHeight)
// 底部Home键位置高度
#define kBottomHomeHeight (isIphoneX ? 34 : 0)
// 底部栏高度
#define kTabBarHeight (isIphoneX ? 83 : 49)
```

### 系统（iOS11以上）

#### 导航栏适配

在`iOS11`上导航栏的自定义图片`UIBarButton`出现拉伸或位置错乱问题，如果是纯文字`UIBarButton`就不需要修改了,自定义图片需要把自定义的按钮包在UIView里面，显示出来的效果才是正确尺寸

```objc
UIView *contentView = [[UIView alloc] initWithFrame:btn.frame];
[contentView addSubview:btn];
UIBarButtonItem *item = [[UIBarButtonItem alloc] initWithCustomView:contentView];
```

#### ScrollView偏移

解决ScrollView偏移问题, TableView和CollectionView同样适用.

```objc
#define adjustsScrollViewInsets_NO(scrollView, vc)\
do { \
_Pragma("clang diagnostic push") \
_Pragma("clang diagnostic ignored \"-Warc-performSelector-leaks\"") \
if ([UIScrollView instancesRespondToSelector:NSSelectorFromString(@"setContentInsetAdjustmentBehavior:")]) {\
[scrollView   performSelector:NSSelectorFromString(@"setContentInsetAdjustmentBehavior:") withObject:@(2)];\
} else {\
vc.automaticallyAdjustsScrollViewInsets = NO;\
}\
_Pragma("clang diagnostic pop") \
} while (0)
```

