| 版本号  | 制定团队      |  更新日期  |备注 |
| ------ |:------------:| --------:|----:|
| 1.0    | 蛋壳Android组 | 18/01/26 |初版 |


## Android 适配方案

**目录**

[toc]

先大小分辨率手机测试

### Android 中的单位和名词

#### px（pixel，像素）
就是一个个的像素点，图像的最小组成单元

#### dp (dip，密度无关像素)
**Density-independent pixel**，抽象意义上的像素，与设备的实际物理像素点无关， 可以保证在不同的像素密度的设备上显示相同的效果，也是Android独有的长度单位。

1dp表示在屏幕像素密度为160dpi的屏幕上，1dp = 1px； 
类推，在320dpi上，1dp = 2px，不难看出这样的转换公式：px = dp * (dpi / 160)

#### sp (sip，独立比例像素)
**scale-independent pixel**，字体大小专用单位，**会根据系统设置的字体大小进行缩放，推荐使用**12sp以上的字体(12sp以下太小)，不推荐用奇数和小数，容易造成 精度丢失问题。

> Tip: 尽管官方建议我们字体都用sp作为单位，但是sp字体会根据系统设置的字体大小进行 缩放，假如用户修改了系统字体大小，可能会导致我们APP的UI受到影响，比如字体大了，然后各种显示不全，建议使用dp来做为字体单位！

#### 屏幕尺寸

手机对角线的物理尺寸，单位：英寸（inch），1英寸=2.54cm

Android手机常见的尺寸有5寸、5.5寸、6寸等等

#### 屏幕分辨率
手机在横向、纵向上的像素点数总和

一般描述成屏幕的"宽x高”=AxB
屏幕在横向方向（宽度）上有A个像素点，在纵向方向（高）有B个像素点
例如，1080x1920，即宽度方向上有1080个像素点，在高度方向上有1920个像素点

单位：px（pixel），1px=1像素点


#### 屏幕像素密度

每英寸的像素点数，单位：dpi（dots per ich）

假设设备内每英寸有160个像素，那么该设备的屏幕像素密度=160dpi


安卓手机对于每类手机屏幕大小都有一个相应的屏幕像素密度：

|密度类型|代表的分辨率（px）|屏幕像素密度（dpi）|换算（px/dp）|比例|
| ------ |:------------:| :--------:|:--------:|:--------:|
| 低密度（ldpi）   | 240x320 | 120 |1dp=0.75px |3 |
| 中密度（mdpi）   | 320x480 | 160 |1dp=1px |4 |
| 高密度（hdpi）   | 480x800 | 240 |1dp=1.5px |6 |
| 超高密度（xhdpi）| 720x128 | 320 |1dp=2px |8 |
| 超超高密度（xxhdpi）| 1080x1920 | 480 |1dp=3px |12 |

#### 屏幕尺寸、分辨率、像素密度三者关系

