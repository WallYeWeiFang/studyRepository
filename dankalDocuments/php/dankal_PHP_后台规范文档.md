# PHP后台编码规范 #
## 编写人：json   
## 编写日期：2018-1-22 ##

正文：  
## 一、基本约定 ##
1、
`源文件`  
（1）、纯PHP代码源文件只使用 <?php 标签，省略关闭标签 ?> ；

（2）、源文件中PHP代码的编码格式必须是无BOM的UTF-8格式；

（3）、使用 Unix LF(换行符)作为行结束符；

（4）、一个源文件只做一种类型的声明，即，这个文件专门用来声明Class, 那个文件专门用来设置配置信息，别混在一起写；  
2、`缩进`  
使用Tab键来缩进，每个Tab键长度设置为4个空格；  
3、`行`
一行推荐的是最多写120个字符，多于这个字符就应该换行了，一般的编辑器是可以设置的。  
4、`关键字 和 True/False/Null`  
PHP的关键字，必须小写，boolean值：true，false，null 也必须小写。

下面是PHP的“关键字”，必须小写：




    '__halt_compiler', 'abstract', 'and', 'array', 'as', 'break', 'callable', 'case', 'catch', 'class', 'clone', 'const', 'continue', 'declare', 'default', 'die', 'do', 'echo', 'else', 'elseif', 'empty', 'enddeclare', 'endfor', 'endforeach', 'endif', 'endswitch', 'endwhile', 'eval', 'exit', 'extends', 'final', 'for', 'foreach', 'function', 'global', 'goto', 'if', 'implements', 'include', 'include_once', 'instanceof', 'insteadof', 'interface', 'isset', 'list', 'namespace', 'new', 'or', 'print', 'private', 'protected', 'public', 'require', 'require_once', 'return', 'static', 'switch', 'throw', 'trait', 'try', 'unset', 'use', 'var', 'while', 'xor'
5、`命名`  
（1）、类名 使用大驼峰式（StudlyCaps）写法；

（2）、（类的）方法名 使用小驼峰（cameCase）写法；

（3）、函数名使用 小写字母 + 下划线 写法，如 function http_send_post()； 

（4）、变量名 使用小驼峰写法，如 $userName；  
6、`代码注释标签`  
如 函数注释、变量注释等，常用标签有 @package、@var、@param、@return、@author、@todo、@throws

