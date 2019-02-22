## 引言

- APP采用object-c纯代码开发，未使用故事面板(storyboard)，个别view采用xib（自定义cell可以是xib）。
- 布局我们采用Masonry布局。Masonry是一个轻量级的布局框架 拥有自己的描述语法 采用更优雅的链式语法封装自动布局 简洁明了 并具有高可读性 。非常简单的使用纯代码实现autoLayout布局，是目前非常流行的手工布局框架。
- 使用MVVM的设计模式，显示层更加细节化、可定制化，数据管理清晰化，业务和数据层松解耦。
使用AFNetworking为网络请求引擎，使用ALNetworking封装, 具体配置见 `DKThirdPartyConfig`
- 使用cocoapods进行库的统一管理，使用方便，清晰明了。
- 本地数据库采用YYCache

## 项目版本管理

- 使用Git进行版本管理 目前托管在coding 
- 仓库地址 `git@git.coding.net:dankal_origin/YDCube_iOS.git` 

## 开发环境

- XCode 9.2
- Cocoapods 1.3.1

## Scheme

- YDCube		开发环境 
- YDCubeTest 测试环境

因为支付只申请了开发环境的, 所以如果手机同时安装两个包的时候, 在测试环境支付可能会跳转到开发环境的app中


## 文件夹说明

|文件夹名|说明|
|----|----|
|Controller/ViewController|视图控制器|
|View|视图|
|Model|模型|
|ViewModel|视图模型 (主要用于网络请求和数据处理)|

|文件夹名|说明|
|----|----|
|Activity|活动模块|
|Case|案例展示模块|
|Comment|评论模块(商城/论坛等)|
|Course|教程模块|
|CustomizeRoom|定制间模块|
|Demand|需求大厅模块|  
|Forum|论坛模块|
|Index|首页模块|
|Login|登录模块|
|Main|父类,底层控制器|
|Mall|商城模块|
|Message|个人消息模块|
|News|新闻模块|
|Other|其他模块(第三方,工具类,分类,部分文件资源)|
|Profile|个人模块|
|Search|搜索模块(商城/论坛等)| 

## 简写说明

RQS : 入墙式 

QW : 墙外

PKM : 平开门

YM : 移门

SG : 书柜

XZZ: 写字桌

DSG: 电视柜

## 定制间规则

- 旧官网 : http://inffur.com
- 定制系统算法逻辑.xls
- 综合柜变色问题.pdf
- 模块功能.html
- 提醒模块 _ 印象笔记网页版.htm
- 物品放置 _ 印象笔记网页版.htm

## 定制间代码设计

父控制器 	 `DKCustomRoomContainerController`

- 子控制器父类 `DKCustomRoomController`

	- 入墙式  `DKRQSCustomRoomViewController`
	- 墙外    `DKQWCustomRoomViewController`
	- 平开门  `DKPKMCustomRoomViewController`
	- 移门    `DKYMCustomRoomViewController`
	- 书柜    `DKSGCustomRoomViewController`
	- 写字桌  `DKXZZCustomRoomViewController`
	- 电视柜  `DKDSGCustomRoomViewController`

	> 以上七个柜类定制均继承自`DKCustomRoomController`
- 定制区域父类 `DKCustomAreaView`

	- 入墙式  `DKRQSCustomAreaView`
	- 墙外    `DKQWCustomAreaView`
	- 平开门  `DKPKMCustomAreaView`
	- 移门    `DKYMCustomAreaView`
	- 书柜    `DKSGCustomAreaView`
	- 写字桌  `DKXZZCustomAreaView`
	- 电视柜  `DKDSGCustomAreaView`

- 定制区域内列

	`DKCustomRoomComponentColumn`


消息总线(RACSubject通讯 两个单例)

- `DKCustomChildEvent`  	一般是子控制器通知父控制器的subject
- `DKCustomContainerEvent` 一般是父控制器通知子控制器的subject

