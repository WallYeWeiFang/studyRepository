#Android小组2016年9月3号第一次会议
---
参与人员：康胜程（主讲人），王驰，温苏彬，黄奕源

##会议主要内容:
* 项目开发的准则和注意事项
* 安卓开发原则与开发规范

### 项目开发的准则和注意事项
* 职责

	项目开发前对自己的开发能力要清晰，
开发过程中遇到问题应该及时请求帮助，解释清楚原因，包括个人意愿的原因，不要因为自身原因影响到项目的整体进度。

* 压力转化为动力

	* 项目立项时应强调让项目组对接客户那边，开发人员专注开发，以免开发受到客户心情的影响，让项目组对接客户。项目组能够抓住问题的重点与开发人员沟通，后期才与客户直接沟通。
	* 项目间隔时间应该控制好自己的休息时间，建议不要马上准备下一个项目的开发，开发周期定下来应该是合理的，只要保证我们每天开发的进度，如果项目逾期也是有原因的，不要因为开发周期看起来太短就给自己太大压力。
	* 除非情况紧急，遇到对接，在自身的休息时间进行项目开发，尽量保证自己的休息。

### 安卓开发原则
* 尽量不要修改别人或者自己以前的代码（在保证自己代码的开发质量的前提下）修改代码会引发多个问题
	* 代码的耦合性问题导致未知的引用受牵连
	* 遇到疑问的代码应该向原来开发的人提出问题，让他解决或者回答
	* 修改别人的代码会影响别人阅读原来自己的代码。

### 安卓开发规范
---
###git 提交规范
说明哪个类改了什么 
例如：
CheckVersionUtil 恢复权限对话框?
PastRevealAdapter 解决 商品揭晓--往期揭晓---机器人刷单的用户名为空
?Manage 修复邀请精度
?ToShowAdapter 增加6.0读写SD卡权限?
**一次提交内容不宜过多,减少一次提交只修改几行的情况，除非这种情况很紧急**

---
###命名规范和注释规范
* ###包名（ui.activity/ui.frgment.home）
  * 一级（默认包名）
  * 二级
common（工具类，常量类）,domain(网络相关)，
ui(customview,activity,fragment) ，第三方类库
  * 三级
具体名字，类根据此三级名字的层级关系放置


* ###类名（驼峰命名法，默认命名,放在指定包名下面）
创建类说明是谁创建的,就是那种默认的格式，修改的时候添加说明是谁修改的，修改时间，修改了什么（修改什么也可以在提交的时候写）
这种格式 
```
/** * Created by AD on 2016/4/29. */
```
```
/** * Edited by Fred on 2016/5/01. */
```
  * 类名+类的类型
SplashActivity，HomeFragment，StringUtil，WheelCustomView(不要复数s)
  * 类注释
[https://segmentfault.com/a/1190000004189959](https://segmentfault.com/a/1190000004189959)
/** +回车 ，可用于普通注释，方法上方注释可添加参数即声明
http://www.jianshu.com/p/08262d720976

* ###方法名（开头小写，驼峰命名法）
（动词+形容词+名词）
getCountTime()(获取 倒计时的 时间)
toRegisterNext(进入 注册的  下一步)

* ###布局（默认命名）
  * 第一级(一个)
activity,fragment,lvitem
  * 二级(允许多个)具体的类名小写
activity_splash
  * 三级
activity_setting_password,
rvitem_fragent_mall
lvitem_fragent_mall
gvitem_fragent_mall
* ###id和布局

  tv,iv,rv,lv,gv

  ll,rl,fl


* ### color

  黑色
title #333333 black33
content #666666 black66 
hint #999999 black99

  灰色
divider grayE5

  白色用系统资源

  如果遇到多个不同 就全写 如 #F6E5F6 colorF6E5F6

* ### dimen (以autolayout为例就是px)
  marginleft20
  margin20
  paddingleft20
  padding20

---
###代码编写规范
> 1 遵循DRY原则（不重复）
2 不要在回调写View的操作，全部放在主线程
3 注意异步线程执行，线程停止会有延时
4 代码逻辑嵌套，耦合性太高
5 如果可以减少成员变量的使用，尽量使用局部变量

###常量类，工具类
ToastUtil,LogUtil
StringUtil
BroadCastReceiver BroadCastSender Thread
BaseActivity
BaseFagment
BaseTabAdapter
BaseTabItem

### 开发统一按照设计图来
统一字符串和吐司（各种状态）
统一各种色值
字符串统一写在string那里