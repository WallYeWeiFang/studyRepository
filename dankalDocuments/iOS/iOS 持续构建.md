# iOS 持续构建

[TOC]

> 持续集成是一种软件开发实践：许多团队频繁地集成他们的工作，每位成员通常进行日常集成，进而每天会有多种集成。每个集成会由自动的构建（包括测试）来尽可能快地检测错误。许多团队发现这种方法可以显著的减少集成问题并且可以使团队开发更加快捷。

## iOS 自动化工具 fastlane

### 使用 fastlane

```bash
gem install fastlane -NV
```

如果安装后无法正常使用需要卸载重新安装，在继续使用

```
gem uninstall fastlane
```

fastlane初始化工程

```
fastlane init
```

初始化工程中会出现四个选项，这里推荐选择第三个，也可以根据具体需求进行更改。

```
// 生成屏幕快照
1. 📸  Automate screenshots
// 打包到 TestFlight
2. 👩‍✈️  Automate beta distribution to TestFlight
// 打包到 App Store
3. 🚀  Automate App Store distribution
// 自定义
4. 🛠  Manual setup - manually setup your project to automate your tasks
```

接着会让你选择`Scheme`，这里直接选择你工程的`Scheme`，不要选错第三方库的。

### 证书管理 match

#### 创建证书仓库

创建一个新的证书仓库（例如 GitHub、 Coding、GitLab），并将其命名为如`certificates`。**提示：**确保仓库已设置为私有。

#### match 初始化

首先安装 `match`

```
gem install match -NV
```

在项目文件夹中使用 `match`

```bash
fastlane match init
```

#### 使用 match

生成证书并安装到本地

```
fastlane match adhoc
fastlane match appstore
fastlane match development
```

在新机器上使用 match 时，如果证书已经存在需要加上`--readonly`命令，防止重复证书

```
fastlane match adhoc --readonly
fastlane match appstore --readonly
fastlane match development --readonly
```

### fastlane 工作流

#### 上传 Appstore

由于fastlane初始化工程时选了
`3. 🚀  Automate App Store`
所以可以直接使用 fastlane 自动生成的 lane

```
fastlane release
```

#### 上传 Fir

新建一个脚本放在项目根路径, 不同项目只需要全局替换掉项目名就可以了,
以下例子项目名是BSD.

```
#设置超时
export FASTLANE_XCODEBUILD_SETTINGS_TIMEOUT=120
#计时
SECONDS=0
#假设脚本放置在与项目相同的路径下
project_path=$(pwd)
#取当前时间字符串添加到文件结尾
now=$(date +"%Y_%m_%d_%H_%M_%S")
#指定项目的scheme名称
scheme="BSD"
#指定要打包的配置名
#测试环境测试包 configuration="Debug"
#正式环境测试包
#configuration="Release"
configuration="AdHoc"
#指定打包所使用的输出方式，目前支持app-store, package, ad-hoc, enterprise, development, 和developer-id，即xcodebuild的method参数
export_method='ad-hoc'
#指定项目地址
workspace_path="$project_path/BSD.xcworkspace"
#指定输出路径
output_path="/Users/nanzengliu/Desktop/BSD/"
#指定输出归档文件地址
archive_path="$output_path/BSD_${now}.xcarchive"
#指定输出ipa地址
ipa_path="$output_path/BSD_${now}.ipa"
#指定输出ipa名称
ipa_name="BSD_${now}.ipa"
#获取执行命令时的commit message
commit_msg="$1"
#输出设定的变量值
echo "===workspace path: ${workspace_path}==="
echo "===archive path: ${archive_path}==="
echo "===ipa path: ${ipa_path}==="
echo "===export method: ${export_method}==="
echo "===commit msg: $1==="
#先清空前一次build
fastlane gym --workspace ${workspace_path} --scheme ${scheme} --clean --configuration ${configuration} --archive_path ${archive_path} --export_method ${export_method} --output_directory ${output_path} --output_name ${ipa_name}
#上传到fir
fir publish ${ipa_path} -T "3ea2a13a78ebbeb16a15ae5aeea6d6ef" -c "${commit_msg}"
#输出总用时
echo "===Finished. Total time: ${SECONDS}s==="
```