父控制器负责

1. 菜单(上下左右)的绘制 动画
2. 菜单的数据获取(父控制器的ViewModel `DKCustomRoomContainerViewModel`)
3. 菜单的隐藏/显示控制
4. 通知子控制器进行对应的操作

子控制器负责

1. 把拖动的任意东西传值给定制区域(`DKCustomRoomAreaView`)
2. 显示控制器(如背板显示隐藏控制器 柜体高度调整控制器等)
3. 通知定制区域显示标尺等操作
4. 更换墙色/地板色 (入墙式)
5. 保存方案(`DKCustomRoomViewModel`协助配合网络请求)

### 常量头文件 (用于储存一些柜体限制数值)

|柜体类型|常量文件|
|-----|----|
|入墙式| `DKRQSConst.h` |
|墙外| `DKQWConst.h`|
|平开门|`DKPKMConst.h`|
|书柜|`DKSGConst.h`|
|电视柜|`DKDSGConst.h`|
|写字桌|`DKXZZConst.h`|

--

重要常量 (位于`YDCube.pch`) 

`#define KFACTOR` 这是用于控制pt与mm的比值

--

### 其他一些单例

1. `DKRQSConfig`  `DKQWConfig` `DKYMConfig` `DKDSGConfig` 这几个单例是用于保存填写的参数, 除了墙外和平开门 其他的柜体在进入定制间之前都会进入`DKCustomRoomSelectSchemeViewController` , 所以网络请求会在`DKCustomRoomSelectSchemeViewModel`里面  其余的就在对应的`DK..StepViewModel`里面
2. `DKSchemeManager` 用于存放当前的方案, 推荐方案, 临时 花色等数据 **初始化柜体的时候需要赋值给里面的属性, 定制间内切换方案的时候需要调用里面的成员方法** 
3. `DKZHGComponentGenerator` 以`CGContext`的方式绘制柜门图片 抽屉图片 移门 背板等 并提供沙盒缓存 缓存匹配方式是以图片名匹配 同个花色同个门板有时候要生成5张图片 目的是**避免花色重复** 所以生成图片的时候图片名后面会带上时间戳
4. `DKCustomRoomViewGenerator` 生成一些控制器. 比如说点击构件的时候下面显示的上下移动, 删除, 关闭 等按钮 这几个按钮合起来为一个大view, 我们称为一个控制器


### 比较重要的类

|类名|作用说明|
|----|----|
|DKComponentTypeConfig|配置构件类型名,图片名,类型标示符 并提供转换方法 **目前构件的唯一标识键是构件类型名(如: 标准衣柜横隔板)** 平时请尽量不要在代码中硬编码某个类型名字符串去判断构件类型等 而是使用该类提供的转换方法比较妥当, 新增加的构件类型也请在这个类里面的`switch case`里面增加|
|DKCustomMenuConfig|配置左右菜单的按钮  顺序 哪个步骤显示哪些按钮等|
|DKCustomroomSizeCalculator|计算板间距离等的类 用在显示标尺和导出数据比较多 有类似需求请尽量不要手写公式计算 调用这个类提供的方法比较妥当|
|DKHoleFitting|孔位贴合 移动构件的最后会调用这个类的方法 通过返回的Y值 使板能够在正确的孔位上 所以如果需要修改移动后的构件的y值 优先修改这个类内的方法|
|DKComponentExportor|导出方案构件信息等 如果以后要修改给后台的方案数据 可以在这里改 具体看头文件注释或者方法实现 (PS:**电视柜和书柜写字桌没有用这个类导出方案, 日后应该需要修改**)|
|DKClosetThreshold|阈值判断 移动构件的过程中会不断调用这个类的阈值判断方法 阻止构件移动到不合适的地方 实现比较复杂 可以配合注释和文档理解|

### 关于柜体花色

**柜体花纹是`DKZHGComponentGenerator `这个类里面裁剪绘制的**