必须遵守 phpDocument 标签规则，不要另外去创造新的标签，更多标签查看 [phpDocument官网](https://phpdoc.org/docs/latest/references/phpdoc/tags/param.html)
7、`业务模块`  
（1）、涉及到多个数据表 更新/添加 操作时，最外层要用事务，保证数据库操作的原子性；

（2）、Model层，只做简单的数据表的查询；

（3）、业务逻辑统一封装到 Logic层；

（4）、控制器只做URL路由，不要当作 业务方法 调用；

（5）、控制器层不能出现SQL操作语句，如 ThinkPHP框架的 where()、order() 等模型方法，

即，控制器中，不要出现类似这样的SQL语句：D('XXX')->where()->order()->limit()->find();  

where()、order()、limit() 等SQL方法只能出现在 Model层、业务层！
# 二、代码样式风格 #
## 1、命名空间(Namespace) 和 导入(Use)声明 ##
先简单文字描述下：

命名空间(namespace)的声明后面必须有一行空行；  
所有的导入(use)声明必须放在命名空间(namespace)声明的下面；  
一句声明中，必须只有一个导入(use)关键字；  
在导入(use)声明代码块后面必须有一行空行；  
用代码来说明下：

	<?php
	namespace Lib\Databases; // 下面必须空格一行
	 
	class Mysql {
	 
	}  
声明代码块后面必须有一行空行；  
namespace下空一行，才能使用use，再空一行，才能声明class  

	<?php
	namespace Lib\Databases; // 下面必须空格一行
	 
	use FooInterface; // use 必须在namespace 后面声明
	use BarClass as Bar;
	use OtherVendor\OtherPackage\BazClass; // 下面必须空格一行
	 
	class Mysql {
	 

	}  
## 2、类(class)，属性(property)和方法(method) ##  
### （1）、继承(extends) 和实现(implement) 必须和 class name 写在一行。  

	<?php
	namespace Lib\Databaes;
	 
	class Mysql extends ParentClass implements \PDO, \DB { // 写一行
	 
	}
### （2）、属性(property)必须声明其可见性，到底是 public 还是 protected 还是 private，不能省略，也不能使用var, var是php老版本中的什么方式，等用于public。 ###

	<?php
	namespace Lib\Databaes;
	 
	class Mysql extends ParentClass implements \PDO, \DB { // 写一行
	    public $foo = null;
	    private $name = 'yangyi';
	    protected $age = '17';
	}
### （3）、方法(method)，必须 声明其可见性，到底是 public 还是 protected 还是 private，不能省略。如果有多个参数，第一个参数后紧接“,” ，再加一个空格：function_name ($par, $par2, $pa3), 如果参数有默认值，“=”左右各有一个空格分开。


	<?php
	namespace Lib\Databaes;
	 
	class Mysql extends ParentClass implements \PDO, \DB { // 写一行
	    public getInfo($name, $age, $gender = 1) { // 参数之间有一个空格。默认参数的“=”左右各有一个空格，) 与 { 之间有一个空格
	 
	    }
	}
### （4）、当用到抽象(abstract)和终结(final)来做类声明时，它们必须放在可见性声明 （public 还是protected还是private）的前面。而当用到静态(static)来做类声明时，则必须放在可见性声明的后面。 ###

直接上代码：

	<?php
	namespace Vendor\Package;
	 
	abstract class ClassName {
	    protected static $foo; // static放后面
	    abstract protected function zim(); // abstract放前面
	 
	    final public static function bar() { // final放前面，static放最后。
	        // 方法主体部分
	    }
	}
## 3、控制结构 ##
### 控制结构，就是 if else while switch等。这一类的写法规范也是经常容易出现问题的，也要规范一下。 ###

### （1）、if，elseif，else写法，直接上规范代码吧： ###

	<?php
	if ($expr1) { // if 与 ( 之间有一个空格，) 与 { 之间有一个空格
	 
	} elseif ($expr2) { // elesif 连着写，与 ( 之间有一个空格，) 与 { 之间有一个空格
	 
	} else { // else 左右各一个空格
	 
	}
### （2）、switch，case 注意空格和换行，还是直接上规范代码： ###


	<?php
	switch ($expr) { // switch 与 ( 之间有一个空格，) 与 { 之间有一个空格
	    case 0:
	        echo 'First case, with a break'; // 对齐
	        break; // 换行写break，也对齐。
	    case 1:
	        echo 'Second case, which falls through';
	        // no break
	    case 2:
	    case 3:
	    case 4:
	        echo 'Third case, return instead of break';
	        return;
	    default:
	        echo 'Default case';
	        break;
	}
### （3）、while，do while 的写法也是类似，上代码： ###

	<?php
	while ($expr) { // while 与 ( 之间有一个空格， ) 与 { 之间有一个空格
	 
	}
	 
	do { // do 与 { 之间有一个空格
	 
	} while ($expr); // while 左右各有一个空格
### （4）、for的写法 ###
	
	<?php
	for ($i = 0; $i < 10; $i++) { // for 与 ( 之间有一个空格，二元操作符 "="、"<" 左右各有一个空格，) 与 { 之间有一个空格
	 
	}
### （5）、foreach的写法 ###


	<?php
	foreach ($iterable as $key => $value) { // foreach 与 ( 之间有一个空格，"=>" 左右各有一个空格，) 与 { 之间有一个空格
	 
	}
### （6）、try catch的写法 ###


	<?php
	try { // try 右边有一个空格
	 
	} catch (FirstExceptionType $e) { // catch 与 ( 之间有一个空格，) 与 { 之间有一个空格
	 
	} catch (OtherExceptionType $e) { // catch 与 ( 之间有一个空格，) 与 { 之间有一个空格
	 
	}
## 4、注释 ##
### （1）、行注释 ###

#### // 后面需要加一个空格； ####

#### 如果 // 前面有非空字符，则 // 前面需要加一个空格； ####

### （2）、函数注释 ###

#### 参数名、属性名、标签的文本 上下要对齐； ####

#### 在第一个标签前加一个空行； ####

	<?php
	/**
	 * This is a sample function to illustrate additional PHP
	 * formatter options.
	 *
	 * @param        $one   The first parameter
	 * @param int    $two   The second parameter
	 * @param string $three The third parameter with a longer
	 *                      comment to illustrate wrapping.
	 * @return void
	 * @author  52php.cnblogs.com
	 * @license GPL
	 */
	function foo($one, $two = 0, $three = "String") {
	 
	}
## 5、空格 ##
#### （1）、赋值操作符（=，+= 等）、逻辑操作符（&&，||）、等号操作符（==，!=）、关系运算符（<，>，<=，>=）、按位操作符（&，|，^）、连接符（.） 左右各有一个空格； ####

#### （2）、if，else，elseif，while，do，switch，for，foreach，try，catch，finally 等 与 紧挨的左括号“(”之间有一个空格； ####

#### （3）、函数、方法的各个参数之间，逗号（","）后面有一个空格； ####

### 6、空行 ###
#### （1）、所有左花括号 { 都不换行，并且 ｛ 紧挨着的下方，一定不是空行； ####

#### （2）、同级代码（缩进相同）的 注释（行注释/块注释）前面，必须有一个空行； ####

#### （3）、各个方法/函数 之间有一个空行； ####

#### （4）、namespace语句、use语句、clase语句 之间有一个空行； ####

#### （5）、return语句 ####

#### 如果 return 语句之前只有一行PHP代码，return 语句之前不需要空行； ####

#### 如果 return 语句之前有至少二行PHP代码，return 语句之前加一个空行； ####

#### （5）、if，while，switch，for，foreach、try 等代码块之间 以及 与其他代码之间有一个空行； ####
## 【参考示例 汇总】 ##

### 参考1： ###

	<?php
	namespace Lib\Databaes;
	 
	class Mysql extends ParentClass implements \PDO, \DB { // 写一行
	    public getInfo($name, $age, $gender = 1) { // 参数之间有一个空格。默认参数的“=”左右各有一个空格，) 与 { 之间有一个空格
	 
	    }
	}
### 参考2: ###


	<?php
	namespace Vendor\Package;
	 
	abstract class ClassName {
	    protected static $foo; // static放后面
	    abstract protected function zim(); // abstract放前面
	 
	    final public static function bar() { // final放前面，static放最后。
	        // 方法主体部分
	    }
	}
### 参考3： ###


	<?php
	namespace library\Model;
	 
	use library\Helper\ImageHelper;
	use library\Logic\UserMainLogic;
	 
	/**
	 * 用户表 数据模型
	 *
	 * @package library\Model
	 */
	class UserMainModel extends BasicModel {
	     /**
	     * 获得用户统计信息
	     *
	     * @param int $userId 用户ID
	     * @return array
	     */
	    public function getUserCard($userId) {
	        $userId = intval($userId);
	        return UserMainLogic::instance()->getUserCard($userId);
	    }
	 
	    /**
	     * 根据Id 获取用户信息
	     *
	     * @param int    $userId 用户Id
	     * @param string $field  显示字段
	     * @return array
	     */
	    public function getByUserId($userId = 0, $field = '*') {
	        if (empty($userId)) {
	            return array();
	        }
	 
	        $where = array('id' => $userId);
	        $info = $this->field($field)->where($where)->find();
	 
	        if (isset($info['image']) && isset($info['sex'])) {
	            $info['image'] = ImageHelper::GetImageUrl($info['image'], $info['sex']);
	        }
	 
	        return $info;
	    }
	}
### 参考4： ###


	$serv = new swoole_server("127.0.0.1", 9502);
	 
	// sets server configuration, we set task_worker_num config greater than 0 to enable task workers support
	$serv->set(array('task_worker_num' => 4));
	 
	// attach handler for receive event, which have explained above.
	$serv->on('receive', function($serv, $fd, $from_id, $data) {
	    // we dispath a task to task workers by invoke the task() method of $serv
	    // this method returns a task id as the identity of ths task
	    $task_id = $serv->task($data);
	    echo "Dispath AsyncTask: id=$task_id\n";
	});
	 
	// attach handler for task event, the handler will be executed in task workers.
	$serv->on('task', function ($serv, $task_id, $from_id, $data) {
	    // handle the task, do what you want with $data
	    echo "New AsyncTask[id=$task_id]".PHP_EOL;
	 
	    // after the task task is handled, we return the results to caller worker.
	    $serv->finish("$data -> OK");
	});
	 
	// attach handler for finish event, the handler will be executed in server workers, the same worker dispatched this task before.
	$serv->on('finish', function ($serv, $task_id, $data) {
	    echo "AsyncTask[$task_id] Finish: $data".PHP_EOL;
	});
	 
	$serv->start();

## 示例  ##：![](https://images2015.cnblogs.com/blog/381128/201609/381128-20160929222300828-1806388753.png)


## PSR0-4 规范 ##

PHP-FIG  




PSR-0 (Autoloading Standard) 自动加载标准   
PSR-1 (Basic Coding Standard) 基础编码标准   
PSR-2 (Coding Style Guide) 编码风格向导   
PSR-3 (Logger Interface) 日志接口   
PSR-4 (Improved Autoloading) 自动加载的增强版，可以替换掉PSR-0了。  


PSR-0强制性要求几点：

Deprecated - As of 2014-10-21 PSR-0 has been marked as deprecated. PSR-4 is now recommended as an alternative.

不推荐使用 - 在2014年10月21日PSR-0已被标记为过时。PSR-4现在推荐作为替代。

那么也就是说PSR-0已！经！过！时！了！，别哭，PHP代码狗。虽说已经过时，但是我们也可以看看嘛，这不影响我们的学习，好了。废话不说了，开始吧：


一个完全合格的namespace和class必须符合这样的结构：“\< Vendor Name>(< Namespace>)*< Class Name>”  
每个namespace必须有一个顶层的namespace（"Vendor Name"提供者名字）  
每个namespace可以有多个子namespace  
当从文件系统中加载时，每个namespace的分隔符(/)要转换成 DIRECTORY_SEPARATOR(操作系统路径分隔符)  
在类名中，每个下划线(_)符号要转换成DIRECTORY_SEPARATOR(操作系统路径分隔符)。在namespace中，下划线(_)符号是没有（特殊）意义的。
当从文件系统中载入时，合格的namespace和class一定是以 .php 结尾的  
verdor name,namespaces,class名可以由大小写字母组合而成（大小写敏感的）  
是不是有点看不懂啊。什么namespace啊，什么autoloading啊。所以，如果你对命名空间还不是特别懂的话，可以google下namespace 和 自动加载相关的php知识。或者看下一篇，我专门来讲讲他们：namespace 和 autoloading  



### [PSR-1 规范](http://www.php-fig.org/psr/psr-1/) ###


PHP源文件必须只使用 <?php 和 <?= 这两种标签。  
源文件中php代码的编码格式必须是不带字节顺序标记(BOM)的UTF-8。  
一个源文件建议只用来做声明（类(class)，函数(function)，常量(constant)等）或者只用来做一些引起副作用的操作（例如：输出信息，修改.ini配置等），但不建议同时做这两件事。  
命名空间(namespace)和类(class) 必须遵守PSR-0标准。  
类名(class name) 必须使用骆驼式(StudlyCaps)写法 (注：驼峰式(cameCase)的一种变种，后文将直接用StudlyCaps表示)。  
类(class)中的常量必须只由大写字母和下划线(_)组成。  
方法名(method name) 必须使用驼峰式(cameCase)写法。  
好，就是上面的基本7大点，有些很简单，就不过多说明，第3点需要仔细说下。  

#### 第1条 ####
这个基本都是大家都懂得的，PHP代码必须只使用长标签(<?php ?>)或者短输出式标签(<?= ?>)；而不要使用其他标签。  


#### 第2条 ####
这个不多说，保存的时候格式必须是无BOM的UTF-8格式，否则会出现很多无法解释的千奇百怪的问题。千万别傻逼的用Windows下的text文本编辑器保存文件啊  

#### 第3条 ####
这个通俗的说呢。就是别把一些输出和修改的操作(副作用) 和 类文件混合在一起，专注一点，这个文件专门来声明Class, 那个文件专门来修改配置文件，别混在一起写：

所以，以下的这个文件是有问题的，最好不要这样：

	// 副作用：修改了ini配置
	ini_set('error_reporting', E_ALL);
	
	// 副作用：载入了文件
	include "file.php";
	
	// 副作用：产生了输出
	echo "<html>\n";
	
	// 声明 function
	function foo()
	{
		// 函数体
	}
你看上面看起来多乱啊。最好全部分开来写:

	namespace Lib;
	
	class Name
	{
		public function __construct()
		{
			echo __NAMESPACE__ . "<br>";
		}
	
		public static function test()
		{
			echo __NAMESPACE__ . ' static function test <br>';
		}
	}
	修改ini:
	
	ini_set('error_reporting', E_ALL);
	require 文件：
	
	require DIR . '/loading.php';

spl_autoload_register("\\AutoLoading\\loading::autoload");
你看是不是整齐好看多了。当然这个是很难约束的。自己仔细划分。

### 第4条 ###
第4条是约束namespace的，前面已经说过，不多说。值得说的是名字要是驼峰方式来

### 第5条 ###
class name必须要用驼峰方式写，驼峰又分小驼峰和大驼峰（小驼峰是第一个字母是小写）这样写看着舒服也比较规范，不做要求，反正是驼峰就可以了。我喜欢用小驼峰：

	class getUserInfo
	{
	}
### 第6条 ###
是规定类中的常量名(const)声明必须要全部大写，如果有多个单词，就用_分开：

	class getUserInfo
	{
		// 全部大写
		const NAME = 'phper';
	
		// 用_隔开
		const HOUSE_INFO = '已经深圳买房';
	
		public function getUserName()
		{
			//
		}
	}​
### 第7条 ###
method name必须要用驼峰方式写，大小驼峰都可以，不做要求，我喜欢用小驼峰：

	class getUserInfo
	{
		public function getUserName()
		{
			//
		}
	}
PRSR-2 规范  
PSR-2 规范的官网链接在此：[PSR-2](http://www.php-fig.org/psr/psr-2/) 这一规范主要是约束代码风格的，可是说是所有里面最关键最重要的，也是需要好好规范和共同遵守的。  

## 上文有 ##

### [PSR-3 规范](http://www.php-fig.org/psr/psr-3/) ###
PSR-3规范主要是来规范日志接口(Logger Interface)的，老实讲，其实平常接触的不是特别多，所以就不说了，可以去看官网的PSR-3

### [PSR-4 规范](http://www.php-fig.org/psr/psr-4/) ###
PSR-4规范是刚出没多久的一条新的规范，它也是规范 自动加载(autoload)的，是对PSR-0的修改，属于补充规范，  
  
我简单说下，主要是以下几点： 

废除了PSR-0中_就是目录分割符的写法，_下划线在完全限定类名中是没有特殊含义了。 
类文件名要以 .php 结尾。 
类名必须要和对应的文件名要一模一样，大小写也要一模一样