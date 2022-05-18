![IceCream](https://i.loli.net/2017/11/18/5a104e5acfea5.png)

[![CI Status](http://img.shields.io/travis/caiyue1993/IceCream.svg?style=flat)](https://travis-ci.org/caiyue1993/IceCream)
[![Version](https://img.shields.io/cocoapods/v/IceCream.svg?style=flat)](http://cocoapods.org/pods/IceCream)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
<a href="https://swift.org/package-manager/"><img src="https://img.shields.io/badge/SPM-supported-DE5C43.svg?style=flat"></a>
[![License](https://img.shields.io/cocoapods/l/IceCream.svg?style=flat)](http://cocoapods.org/pods/IceCream)
[![Platform](https://img.shields.io/cocoapods/p/IceCream.svg?style=flat)](http://cocoapods.org/pods/IceCream)
   
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/caiyue1993/icecream/issues)
<a href="https://twitter.com/caiyue5"><img src="https://img.shields.io/twitter/follow/caiyue5.svg?style=social"></a>

IceCream helps you sync Realm Database with CloudKit.
IceCream 可帮助您将 Realm Database 与 CloudKit 同步。

> It works like magic! (它像魔术一样工作)

## Features

- Realm Database
    - [x] Off-line First (离线优先)
    - [x] Thread Safety (线程安全)
    - [x] Reactive Programming (响应式编程)
    - [x] Optimized for mobile apps (针对移动应用进行了优化)
    - [x] Easy when migrating (迁移简单)

- Apple CloudKit 
    - [x] Automatical Authentication (自动身份验证)
    - [x] Silent Push (静默推送)
    - [x] Free with limits(Private database consumes your user's iCloud quota)(免费限制（私人数据库使用用户的iCloud配额）)

- [x] Delta update (增量更新)
- [x] Reachability(Support Long-lived Operation) 
- [x] Powerful Error Handling (强大的错误处理)
- [x] Sync Automatically (自动同步)
- [x] Multiple object models support (多对象模型支持)
- [x] Public/Private Database support (公共/私有数据库支持)
- [x] Large Data Syncing (大数据同步)
- [x] Manually Synchronization is also supported (还支持手动同步)
- [x] Relationship(To-One/To-Many) support (关系（对一/对多）支持)
- [x] Available on every Apple platform(iOS/macOS/tvOS/watchOS) (可在所有 Apple 平台上使用（iOS/macOS/tvOS/watchOS）)
- [x] Support Realm Lists of Natural Types (支持Natural类型的Realm Lists)
- [ ] Complete Documentation (完整的文档)

## Prerequisite (前提条件)

1. Be sure to have enrolled in Apple Developer Program (请务必注册 Apple 开发者计划)
2. Turn on your iCloud in Capabilities and choose `CloudKit` (在“Capabilities”中打开iCloud，然后选择“CloudKit”)
3. Turn on Background Modes and check `Background fetch` and `Remote notification`  (打开后台模式并选中“Background fetch”和“Remote notification”)

## Usage (用法)

### Basics (基本用法)

1. Prepare your Realm Objects (e.g. Dog, Cat...)(准备你的Realm对象):

```swift
class Dog: Object {
    @objc dynamic var id = NSUUID().uuidString
    @objc dynamic var name = ""
    @objc dynamic var age = 0
    @objc dynamic var isDeleted = false

    static let AVATAR_KEY = "avatar"
    @objc dynamic var avatar: CreamAsset?

    @objc dynamic var owner: Person? // to-one relationships must be optional

    override class func primaryKey() -> String? {
        return "id"
    }
}
```

2. Do stuff like this(像这样):

```swift
extension Dog: CKRecordConvertible & CKRecordRecoverable {
    // Leave it blank is all
}
```

Is that easy? Protocol Extensions do this trick.

3. Start the Engine!（启动Engine！）

```swift
var syncEngine: SyncEngine?
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    ...
    syncEngine = SyncEngine(objects: [
            SyncObject(type: Dog.self),
            SyncObject(type: Cat.self),
            SyncObject(type: Person.self)
        ])
    application.registerForRemoteNotifications()
    ...
}
```

4. Listen for remote notifications (侦听远程通知)

> The sample code in [AppDelegate](Example/IceCream_Example/AppDelegate.swift) will be a good reference.
> ([AppDelegate]（示例/IceCream_Example/AppDelegate.swift）中的示例代码将是一个很好的参考。)

That's all you need to do! Every time you write to Realm, the SyncEngine will get notified and handle sync stuff!

这就是你需要做的！ 每次你写入 Realm 时，SyncEngine 都会收到通知并处理同步内容！

For more details, clone the project to see the source code.

有关更多详细信息，请克隆项目以查看源代码。

### Object Deletions (对象删除)

Yep, we highly recommend you use **Soft Deletions**. That's why we add an `isDeleted` property to `CKRecordConvertible` protocol. 

是的，我们强烈建议您使用**软删除**。这就是为什么我们将“isDeleted”属性添加到“CKRecordConvertible”协议中的原因.

When you want to delete an object, you just need to set its `isDeleted` property to true and the rest of the things are already taken care of.

当您要删除对象时，只需将其“isDeleted”属性设置为 true，其余部分就已经处理好了。

*You also don't need to worry about cleaning-up things. It has also been considered.*

您也不必担心关于清理的事情.它也已被深思熟虑过.

### How about syncing asset?  (如何同步asset资源？)

Luckily, we have a perfect solution for syncing asset. 

幸运的是，我们有一个完美的解决方案来同步asset资源。

Absolutely, you could also store your image or kind of resource stuff as `Data` type and everything works fine. But Realm has a [16MB limit](https://realm.io/docs/objc/latest/#current-limitations) of data property. And CloudKit encourages us to use `CKAsset` in places where the data you want to assign to a field is more than a few kilobytes in size.

当然，您还可以将图像或资源内容存储为“Data”类型，一切正常.但是 Realm 的数据属性有 [16MB 限制](https://realm.io/docs/objc/latest/#current-limitations)。CloudKit 鼓励我们在要分配给字段的数据大小超过几千字节的地方使用“CKAsset”。

So taking the consideration of the above two, we recommend you to use `CreamAsset` property to hold data. `CreamAsset` will store local data on the file system and just save file paths in the Realm, all automatically. And we'll wrap things up to upload to CloudKit as `CKAsset`. 

因此，考虑到上述两点，我们建议您使用“CreamAsset”属性来保存数据。“CreamAsset”会将本地数据存储在文件系统上，并且只是将文件路径保存在 Realm 中，所有这些都是自动的。我们将把内容作为“CKAsset”打包到CloudKit。

An example project is provided to see the detailed usage.

提供了一个示例项目来查看详细的用法。

### Relationships (关系)

IceCream has officially supported Realm relationship(both one-to-one and one-to-many) since version 2.0.

IceCream 从 2.0 版本开始就正式支持 Realm 关系（一对一和一对多）。

Especially, for the support of to-many relationship, you have to pass the element type of the List to the SyncObject init method parameters. For example:

特别是，为了支持多关系，您必须将 List 的元素类型传递给 SyncObject init 方法参数。例如：

```swift
syncEngine = SyncEngine(objects: [
            SyncObject(type: Dog.self),
            SyncObject(type: Cat.self),
            SyncObject(type: Person.self, uListElementType: Cat.self) // if Person model has a List<Cat> property
        ])
```

## Requirements (要求)

- iOS 10.0+ / macOS 10.12+ / tvOS 10.0+ / watchOS 3.0+ 
- Swift 5

## Debug Suggestions (调试建议)

It's true that debugging CloudKit is hard and tedious. But I have some tips for you guys when facing puzzles:

的确，调试 CloudKit 既困难又乏味。但是，在面对难题时，我有一些提示给你们：

- You should know how Realm and CloudKit works.(你应该知道 Realm 和 CloudKit 是如何工作的。)
- Using GUI tools, like [Realm Browser](https://itunes.apple.com/us/app/realm-browser/id1007457278?mt=12) and [CloudKit Dashboard](https://icloud.developer.apple.com/dashboard). (使用 GUI 工具，如[Realm Browser](https://itunes.apple.com/us/app/realm-browser/id1007457278?mt=12) and [CloudKit Dashboard](https://icloud.developer.apple.com/dashboard).)
- When you are lost and don't remember where you are, I suggest starting all over again. In CloudKit Dashboard, "Reset..." button is provided. You can also clear local database by re-install apps.(当你迷路了，不记得自己在哪里时，我建议你重新开始。在 CloudKit 仪表板中，“Reset...”按钮。您还可以通过重新安装应用程序来清除本地数据库。
)
- By default, IceCream only prints some logs to your console in DEBUG mode. However, you could turn it off by adding `IceCream.shared.enableLogging = false` if it bothers you.( 默认情况下，IceCream 仅在 DEBUG 模式下将一些日志打印到您的主机。但是，如果它打扰了您，您可以通过添加“IceCream.shared.enableLogging = false”来关闭它。)
- Keep calm and carry on! (保持冷静，继续前进！)

*Warning: If you're going to launch your app onto App Store, don't forget to deploy your environment settings to production. You can do it easily in the CloudKit Dashboard. Write & Read permissions are also need to be considered.*

*警告：如果您要在 App Store 上启动您的 app，请不要忘记将环境设置部署到生产环境。您可以在 CloudKit 控制面板中轻松完成此操作。还需要考虑写入和读取权限。

### One More Tip (再多一个提示)

How to debug CloudKit in production mode? See this [post](https://stackoverflow.com/questions/30182521/use-production-cloudkit-during-development).

如何在生产模式下调试 CloudKit？请参阅此 [post](https://stackoverflow.com/questions/30182521/use-production-cloudkit-during-development).

## Example (例子)

To run the example project, clone the repo, then open the `Example/IceCream_Example.xcodeproj`.

若要运行示例项目，请克隆存储库，然后打开“Example/IceCream_Example.xcodeproj”。

## Installation Guide (安装指南)

Using Swift Package Manager, Carthage or CocoaPods.

使用 Swift Package Manager, Carthage or CocoaPods.

### Swift Package Manager

From Xcode 11, you can use [Swift Package Manager](https://swift.org/package-manager/) to add IceCream and its dependencies to your project.

在 Xcode 11 中，您可以使用 [Swift Package Manager](https://swift.org/package-manager/) 将 IceCream 及其依赖项添加到您的项目中。

Select File > Swift Packages > Add Package Dependency. Enter https://github.com/caiyue1993/IceCream.git in the "Choose Package Repository" dialog.
In the next page, specify the version resolving rule as "Up to Next Major" with "2.0.2" as its earliest version.
After Xcode checking out the source and resolving the version, you can choose the "IceCream" library and add it to your app target.

选择“File> Swift Packages>Add Package Dependency“ 。在“Choose Package Repository”对话框中输入 https://github.com/caiyue1993/IceCream.git。
在下一个页面中，将版本解析规则指定为“最多下一个主要版本”，并将“2.0.2”指定为最早版本。
在Xcode检查出源代码并解析版本后，您可以选择“IceCream”库并将其添加到您的应用程序目标。

If you encounter any problem or have a question on adding the package to an Xcode project, I suggest reading the [Adding Package Dependencies to Your App](https://developer.apple.com/documentation/xcode/adding_package_dependencies_to_your_app) guide article from Apple.

如果您在将软件包添加到 Xcode 项目时遇到任何问题或有疑问，我建议您阅读 Apple 的 [Adding Package Dependencies to Your App](https://developer.apple.com/documentation/xcode/adding_package_dependencies_to_your_app) 指南文章。


### Carthage

[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager for Cocoa applications.

[Carthage](https://github.com/Carthage/Carthage) Cocoa应用程序的依赖管理器。

To integrate IceCream into your Xcode project using Carthage, specify it in your `Cartfile`:

要使用IceCream集成到您的Xcode项目中，请在“Cartfile”中指定它：

```ogdl
github "caiyue1993/IceCream"
```

Then, run the following command to build the frameworks:

然后，运行以下命令以构建框架：

```bash
carthage update
```

Normally, you'll get **IceCream**, **Realm** and **RealmSwift** frameworks. You need to set up your Xcode project manually to add these 3 frameworks.

通常，你会得到**IceCream**，**Realm**和**RealmSwift**框架。您需要手动设置 Xcode 项目才能添加这 3 个框架。

On your application targets’ **General** settings tab, in the **Linked Frameworks and Libraries** section, drag and drop each framework to use from the `Carthage/Build` folder on disk.

在应用程序目标的“**General**设置选项卡上的**Linked Frameworks and Libraries**部分中，从磁盘上的“Carthage/Build”文件夹中拖放要使用的每个框架。

On your application targets’ **Build Phases** settings tab, click the “+” icon and choose “New Run Script Phase”. Create a Run Script with the following content:

在应用程序目标的**Build Phases**设置选项卡上，单击“+”图标，然后选择“New Run Script Phase”。创建包含以下内容的运行脚本：


```bash
/usr/local/bin/carthage copy-frameworks
```

and add the paths to the frameworks you want to use under “Input Files”(taking iOS platform for example):

并在“Input Files”下添加要使用的框架的路径（以iOS平台为例）：

```
$(SRCROOT)/Carthage/Build/iOS/IceCream.framework
$(SRCROOT)/Carthage/Build/iOS/Realm.framework
$(SRCROOT)/Carthage/Build/iOS/RealmSwift.framework
```

For more information about how to use Carthage, please see its [project page](https://github.com/Carthage/Carthage).

有关如何使用Carthage的更多信息，请参阅其[project page](https://github.com/Carthage/Carthage).

### CocoaPods

IceCream is available through [CocoaPods](http://cocoapods.org). To install it, simply add the following line to your Podfile:

IceCream可通过 [CocoaPods](http://cocoapods.org) 获得。要安装它，只需将以下行添加到您的 Pod 文件中：

```ruby
pod 'IceCream'
```

> If you want to build IceCream as a static framework, CocoaPods 1.4.0+ is required.
> 如果你想把IceCream构建成一个静态框架，CocoaPods 1.4.0+是必需的。

## Make it better

This is the to-do list for the IceCream project. You can join us to become a contributor.

- [ ] CloudKit Shared Database 

See the [CONTRIBUTING](docs/CONTRIBUTING.md) file for contributing guidelines.

## Live Demo

My app [Music Mate](https://apps.apple.com/app/music-mate-for-apple-music/id1605379758) (The missing mate for Apple Music) is using IceCream to its fullest. You can download it and try it on your multiple devices to see this magic.

<a href="https://apps.apple.com/app/music-mate-for-apple-music/id1605379758">
  <img src="https://github.com/caiyue1993/Tiptoes/blob/master/images/appstore.png">
</a>

## Reference

- [Synchronizing data with CloudKit](https://medium.com/@guilhermerambo/synchronizing-data-with-cloudkit-94c6246a3fda) (Recommended)
- [CloudKit Best Practices](https://developer.apple.com/videos/play/wwdc2016/231/)
- [Mastering Realm Notifications](https://academy.realm.io/posts/meetup-jp-simard-mastering-realm-notifications/)

## Contributors

This project exists thanks to all the people who contribute:

<a href="https://github.com/caiyue1993/IceCream/graphs/contributors"><img src="https://opencollective.com/IceCream/contributors.svg?width=890&button=false" /></a>

## Sponsorship

Open source is great, but it takes time and efforts to maintain. I'd be greatly appreciated and motivated if you could to support the maintenance of IceCream financially. You could sponsor this project through the below ways:

- Become my [GitHub Sponsors](https://github.com/sponsors/caiyue1993), recommended
- Back me on [Open Collective](https://opencollective.com/icecream)
- Transfer your donations directly via [PayPal](https://paypal.me/yuecai)

And thanks to all our backers on open collective:

<a href="https://opencollective.com/icecream#backers" target="_blank"><img src="https://opencollective.com/icecream/backers.svg?width=890"></a>

## License

IceCream is available under the MIT license. See the LICENSE file for more info.