花色的模型是这个 `DKClosetStyle`

```

@property (nonatomic, copy) NSString *color_no; // 花色代号
@property (nonatomic, copy) NSString *color_name; // 花色名
@property (nonatomic, copy) NSString *origin_img_src; // 花色预览图图片地址 
@property (nonatomic, copy) NSString *img_src_row_light; // 横浅色条纹图片地址
@property (nonatomic, copy) NSString *img_src_row_dark; //  横深色条纹图片地址  1024*1024
@property (nonatomic, copy) NSString *img_src_col_light; // 纵浅条纹 把横条纹转90度,看get方法
@property (nonatomic, copy) NSString *img_src_col_dark; // 纵深条纹 同上 看get方法
@property (nonatomic, copy) NSString *is_show; // 是否展示给用户选择
/** 颜色等级 */
@property (nonatomic, copy) NSString *color_level; // 判断使用哪张酒架图片, 深色柜体用浅色的酒架图片, color_level直接和图片名相关
/** 是否是单色 */
@property (nonatomic, copy) NSString *is_monochrome; // 是否是纯色

@property (nonatomic, copy) NSString *material_color; // 比较细的构件直接填充背景的颜色, 比如横隔板竖隔板层板之类 
@property (nonatomic, copy) NSString *alloy_color;// 金属颜色,比如移门的边框色
@property (nonatomic, copy) NSString *border_color; // 边框颜色, 抽屉,综合柜门板,平开门门板等要用这个颜色作为边框
@property (nonatomic, copy) NSString *handle_color; // 把手颜色, 抽屉,综合柜门板,平开门门板等要用这个颜色作为边框
```

为了保证花纹随机性, 平开门每次换花色会生成五张门的图片, 然后给`DKCustomRoomDoor`的`UIImageView`添加`image`的时候,再随机取图片放入.

另外书柜的木板门(木框门和铝合金门没有)是每次进入生成图片的时候会先检查沙盒缓存里够不够五张, 够五张就返回五个`UIImage`回去让他随机选, 不够的话就生成一张图片存进去,最后还是返回个数组回去(可能不够五个)让他随机选

墙外顶柜同书柜门板道理一样

#### 平开门有垂直桌子的时候改变花色

垂直桌子上面盖了一个长方形, 宽度和桌子一样, 高度是4, 加了个边框(边框颜色是当前的`border_color`), 目的是看起来有个水平的桌子, 花色是直接填充背景颜色(后台字段`material_color`)然后外部改变花色的时候要手动调用这个方法去改变那个长方形的边框花色

```
/* 设置垂直桌面上面那块横隔板的花纹 */
- (void)setVerticalDeskBoardColor:(NSString *)colorNo borderColorNo:(NSString *)borderColorNo;
```


### 一些抽出来的分类

|类名|描述|
|----|----|
|DKCustomRoomComponentColumn+ZHG|综合柜相关的操作: 添加构件 添加门板 添加抽屉 更换门图片  移动门的位置 添加/减少门的高度 等等 具体可以看头文件说明|
|DKCustomRoomComponentColumn+Item|添加物品, 一件添加物品等 具体可以看头文件说明|
|DKCustomRoomViewGenerator+CustomRoomAreaView|根据后台返回的数据生成柜体|


`DKCustomRoomViewGenerator+CustomRoomAreaView`中
```
- (NSArray *)areaWithFrame:(CGRect)frame
                    scheme:(DKCustomRoomScheme *)scheme
                beginClick:(void(^)())beginClick
                  endClick:(void(^)(NSString *))endClick
          showControlBoard:(void(^)(UIView *))showControlBoard
                      type:(DKWardrobeType)type`
