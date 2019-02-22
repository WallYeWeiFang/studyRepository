| 版本号  | 制定团队      |  更新日期  |备注 |
| ------ |:------------:| --------:|----:|
| 1.0    | 蛋壳Android组 | 18/01/24 |初版 |


**目录**

 [toc]

### 前言

> 对软件来说，适当的规范和标准绝不是消灭代码内容的创造性、优雅性，而是限制过度个性化，以一种普遍认可的统一方式一起做事，提升协作效率。代码的字里行间流淌的是软件生命中的血液，质量的提升是尽可能少踩坑，杜绝踩重复的坑，切实提升质量意识。

 
### Alibaba Java开发规约插件

在以后的Android开发编码中，要求每位Android组成员都必须遵循Java开发规约，强制代码规范，提升代码质量意识。

安装：打开AndroidStudio的 Preferences/Setting >> Plugins >> Browse repositories... 搜索框内输入`alibaba`， 找到`Alibaba Java coding Guidelines`插件安装重启即可使用。

![Alibaba Java coding Guidelines](http://upload-images.jianshu.io/upload_images/1485186-991cd9d06a73955d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Java 编程规约

#### (一).命名风格
1. **【强制】**代码中的命名均不能**以下划线或美元符号**开始，也不能以下划线或美元符号结束。
    * 反例: `_name / __name / $Object / name_ / name$ / Object$`

2. **【强制】**代码中的命名**严禁使用拼音与英文混合**的方式，更**不允许**直接使用**中文**的方式。 
    * 说明: 正确的英文拼写和语法可以让阅读者易于理解，避免歧义。注意，即使纯拼音命名方式 也要避免采用。
    * 正例: `alibaba / taobao / youku / hangzhou` 等国际通用的名称，可视同英文。 
    * 反例: `DaZhePromotion [打折] / getPingfenByName() [评分] / int 某变量 = 3`

3. **【强制】** **类名**使用 **UpperCamelCase** 风格，必须遵从驼峰形式，但以下情形例外:`DO / BO / DTO / VO / AO`
    * 正例: `MarcoPolo / UserDO / XmlService / TcpUdpDeal / TaPromotion` 
    * 反例: `macroPolo / UserDo / XMLService / TCPUDPDeal / TAPromotion`

4. **【强制】** **方法名、参数名、成员变量、局部变量**都统一使用 **lowerCamelCase** 风格，必须遵从 驼峰形式。
    * 正例: `localValue / getHttpMessage() / inputUserId`
    
5. **【强制】** **常量**全部大写，单词间用下划线隔开，力求语义表达完整清楚，不要嫌名字长。 
    * 正例: `MAX_STOCK_COUNT`
    * 反例: `MAX_COUNT`

6. **【强制】** **抽象类**命名使用 **Abstract 或 Base** 开头;**异常类**命名使用 **Exception** 结尾;**测试类**命名以它要测试的类的名称开始，以 **Test** 结尾。

7. **【强制】**中括号是**数组类型**的一部分，数组定义如下:`String[] args;` 
    * 反例:使用`String args[]`的方式来定义。

8. **【强制】** **POJO 类**中布尔类型的变量，都不要加 is，否则部分框架解析会引起序列化错误。 
    * 反例: 定义为基本数据类型`Boolean isDeleted;`的属性，它的方法也是`isDeleted()`，RPC框架在反向解析的时候，“以为”对应的属性名称是 deleted，导致属性获取不到，进而抛出异常。(Fastjson貌似也会)

9. **【强制】**包名统一使用小写，点分隔符之间有且仅有一个自然语义的英语单词。包名统一使用 单数形式，但是类名如果有复数含义，类名可以使用复数形式。
    * 正例: 应用工具类包名为`com.alibaba.open.util`、类名为`MessageUtils`(此规则参考 spring 的框架结构)

10. **【强制】**杜绝完全不规范的缩写，避免望文不知义。
    * 反例: `AbstractClass`“缩写”命名成 `AbsClass;condition`“缩写”命名成 `condi`，此类随意缩写严重降低了代码的可阅读性。

11. 【推荐】为了达到代码自解释的目标，任何自定义编程元素在命名时，使用尽量完整的单词 组合来表达其意。
    * 正例:从远程仓库拉取代码的类命名为 `PullCodeFromRemoteRepository。`
    * 反例:变量 `int a;` 的随意命名方式。

12. 【推荐】如果模块、接口、类、方法使用了设计模式，在命名时体现出具体模式。 说明:将设计模式体现在名字中，有利于阅读者快速理解架构设计理念。 
    * 正例:`public class OrderFactory;
public class LoginProxy; public class ResourceObserver;`

13. 【推荐】接口类中的方法和属性不要加任何修饰符号(public 也不要加)，保持代码的简洁 性，并加上有效的 Javadoc 注释。尽量不要在接口里定义变量，如果一定要定义变量，肯定是 与接口方法相关，并且是整个应用的基础常量。
    * 正例:接口方法签名:`void f()`;接口基础常量表示:`String COMPANY = "alibaba";`
    * 反例:接口方法定义:`public abstract void f();`
    * 说明:JDK8 中接口允许有默认实现，那么这个 default 方法，是对所有实现类都有价值的默认实现。

14. 接口和实现类的命名有两套规则:
    1. **【强制】**对于 Service 和 DAO 类，基于 SOA 的理念，暴露出来的服务一定是接口，内部的实现类用 Impl 的后缀与接口区别。 
        * 正例:`CacheServiceImpl` 实现 `CacheService` 接口。
    2. 【推荐】 如果是形容能力的接口名称，取对应的形容词做接口名(通常是`–able` 的形式)。 
        * 正例:`AbstractTranslator` 实现 `Translatable`。

15. 【参考】**枚举类**名建议带上 `Enum` 后缀，枚举成员名称需要全大写，单词间用下划线隔开。
    * 说明:枚举其实就是特殊的常量类，且构造方法被默认强制是私有。 
    * 正例:枚举名字为`ProcessStatusEnum`的成员名称:`SUCCESS / UNKOWN_REASON`。

16. 【参考】各层命名规约:
    1. Service/DAO层方法命名规约
        1. 获取单个对象的方法用`get`做前缀。
        2. 获取多个对象的方法用`list`做前缀。
        3. 获取统计值的方法用`count`做前缀。
        4. 插入的方法用`save/insert`做前缀。
        5. 删除的方法用`remove/delete`做前缀。
        6. 修改的方法用`update`做前缀。
    2. 领域模型命名规约
        1. 数据对象:`xxxDO`，xxx即为数据表名。
        2. 数据传输对象:`xxxDTO`，xxx为业务领域相关的名称。
        3. 展示对象:`xxxVO`，xxx一般为网页名称。
        4. `POJO`是`DO/DTO/BO/VO`的统称，禁止命名成xxxPOJO。

#### (二).常亮定义

1. **【强制】**不允许任何魔法值(即未经定义的常量)直接出现在代码中。 
    * 反例:`String key = "Id#taobao_" + tradeId;
    cache.put(key, value);`
    
2. **【强制】** long 或者 Long 初始赋值时，使用大写的 L，不能是小写的 l，小写容易跟数字 1 混 淆，造成误解。
    * 说明:`Long a = 2l`; 写的是数字的21，还是Long型的2?

3. 【推荐】不要使用一个常量类维护所有常量，按常量功能进行归类，分开维护。 
    * 说明:大而全的常量类，非得使用查找功能才能定位到修改的常量，不利于理解和维护。 
    * 正例:缓存相关常量放在类 `CacheConsts `下;系统配置相关常量放在类 `ConfigConsts `下。
4. 【推荐】常量的复用层次有五层:跨应用共享常量、应用内共享常量、子工程内共享常量、包 内共享常量、类内共享常量。
    1. 跨应用共享常量:放置在二方库中，通常是`client.jar`中的`constant`目录下。
    2. 应用内共享常量:放置在一方库中，通常是`modules`中的`constant`目录下。
        * 反例:易懂变量也要统一定义成应用内共享常量，两位攻城师在两个类中分别定义了表示 “是”的变量:
</br> 类A中:`public static final String YES = "yes";`
</br> 类B中:`public static final String YES = "y";`
</br>**—> A.YES.equals(B.YES)，预期是 true，但实际返回为 false，导致线上问题。**

    3. 子工程内部共享常量:即在当前子工程的`constant`目录下。
    4. 包内共享常量:即在当前包下单独的`constant`目录下。
    5. 类内共享常量:直接在类内部`private static final`定义。

5. 【推荐】如果变量值仅在一个范围内变化，且带有名称之外的延伸属性，定义为枚举类。下面 正例中的数字就是延伸信息，表示星期几。
    * 正例:`public Enum {MONDAY(1), TUESDAY(2), WEDNESDAY(3), THURSDAY(4),FRIDAY(5), SATURDAY(6), SUNDAY(7);}`

#### (三).代码格式
1. **【强制】**大括号的使用约定。如果是大括号内为空，则简洁地写成{}即可，不需要换行;如果 是非空代码块则:
    1. 左大括号前不换行。
    2. 左大括号后换行。
    3. 右大括号前换行。
    4. 右大括号后还有else等代码则不换行;表示终止的右大括号后必须换行。

2. **【强制】** 左小括号和字符之间不出现空格;同样，右小括号和字符之间也不出现空格。详见 第 5 条下方正例提示。
    * 反例:`if (空格a == b空格)`

3. **【强制】**`if/for/while/switch/do` 等保留字与括号之间都必须加空格。

4. **【强制】**任何二目、三目运算符的左右两边都需要加一个空格。
    * 说明:运算符包括赋值运算符`=`、逻辑运算符`&&`、加减乘除符号等。

5. **【强制】**采用 4 个空格缩进，禁止使用 tab 字符。
    * 说明:如果使用 tab 缩进，必须设置 1 个 tab 为 4 个空格。IDEA 设置 tab 为 4 个空格时， 请勿勾选`Use tab character`;而在 eclipse 中，必须勾选`insert spaces for tabs`。
    * 正例: (涉及1-5点)

    ```java
    public static void main(String[] args) {
        // 缩进 4 个空格
       String say = "hello";
       // 运算符的左右必须有一个空格 
       int flag = 0;
    
       // 关键词 if 与括号之间必须有一个空格，括号内的 f 与左括号，0 与右括号不需要空格 
        if (flag == 0) {
           System.out.println(say);
       }
       
       // 左大括号前加空格且不换行;左大括号后换行
       if (flag == 1) {
           System.out.println("world");
        // 右大括号前换行，右大括号后有 else，不用换行 
        } else {
            System.out.println("ok");
        // 在右大括号后直接结束，则必须换行
        }
    }
    ```

6. **【强制】**注释的双斜线与注释内容之间**有且仅有**一个空格。
    * 正例:// 注释内容，注意在**//**和**注释内容**之间有一个空格。
    
7. **【强制】**单行字符数限制不超过 120 个，超出需要换行，换行时遵循如下原则: 
    1. 第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进，参考示例。
    2. 运算符与下文一起换行。
    3. 方法调用的点符号与下文一起换行。
    4. 方法调用时，多个参数，需要换行时，在逗号后进行。
    5. 在括号前不要换行，见反例。
        * 正例:
    
        ```java
        StringBuffer sb = new StringBuffer();
        // 超过 120 个字符的情况下，换行缩进 4 个空格，点号和方法名称一起换行 
        sb.append("zi").append("xin")...
          .append("huang")...
          .append("huang")...
          .append("huang");
        ```

        * 反例:

        ```java
        StringBuffer sb = new StringBuffer();
        // 超过 120 个字符的情况下，不要在括号前换行
        sb.append("zi").append("xin")...append("huang");
        
        // 参数很多的方法调用可能超过 120 个字符，不要在逗号前换行
        method(args1, args2, args3, ..., argsX);
        ```
    
8. **【强制】**方法参数在定义和传入时，多个参数逗号后边必须加空格。
    * 正例:下例中实参的"a",后边必须要有一个空格。 `method("a", "b", "c");`

9. **【强制】**IDE的`text file encoding`设置为`UTF-8`; IDE中文件的换行符使用`Unix`格式， 不要使用 `Windows` 格式。

10. 【推荐】没有必要增加若干空格来使某一行的字符与上一行对应位置的字符对齐。
    * 正例:

    ```
    int a = 3;
    long b = 4L;
    float c = 5F;
    StringBuffer sb = new StringBuffer();
    ```
    
    * 说明:增加 `sb` 这个变量，如果需要对齐，则给 `a、b、c` 都要增加几个空格，在变量比较多的 情况下，是一种累赘的事情。
    
11. 【推荐】方法体内的执行语句组、变量的定义语句组、不同的业务逻辑之间或者不同的语义 之间插入一个空行。相同业务逻辑和语义之间不需要插入空行。 
    * 说明:没有必要插入多个空行进行隔开。

#### (四).注释规约
1. **【强制】**类、类属性、类方法的注释必须使用 Javadoc 规范，使用/**内容\*/格式，不得使用 // xxx方式。
    * 说明:在 IDE 编辑窗口中，Javadoc 方式会提示相关注释，生成 Javadoc 可以正确输出相应注 释;在 IDE 中，工程调用方法时，不进入方法即可悬浮提示方法、参数、返回值的意义，提高 阅读效率。
    
2. **【强制】**所有的抽象方法(包括接口中的方法)必须要用 Javadoc 注释、除了返回值、参数、 异常说明外，还必须指出该方法做什么事情，实现什么功能。 
    * 说明:对子类的实现要求，或者调用注意事项，请一并说明。

3. **【强制】**所有的类都必须添加创建者和创建日期。

4. **【强制】**方法内部单行注释，在被注释语句上方另起一行，使用//注释。方法内部多行注释使用`/* */`注释，注意与代码对齐。

5. **【强制】**所有的枚举类型字段必须要有注释，说明每个数据项的用途。

6. 【推荐】与其“半吊子”英文来注释，不如用中文注释把问题说清楚。专有名词与关键字保持 英文原文即可。
    * 反例: “TCP 连接超时” 解释成 “传输控制协议连接超时” ，理解反而费脑筋。

7. 【推荐】代码修改的同时，注释也要进行相应的修改，尤其是参数、返回值、异常、核心逻辑 等的修改。
    * 说明:代码与注释更新不同步，就像路网与导航软件更新不同步一样，如果导航软件严重滞后， 就失去了导航的意义。

8. 【参考】谨慎注释掉代码。在上方详细说明，而不是简单地注释掉。如果无用，则删除。
    * 说明:代码被注释掉有两种可能性:
        1. 后续会恢复此段代码逻辑。
        2. 永久不用。前者如果没 有备注信息，难以知晓注释动机。后者建议直接删掉(代码仓库保存了历史代码)。

9. 【参考】对于注释的要求:
    * 第一、能够准确反应设计思想和代码逻辑;
    * 第二、能够描述业务含 义，使别的程序员能够迅速了解到代码背后的信息。完全没有注释的大段代码对于阅读者形同 天书，注释是给自己看的，即使隔很长时间，也能清晰理解当时的思路;注释也是给继任者看 的，使其能够快速接替自己的工作。

10. 【参考】好的命名、代码结构是自解释的，注释力求精简准确、表达到位。避免出现注释的一个极端:过多过滥的注释，代码的逻辑一旦修改，修改注释是相当大的负担。
    * 反例:`// put elephant into fridge put(elephant, fridge);`

        方法名 `put`，加上两个有意义的变量名 `elephant` 和 `fridge`，已经说明了这是在干什么，语义清晰的代码不需要额外的注释。
   
11. 【参考】特殊注释标记，请注明标记人与标记时间。注意及时处理这些标记，通过标记扫描， 经常清理此类标记。线上故障有时候就是来源于这些标记处的代码。

    1. 待办事宜(TODO):( 标记人，标记时间，[预计处理时间]) 表示需要实现，但目前还未实现的功能。这实际上是一个 Javadoc 的标签，目前的 Javadoc还没有实现，但已经被广泛使用。只能应用于类，接口和方法(因为它是一个 Javadoc 标签)。
    2. 错误，不能工作(FIXME):(标记人，标记时间，[预计处理时间])

    在注释中用 FIXME 标记某代码是错误的，而且不能工作，需要及时纠正的情况。


### Android 编程规约

#### (一)构建环境

* Gradle版本(project/gradle/wrapper/gradle-wrapper.properties)

```gradle
distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip
```

* Gradle插件版本 ( project/build.grale )

```gradle
dependencies {
    classpath 'com.android.tools.build:gradle:3.0.1'
}
```

* 构建配置( project/module/build.gradle )

```gradle
android {
    compileSdkVersion 26
    buildToolsVersion "27.0.1"
    defaultConfig {
        minSdkVersion 16
        targetSdkVersion 26
       	...
    }
    ...
}
```

#### (二) 命名风格
##### Android类命名
1. **Application**统一以`DankalApplication` 命名，不能缩成写`DKApp`，也不能缩写成`DKApplication`。
2. **Activity** 命名以Activity结尾
3. **Fragment** 命名以Fragment结尾
4. **Adapter** 命名以Adapter 结尾
5. MVP模式中**Contact**  命名以Contact 结尾
6. **EventBus** 事件 命名以Event结尾


##### XML命名
| res       | 描述           | 例子  |
| ------------- |:-------------:| -----:|
|anim      | 组件_动画 | dialog_enter |
| drawable      | LocalVector | vector_XXX |
| drawable      | 系统Vector | 默认 |
| drawable      | selector | slt_XXX |
| drawable      | 其他 | xml头标签_XXX |
| menu      | menu_XXX | menu_goods |
| mipmap      | ic_XXX(一般100\*100px以下) | ic_add |
| mipmap      | pic_XXX(较大图片) | pic_splash |
| mipmap      | logo_XXX | logo_app/logo_wechat |
| values      | colorXXX | colorDivider/black333 |
| values      | dimen_xxx | dimen_commom_text |
| layout      | activity_xxx | activity_main |
| layout      | fragment_xxx | fragment_home |
| layout      | layout_xxx(复用) | layout_appbar |
| layout      | dialog_xxx | dialog_delete |
| layout      | item_xx_xxx | item_rv_list/item_gv_pic |
| layout      | vs_xxx(viewstub) | vs_goods |


#### View Id
| 控件(全小写) | 描述           | 例子  |
| ---------- |:-------------:| -----:|
| Button  | bt_xxx | bt_login |
|TextView| tv_xxx | tv_name |
|EditText| et_xxx | et_name |
|ImageView| iv_xxx | iv_user |
|ViewStub| vs_xxx | vs_detail |




#### string.xml
无论是在Java Code 中还是在XML中，在屏幕显示的文字都定义在strings.xml中。一方面方便查找修改，另一方面方便以后做国际化。

#### AndroidStudio 字符集
统一使用utf-8 