![屏幕尺寸.png](http://upload-images.jianshu.io/upload_images/1485186-0f7cc273126072af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 屏幕适配问题的本质
使得“布局”、“用户界面流程”、“布局组件”、“图片资源”匹配不同的屏幕尺寸

> 使得布局、布局组件自适应屏幕尺寸；
根据屏幕的配置来加载相应的UI布局、用户界面流程


#### “布局”匹配
##### 使得布局元素自适应屏幕尺寸

线性布局（Linearlayout）、相对布局（RelativeLayout）和帧布局（FrameLayout）需要根据需求进行选择，但要记住：

* RelativeLayout
布局的子控件之间使用相对位置的方式排列，因为RelativeLayout讲究的是相对位置，即使屏幕的大小改变，视图之前的相对位置都不会变化，与屏幕大小无关，灵活性很强
* LinearLayout
通过多层嵌套LinearLayout和组合使
用"wrap_content"和"match_parent"已经可以构建出足够复杂的布局。但是LinearLayout无法准确地控制子视图之间的位置关系，只能简单的一个挨着一个地排列

    **所以，对于屏幕适配来说，使用相对布局（RelativeLayout）将会是更好的解决方案**

##### 根据屏幕的配置来加载相应的UI布局

做法：使用限定符

作用：通过配置限定符使得程序在运行时根据当前设备的配置（屏幕尺寸）自动加载合适的布局资源

限定符类型：

* 最小宽度（Smallest-width）限定符(适配平板)
* 屏幕方向（Orientation）限定符

###### 最小宽度（Smallest-width）限定符(适配平板)

通过指定某个最小宽度（以 dp 为单位）来精确定位屏幕从而加载不同的UI资源

使用场景：

```
你需要为标准 7 英寸平板电脑匹配双面板布局（其宽度大于 600 dp），
在手机（较小的屏幕上）匹配单面板布局
```

解决方案：

```
使用上文中所述的单面板和双面板这两种布局，但应使用 sw600dp 指
明双面板布局仅适用于最小宽度为 600 dp 的屏幕，而不是使用 large 尺寸
限定符(Android 3.2以前)。
```

* 说明：`sw xxxdp`，即`small width`的缩写，其不区分方向，即无论是宽度还是高度，只要大于 xxxdp，就采用次此布局。
* 例子：使用了`layout-sw600dp`的最小宽度限定符，即无论是宽度还是高度，只要大于`600dp`，就采用`layout-sw600dp`目录下的布局

###### 屏幕方向（Orientation）限定符
可为resources设置bool，通过获取其值来动态判断目前已处在哪个适配布局

res/values-sw600dp-land/layouts.xml

```
<resources>
    //布局别名
    <item name="main_layout" type="layout">@layout/twopanes</item>
    <bool name="has_two_panes">true</bool>
</resources>
```

#### “用户界面流程”匹配
如果应用处于双面板模式下，点击左侧面板上的项即可直接在右侧面板上显示相关内容；而如果该应用处于单面板模式下，点击相关的内容应该跳转到另外一个Activity进行后续的处理。

**根据屏幕的配置来加载相应的用户界面流程**
 
* 步骤1：确定当前布局

    我们可以先了解用户所处的是“单面板”模式还是“双面板”模式。要做到这一点，可以通过查询指定视图是否存在以及是否已显示出来。
    
    ```java
    public class NewsReaderActivity extends FragmentActivity {
      boolean mIsDualPane;
    
      @Override
      public void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.main_layout);
    
          View articleView = findViewById(R.id.article);
          mIsDualPane = articleView != null &&
                          articleView.getVisibility() == View.VISIBLE;
      }
    }
    ```
    

* 步骤2：根据当前布局做出响应
有些操作可能会因当前的具体布局而产生不同的结果。

    例如，用户界面处于双面板模式下，那么点击左边面板Item就会在右侧面板中打开相应内容；但如果用户界面处于单面板模式下，那么上述操作就会启动一个独立Activity：

    ```java
    public void OnItemClick(int index) {
      if (mIsDualPane) {
          /* display article on the right pane */
    mArticleFragment.displayArticle(mCurrentCat.getArticle(index));
      } else {
          /* start a separate activity */
          Intent intent = new Intent(this, ArticleActivity.class);
          intent.putExtra("catIndex", mCatIndex);
          intent.putExtra("artIndex", index);
          startActivity(intent);
      }
    }
    ```

* 步骤3：重复使用其他Activity中的片段多屏幕设计中的重复模式是指，对于某些屏幕配置，已实施界面的一部分会用作面板；但对于其他配置，这部分就会以独立Activity的形式存在。

    例如，在示例中，对于较大的屏幕，内容会显示在右侧面板中；但对于较小的屏幕，这些内容就会以独立Activity的形式存在。

    在类似情况下，通常可以在多个Activity中重复使用相同的 Fragment 子类以避免代码重复。例如，在双面板布局中使用了 ArticleFragment：

    ```
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="horizontal">
        <fragment android:id="@+id/headlines"
                  android:layout_height="fill_parent"
                  android:name="com.example.android.newsreader.HeadlinesFragment"
                  android:layout_width="400dp"
                  android:layout_marginRight="10dp"/>
        <fragment android:id="@+id/article"
                  android:layout_height="fill_parent"
                  android:name="com.example.android.newsreader.ArticleFragment"
                  android:layout_width="fill_parent" />
    </LinearLayout>
    ```


    然后又在小屏幕的Activity布局中重复使用了它 ：

    ```
    ArticleFragment frag = new ArticleFragment();
    getSupportFragmentManager().beginTransaction()
                               .add(android.R.id.content, frag)
                               .commit();
    ```

* 步骤3：处理屏幕配置变化
    如果我们使用独立Activity实施界面的独立部分，那么请注意，我们可能需要对特定配置变化（例如屏幕方向的变化）做出响应，以便保持界面的一致性。
    
    例如，在运行 Android 3.0 或更高版本的标准 7 英寸平板电脑上，如果示例应用运行在纵向模式下，就会在使用独立Activity显示内容；但如果该应用运行在横向模式下，就会使用双面板布局。

    也就是说，如果用户处于纵向模式下且屏幕上显示的是用于原本处于横向用于显示内容的Activity，那么就需要在检测到屏幕方向变化（变成横向模式）后执行相应操作，即停止上述Activity并返回主Activity，以便在双面板布局中显示相关内容：

    ```
    public class ArticleActivity extends FragmentActivity {
        int mCatIndex, mArtIndex;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            mCatIndex = getIntent().getExtras().getInt("catIndex", 0);
            mArtIndex = getIntent().getExtras().getInt("artIndex", 0);
    
            // If should be in two-pane mode, finish to return to main activity
            if (getResources().getBoolean(R.bool.has_two_panes)) {
                finish();
                return;
            }
            ...
    }
    ```

#### “布局组件”匹配

> 使得布局组件自适应屏幕尺寸

做法：使用"wrap_content"、"match_parent"和"weight“来控制视图组件的宽度和高度，替代硬编码的方式定义视图大小&位置，你的视图要么仅仅使用了需要的那边一点空间，要么就会充满所有可用的空间，即按需占据空间大小，能让你的布局元素充分适应你的屏幕尺寸。

#### “图片资源”匹配

> 使得图片资源在不同屏幕密度上显示相同的像素效果

* 使用Nine-Patch的图片类型，.9图的作用是：拉伸的时候特定的区域不会发生图片失真，而不失真的区域 可以由我们自己绘制，从而实现图片适配。
* 一些纯色的矩形，圆角，圆都可以通过编写shape文件来替换，比起png， xml文件小太多。
* 使用SVG矢量图替换位图:通过「XML文件」来定义一个图形，通过一些特定的语法和规则来绘制 出我们所需的图像，而不是位图那样通过存储图像中每一点的像素值来保存与使用图形。详细使用请[百度](https://www.baidu.com/s?wd=Android%20Vector%E7%9A%84%E4%BD%BF%E7%94%A8&rsv_spt=1&rsv_iqid=0xbde450d6000003be&issp=1&f=8&rsv_bp=0&rsv_idx=2&ie=utf-8&tn=baiduhome_pg&rsv_enter=0&rsv_sug3=6&rsv_t=102f2cuoYlsBJ1r8otiSVLBSrAkxAG1vUN9P5FD4yJSiKzLOuDql%2BRdlDWhp%2FwZGU0BN&inputT=7585&rsv_sug4=7585)

    但是！
    > * 1.适用于Android 5.0以上，尽管官方有兼容包，低版本还是会有些问题的！
    > * 2.不适合细节过于复杂的图片！
    > * 3.因为是用到的时候才画，所以加载图片所消耗的时间和资源可能会增加。

* 备用位图（符合屏幕尺寸的图片资源）
    由于 Android 可在各种屏幕密度的设备上运行，因此我们提供的位图资源应该始终可以满足各类密度的要求。
    
    * 步骤1：根据以下尺寸范围针对各密度生成相应的图片。

        比如说，如果我们为 xhdpi 设备生成了 200x200 px尺寸的图片，就应该按照相应比例地为 hdpi、mdpi 和 ldpi 设备分别生成 150x150、100x100 和 75x75 尺寸的图片。
        **即一套分辨率=一套位图资源（这个当然是Ui设计师做了）**

    * 步骤2：将生成的图片文件放在 res/ 下的相应子目录中(mdpi、hdpi、xhdpi、xxhdpi)，系统就会根据运行您应用的设备的屏幕密度自动选择合适的图片

    * 步骤3：通过引用 @drawable/id，系统都能根据相应屏幕的 屏幕密度（dpi）自动选取合适的位图。
注：

    > 如果是.9图或者是不需要多个分辨率的图片，放在drawable文件夹即可对应分辨率的图片要正确的放在合适的文件夹，否则会造成图片拉伸等问题。

### 解决方案

了解以上知识点，已经能适配大部分适配问题，但要做到更完美的适配我们还需要：

使用dimens适配，这种适配方式的优点很明显,不用管什么dp还是dpi这些东西,只需要以一种屏幕分辨率为基准(例如1280x720,相当于把屏幕宽分成720份,高分成1280份),生成对应屏幕分辨率的的dimens文件即可完成适配,缺点也比较明显,就是一种分辨率就需要一套dimens文件,所以dimens文件会比较多;

例如这里我们以1280x720的屏幕分辨率为基准那么values-1280x720文件夹下面的dimens.xml文件如下：

```xml
<dimen name="x1">1px</dimen>  
<dimen name="x2">2px</dimen>  
<dimen name="x3">3px</dimen>  
<dimen name="x4">4px</dimen>  
<!--省略若干行...-->  
<dimen name="x1279">1279px</dimen>  
<dimen name="x1280">1280px</dimen>  
```

那么values-1920x1080文件夹下面的dimens.xml文件就应该是这样的：

```xml
<dimen name="x1">1.5px</dimen>  
<dimen name="x2">3px</dimen>  
<dimen name="x3">4.5px</dimen>  
<dimen name="x4">6px</dimen>  
<!--省略若干行...-->  
<dimen name="x1279">1918.5px</dimen>  
<dimen name="x1280">1920px</dimen> 
```

**在适配过程中比较常见的问题是虚拟按键的问题,这里重点说一下：**

有些手机有虚拟按键,例如华为的很多手机都有,有些手机没有,有虚拟按键的手机在适配过程中会出现一些问题。

我们可以通过以下两个方法得到手机的分辨率高度和手机去除虚拟按键的高度,两者相减就是手机的虚拟按键的高度。

```java
    /** 
     * @param context 
     * @return 获取屏幕原始尺寸高度，包括虚拟功能键高度 
     */  
    public static int getTotalHeight(Context context) {  
        int dpi = 0;  
        WindowManager windowManager = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);  
        Display display = windowManager.getDefaultDisplay();  
        DisplayMetrics displayMetrics = new DisplayMetrics();  
        @SuppressWarnings("rawtypes")  
        Class c;  
        try {  
            c = Class.forName("android.view.Display");  
            @SuppressWarnings("unchecked")  
            Method method = c.getMethod("getRealMetrics", DisplayMetrics.class);  
            method.invoke(display, displayMetrics);  
            dpi = displayMetrics.heightPixels;  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
        return dpi;  
    }  
```

```
/** 
 * @param context 
 * @return 获取屏幕内容高度不包括虚拟按键 
 */  
public static int getScreenHeight(Context context) {  
    WindowManager wm = (WindowManager) context  
            .getSystemService(Context.WINDOW_SERVICE);  
    DisplayMetrics outMetrics = new DisplayMetrics();  
    wm.getDefaultDisplay().getMetrics(outMetrics);  
    return outMetrics.heightPixels;  
}  
```

那么一款屏幕为2560x1440的手机，减去虚拟按键高度后的分辨率可能为：2408x1440。而在我们生成的屏幕分辨率的dimens文件中并没有这个分辨率的dimens文件。**只要再生成对应的dimens文件即可适配。**

但是这种适配方式带来的缺点也很明显：适配文件太多，会增加apk大小，光是适配xml文件就有1.4M。分辨率其实就那么多，但是有些厂家手机上面标着是常见分辨率实际并不是那么样，而且万一碰到个奇形怪状的手机（注意不是手机分辨率），不能适配是小事，如果不处理有可能报错闪退。


* 减少dimens文件的方法**[不推荐]**：

    对于一个主流的分辨率只要留虚拟按键高度最高的那组dimens文件即可,什么意思呢?比如说1920x1080分辨率,有多款机型都是这个分辨率,只不过是虚拟按键高度不同,你可能需要创建1788x1080,1812x1080,1776x1080...等多套dimens文件,其实大可不必,只需要1776x1080这一套就够了,因为系统找不到对应尺寸的dimens文件,会使用比它略小的分辨率的dimens文件,如此一来我们的dimens文件会大大减少的。
    
* 解决分辨率不匹配导致的闪退问题：

    values文件夹下面最好也创建一个默认的dimens文件(若是找不到对应分辨率的dimens文件,系统默认使用该dimens文件,内容使用720×1280那两个dimens文件lay_x 和lay_y放到values下面。)

##### autolayout.jar的使用

默认情况下，双击即可生成，内置了常用的分辨率，默认基准为480*320，当然对于特殊需求，通过命令行指定即可：

> 例如：基准 1280 * 800 ，额外支持尺寸：1152 * 735；4500 * 3200；
> 按照 java -jar autolayout.jar width height width,height_width,height
> 即：java -jar autolayout.jar 800 1280 735,1152_3200,4500