```

这个方法尤其重要, 他会将后台返回的数据模型生成一个基本的柜体view, 所有的柜体都是基于这个方法返回的view去进行添加修改

生成出来的柜体view的层级结构是

`DKCustomAreaView` = `DKCustomRoomComponentColumn`+`NSArray<DKCustomRoomComponent *>`(外侧板) + `NSArray<DKCustomRoomComponent *>`(内侧板)

`DKCustomRoomComponentColumn` = `NSArray<DKCustomRoomComponent *>`(背板) + `NSArray<DKCustomRoomComponent *>`(构件) + `NSArray<DKCustomRoomComponent *>`(门板--只有综合柜才有) + `NSArray<DKCustomRoomItemView *>`(物品)

### 右边菜单拖拽

右边菜单的cell上都有pan手势, pan手势会传递给`DKCustomRoomContainerController` 然后`DKCustomRoomContainerController(父控制器)` 传递给`DKCustomRoomController` 然后`DKCustomRoomController(父类)` 传递给 `DKCustomAreaView(父类)` 最后`DKCustomAreaView`传递给每个`DKCustomRoomComponentColumn`列的view, 然后每个列接收到数据后会显示可放置和不可放置的颜色区域view, 显示黑黑view表示的当前位置mm的数据, 等等

最后松手放置构件的时候

会调用`addComponentWithType`系列方法  然后进行阈值判断 判断通过进行孔位调整操作 最后才确定位置

### 在列中拖拽构件

- 纵向拖拽控件 和 横向拖拽控件(只有入墙式的中侧板 和 组合的中立板可以横向移动) 的时候需要一个pan手势, 现在这个pan手势位于父类`DKCustomAreaView`中`- initWithFrame:`当中

- 纵向滑动会传值给列, 然后列调用`moveComponent`系列方法, 中间会经历阈值判断, 显示错误弹窗, 显示可滑动区域, 显示当前位置标尺, 最后的松手时进行的位置判断, 孔位调整等操作, 主要是这个方法`- controlComponentWithPoint:(CGPoint)point state:`

- 横向滑动会传值给入墙式的`DKRQSCustomAreaView` 因为中侧板和列是同级的, 所以这个横向拖拽需要在定制区域内进行 主要是这个方法`- moveShubanWithState:`

- 组合(`DKCustomRoomComponentGroup`)内的纵向和横向拖拽是直接由组合控制, 组合身上自带一个pan手势, 所以方向的判断什么的都是由他自己控制的, 还有阈值判断孔位调整等等

### 构件组合

构件组合是一个微缩版定制区域 从菜单拖进去之后中立板右侧会固定为`400mm`, 当拖拽中侧板的时候, 左侧拉大; 

当放置的是最后一列的时候, 整个组合的结构镜像翻转, 左侧固定为`400mm`, 拖拽中侧板的时候, 右侧拉大.

组合内拖拽/孔位贴合/阈值限制基本跟列的逻辑一样

> ps: 只有入墙式的中侧板可以移动
 
### 模块

模块就是重置整个列的结构, 会调用多一次列的`setupProduct`方法, 具体看`- addModuleInfo:`整个方法就好

模块放进去之后要根据规则调整一下内部结构, 这份文档的外面有一份`模块功能.html`说明, 需要看一下.

### 特地需要说明一下生成方案

界面上显示的柜体高度比传值给后台的高度参数之间会差**6mm**  
也就是界面上显示的柜体高度会比较大  所以给后台数据的时候需要减掉6

### 平开门和其他柜体的数据结构

除平开门以外  其他柜体的数据结构都是

`柜体 = 列的数组 + 外侧板数组 + 内侧板数组`

平开门的数据结构分两种 

1. 有桌子
 
	`柜体` = `小柜体数组(数组内只有一个元素)` = `列的数组 + 外侧板数组 + 内侧板数组`

2. 没桌子
 
	`柜体` = `小柜体数组(代码内命名为 viewColumns)` 
	`一个小柜体数组` =  `列的数组 + 外侧板数组 + 内侧板数组`
	
	ps : `小柜体数组`和`列数组`在后台的接口中键名同为`scheme_schemes`
	
3. 在拖动构件和物品, 模块, 组合等等情况下需要判断当前移动的位置究竟是在哪个列上面的时候, 可以通过`- (DKCustomRoomComponentColumn *)findColumnByPoint:(CGPoint)point pkm:(BOOL)pkm`这个方法去得到这个点所在的列(已经处理好了平开门和非平开门的情况了)

### 左菜单和右菜单的类

由于左右菜单是所有柜体复用的，所以放在`DKCustomRoomContainerController`，类的位置在文件夹Common-View-Menu


- `DKCustomMenuConfig`

	这个类为配置各个柜体的左右菜单需要哪些按钮，命名严格要求，对应菜单的图片命名（`ic_customizedroom_%@`) 和 已选图片 （`ic_customizedroom_%@_selected`），左菜单对应event的subject命名相同
 	左菜单为一个二维数组，具体为 `@[@[一级菜单], @[二级菜单]]`
 	右菜单也是二维数组，和左菜单一样，`@[@[第一步骤菜单], @[第二步骤菜单],@[第三步骤菜单]]`
 	左右菜单的配置只与这个类相关

- 左菜单

	左菜单又分为一级和二级菜单
 - 一级菜单为`DKCustomRoomLeftMenu`，因为是复用且每个柜体不同，所以用代码写，点击事件使用的是subject传递出去
 - 二级菜单为`DKCustomRoomLeftMoreMenu`，UI使用`xib`布局，也是复用，主要是给墙面花色和地板花色使用，就一个`collectionView`，使用`subject`和`signal`来传递点击事件和关闭事件

- 右菜单
	
	右菜单一般也分为一级和二级菜单
	- 一级菜单为`DKCustomRoomRightMenu`，`masonry`布局，有分步骤（step），每个步骤会有一些差异，重写了步骤（step）的set方法，一般设置了`step`即可
	- 二级菜单有多种，
		- 一般性的为`DKCustomRoomRightMoreMenu`，UI使用`xib`布局，用一个枚举来区分类型`DKCustomRoomRightMenuType`，并且重写了`type`的`set`方法，根据类型来设置数据源重新加载数据，长按事件通过`longPanSubject`传递出去，点击通过`menuClick`传递
		- 门菜单为`DKRightDoorMenuView`，因为点击之后有三级菜单（二级门菜单，选择不同高度的门），所以分离出来
		- 综合柜花色菜单为`DKCustomRoomZHGColorMenu`，下面可选 门板和抽屉颜色 和 柜体颜色 不同来弹出三级菜单，通过改变约束来弹出
		- 物品菜单为`DKCustomRoomRightItemMenu`，物品菜单有个滚动可点击的菜单，使用`scrollView`，当为饰品的时候使用约束隐藏了


### 普通放置物品

普通放置物品跟放置构件的数据传输路线是一样的, 但是调用的是`addItem`系列方法

### 一键放置物品

#### 综合柜一键放置

方法为`- (void)addZHGItemsFromAllItems:(NSArray *)allItems`，综合柜的物品现在分为两种类型，书（DKCustomRoomItemViewTypeBook）和其他（DKCustomRoomItemViewTypeOther），放置主要是先放书，错开放，然后其他物品从宽到窄放（旧官网逻辑），写字桌和电视柜的田字格则放置固定物品
计算每一块隔板之间的高度距离，只有高度等于295的时候才能放置物品
还要用一个数组来记录是否已经添加过，一般在有足够物品的时候不能重复添加，但是物品不够了就可以重复添加
添加了物品之后还要还要把门移动到图层前面，不能让物品遮挡住门


#### 衣柜一键添加

衣柜一键添加的顺序是先在挂衣杆放衣服，然后在裤抽上放裤子，最后在板上放叠加的物品，

首先横向构件进行排序，从上到下
然后挂衣杆添加衣服，先找到挂衣杆下面的构件，下面的构件的topMm 减去 挂衣杆的topMm 和 挂衣杆的高度heightMm 就是 挂衣杆可以放置最长（height）的衣服的高度，如果下面是组合的话还要分开计算组合左边的挂衣杆可以放置的最长长度和右边的最长长度
挂衣杆放置衣服的顺序是从长到短，并且一个挂衣杆只能挂3件衣服

裤抽只能放裤子和领带，跟挂衣杆差不多，也要找到下面的构件计算最大长度，挂衣杆挂的领带只能最左边一条，最右边一条，最多两条，其他逻辑和挂衣杆差不多

叠加物品，即放在板上的物品，叠加物品是宽度优先，先放宽的再放窄的直到放不下
先计算最大高度，看上面是否有挂衣杆或者裤抽，如果有挂衣杆和裤抽还要计算挂衣杆上面的衣服长度和裤子长度（就只看第一件，因为是从长到短放置的），计算最大高度之后，放置时物品都要比最大高度小
因为入墙式洞口还可以放东西，所以第一个横向构件还要计算洞口距离
鞋子只可以放最下面，并且空间很足（>300mm）的时候不放鞋子
折叠的衣服不能放最下面和最上面

如果有组合，要在组合里面放东西首先是在组合的横板上，要分别计算左边和右边最上面的板上面可以放的物品的最大高度，和普通横隔板一样计算，然后计算里面的横隔板的最大高度
然后在组合最下面放物品，其实是在底板上放东西，但是要拿到组合最后一个构件来计算底板可以放物品的最大高度



### 柜体换位和柜体高度调整

#### 柜体换位
柜体换位的方法写在 `DKCustomAreaView`里面，综合柜（书柜，写字桌，电视柜）复用，由于交换了两列需要交换两列的所有东西，不但是UI还有数据上也要跟着改变，如果点击一下交换 改变UI和数据的话非常麻烦，所以实现思路为直接改变数据，然后使用`scheme`的数据来重新渲染`areaView`
父方法为`- (NSArray *)exchangeWithIndexA:(NSInteger)indexA indexB:(NSInteger)indexB`，在`DKCustomRoomController`，综合柜的`controller`（都继承于`DKCustomRoomController`）都重写了`- (NSArray *)exchangeWithIndexA:(NSInteger)indexA indexB:(NSInteger)indexB`方法，再调用`viewModel`里面的同名方法，再调用`scheme`分类的同名方法，所有逻辑都写在`scheme`的分类`DKCustomRoomScheme+Operation`里面，请看注释

柜体交换逻辑大概为
- 交换 scheme的列数组(`scheme_schemes`)的两个列
- 两个列交换了，因为两个列的高度可能不同，所以要改变两个列两侧的侧板，和中间的侧板（总共3个侧板）的高度，由于写字桌的侧板和普通列的侧板不同，要做特殊处理
- 要重新生成盖板
- `areaView`重新渲染

#### 高度调整

高度调整和柜体换位一样，也是修改数据然后重新渲染界面
高度调整的方法为 `- (void)changeGridWithIndex:(NSInteger)index add:(BOOL)add`，调用顺序和柜体换位一样，如果是向上加一格(add = YES)则调用`- (void)addGridWithIndex:(NSInteger)index`，否则则调用`- (void)minusGridWithIndex:(NSInteger)index`，然后在viewModel里面调用分类`DKCustomRoomScheme+Operation`里面的同名方法

高度调整的逻辑实现大概为：
- 要调整该列的实际高度（单位为mm的高度参数）
- 还要调整该列两边的侧板的距离，如果一个侧板两边的列的高度都降低了，那这个侧板也要降低高度
- 还要把改列里面已经添加的物品调整相应的高度，删除已经超出柜体高度的物品，调整门
- 重新生成盖板

这里涉及到可否调整的问题，所有逻辑都写在`DKCustomRoomScheme+Operation`里面的`- (NSNumber *)columnOperation:(NSInteger)index`方法，一般没什么问题不要修改这个方法




## 项目文件结构

```
.
├── Activity						活动模块
│   ├── Controller
│   ├── Model
│   └── ViewModel
├── Case							案例模块
│   ├── Controller
│   ├── Model
│   ├── View
│   └── ViewModel
├── Comment						评论模块
│   ├── Controller
│   ├── Model
│   ├── View
│   └── ViewModel
├── Course						教程模块
│   ├── Model
│   ├── View
│   ├── ViewController
│   └── ViewModel
├── CustomizeRoom				定制间模块
│   ├── Common					共用类
│   │   ├── Controller
│   │   │   └── Order
│   │   ├── Model
│   │   ├── Util
│   │   │   └── Event
│   │   ├── View
│   │   │   ├── Menu
│   │   │   └── Order
│   │   ├── ViewGenerator
│   │   └── ViewModel
│   ├── DSG	 				  电视柜
│   │   ├── Controller
│   │   ├── Model
│   │   ├── Util
│   │   ├── View
│   │   └── ViewModel
│   ├── PKM					  平开门
│   │   ├── Controller
│   │   ├── Model
│   │   ├── Util
│   │   ├── View
│   │   └── ViewModel
│   ├── QW					 墙外
│   │   ├── Controller
│   │   ├── Util
│   │   ├── View
│   │   │   └── Custom
│   │   └── ViewModel
│   ├── RQS					入墙式
│   │   ├── Controller
│   │   │   └── Step
│   │   ├── View
│   │   │   ├── CustomRoom
│   │   │   └── Step
│   │   └── ViewModel
│   ├── SG					书柜综合柜
│   │   ├── Controller
│   │   ├── Util
│   │   ├── View
│   │   │   └── Custom
│   │   └── ViewModel
│   ├── XZZ					写字桌
│   │   ├── Controller
│   │   ├── Util
│   │   ├── View
│   │   │   └── Custom
│   │   └── ViewModel
│   └── YM					移门
│       ├── Controller
│       │   └── Step
│       ├── Util
│       ├── View
│       └── ViewModel
├── Demand						需求模块
│   ├── Controller
│   │   ├── Publish
│   │   └── Upload
│   ├── Model
│   ├── View
│   └── ViewModel
├── Forum							论坛模块
│   ├── Controller
│   │   ├── Base.lproj
│   │   └── zh-Hans.lproj
│   ├── Model
│   ├── View
│   └── ViewModel
├── Index							首页模块
│   ├── Controller
│   ├── View
│   └── ViewModel
├── Login							登录模块
│   ├── Controller
│   ├── View
│   └── ViewModel
├── Main
│   ├── Controller
│   ├── Model
│   ├── View
│   └── ViewModel
├── Mall							  商城模块
│   ├── Controller
│   │   ├── Category
│   │   ├── Comment
│   │   ├── Detail
│   │   ├── Order
│   │   ├── ShoppingCart
│   │   └── Supplier
│   ├── Model
│   ├── View
│   │   ├── Category
│   │   ├── Comment
│   │   ├── Detail
│   │   ├── Index
│   │   ├── Order
│   │   ├── ShoppingCart
│   │   └── Supplier
│   └── ViewModel
├── Message							信息模块
│   ├── Controller
│   ├── Model
│   ├── View
│   │   └── Demand
│   └── ViewModel
├── News								新闻模块
│   ├── Controller
│   ├── Model
│   ├── View
│   └── ViewModel
├── Other
│   ├── BGLogation					定位  用于退出后台的时候服务器不会关掉(这个服务器是用来看webGL的, 在app启动的时候就会启动服务器)
│   ├── Category
│   ├── DKProgressHUD				HUD弹窗
│   │   └── DKProgressHUD.bundle
│   ├── DataCenter					持久化个人信息
│   ├── Lib
│   │   ├── ALNetworking
│   │   │   └── ALNetworking.bundle
│   │   ├── AliPay		
│   │   │   └── AlipaySDK.framework
│   │   │       ├── Headers
│   │   │       └── en.lproj
│   │   ├── DKBanner				轮播图
│   │   ├── DKDropDownMenu			下拉菜单
│   │   ├── DKExtension				实用分类
│   │   ├── DKLoginTool				第三方登录
│   │   ├── DKPayTool				支付
│   │   ├── DKRefresh				下拉刷新上拉加载
│   │   ├── DKSearchHistory		储存历史搜索记录
│   │   ├── DKTouchID				没用
│   │   ├── Fir						检查app版本(测试包)
│   │   ├── IQKeyboardManager		键盘控制
│   │   │   ├── Categories
│   │   │   ├── Constants
│   │   │   ├── IQTextView
│   │   │   ├── IQToolbar
│   │   │   └── Resources
│   │   │       └── IQKeyboardManager.bundle
│   │   │           ├── de.lproj
│   │   │           ├── en.lproj
│   │   │           ├── es.lproj
│   │   │           ├── fr.lproj
│   │   │           ├── zh-Hans
│   │   │           └── zh-Hant
│   │   ├── MJPhotoBrowser				查看大图
│   │   │   └── MJPhotoBrowser.bundle
│   │   ├── Translate	
│   │   ├── XHStarRateView				评价星星
│   │   ├── YZDisplayViewController	类似安卓fragment的界面
│   │   ├── YZTagList			标签view 用在很多地方 比如历史搜索
│   │   ├── ZFChart				图表 用在商品详情里的历史价格
│   │   │   ├── Category
│   │   │   ├── Component
│   │   │   ├── Model
│   │   │   ├── Other
│   │   │   ├── ZFBarChart
│   │   │   ├── ZFCirqueChart
│   │   │   ├── ZFGenericAxis
│   │   │   ├── ZFGenericChart
│   │   │   ├── ZFHorizontalAxis
│   │   │   ├── ZFHorizontalBarChart
│   │   │   ├── ZFLineChart
│   │   │   ├── ZFPieChart
│   │   │   ├── ZFRadarChart
│   │   │   └── ZFWaveChart
│   │   ├── ZFPlayer
│   │   │   ├── Category
│   │   │   └── ZFPlayer.bundle
│   │   └── popAnimation
│   ├── SDCycleScrollView
│   │   └── PageControl
│   ├── View
│   └── gif
├── Profile								个人中心模块
│   ├── Controller
│   │   ├── Address
│   │   ├── Designer
│   │   ├── Info
│   │   ├── MyActivity
│   │   ├── MyCase
│   │   ├── MyCollection
│   │   ├── MyDemand
│   │   ├── MyOrder
│   │   ├── MyPost
│   │   ├── Setting
│   │   └── Wallet
│   ├── Model
│   ├── View
│   │   └── Wallet
│   └── ViewModel
└── Search							搜索模块
    ├── Controller
    ├── View
    └── ViewModel
```

### 定制间文件夹说明

|文件夹名|描述|
|----|----|
|Step|表示进入定制间前面的设定的步骤 在这个步骤里输入的参数会进入对应的DK...Config单例 然后下个控制器直接拿出单例里的值拼成请求参数给后台生成方案 |
|Util|跟这个柜体直接相关的工具类|

### 进入定制间点击菜单时显示的gif图片

设定是  **每个柜体刚进入的时候,点击拖拽类菜单(如: 构件菜单)和点击点击类菜单(如 :更换花色菜单)的时候显示一遍 之后不再显示, 不同柜体互不影响**

弹出来的gif图在`DKCustomRoomGuideView`实现

这部分的使用了本地持久化的方式进行记录的, 具体实现在`DKCustomRoomGuideHelper`中  