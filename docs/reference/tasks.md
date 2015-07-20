# 任务

这一节我们将看看加在ForgeGradle里的任务并且和它们的功能. 但这并不是ForgeGradle真正创建的所有的任务，它们只是那些对用户来说非常常用的.

### SetupCIWorkspace

这一个任务为那些无外设的持续集成(Continuous Integration)服务器设计的，它们既不需要也没有能力运行Minecraft客户端. 它仅仅只需要一个能编译Minecraft mod的最低要求；具体来说有以下几个任务:

 - 下载MC的jar文件
 - 下载Forge/FML的userdev包
 - (分别)合并MC服务器和客户端的jar文件
 - 下载MCP映射表
 - 由映射表生成反混淆和重混淆SRG文件
 - 反混淆打过补丁(Patched)的jar到MCP名字(译注:就是我们看到的被MCP翻译到能够理解的方法或者字段的名字)并且应用限制级转换器(Access Transformers)(创建forgeBin产物)

### SetupDevWorkspace

这个方法建立了一个供Minecraft mod开发的开发环境. 它包括了编译Minecraft mod与运行Minecraft所需要的所有东西. 它做了SetupCIWorkspace里的所有任务之外加上下面几个额外的任务:
 - 下载并解压LWJGL的本地文件(Natives)
 - 下载Minecraft的AssetIndex文件
 - 下载在AssetIndex文件里定义的所有资源文件
 - 生成GradleStart和GradleStartServer的主类


### SetupDecompWorkspace

这个任务是至今最受欢迎的环境建立任务了. 它建立了一个完整的开发环境，它和setupDevWorkspace很相似，主要不同是它反编译了Minecraft并且提供了可浏览的反编译之后的Java代码. 具体来说它做了一下任务:

- 下载MC
- 下载Forge/FML的userdev包
- (分别)合并MC服务器和客户端的jar文件
- 下载MCP映射表
- 从映射表生成反混淆和重混淆的SRG文件
- 反混淆合并后的jar到SRG名字，并且应用访问级转换器.
- 反编译已经被反混淆的Minecraft的jar文件并且应用源码的一些变换
- 应用Forge/FML的补丁(创建forgeSrc-sources产物)
- 解压补丁到Minecraft源码jar文件中
- 重新编译打补丁后的Minecraft源码
- 重新打包编译后的Minecraft类(创建forgeSrc产物)
- 下载并解压LWJGL的本地文件(Natives)
- 下载Minecraft的AssetIndex文件
- 下载在AssetIndex文件里定义的所有资源文件
- 生成GradleStart和GradleStartServer的主类

### runClient和runServer

这些任务是分别为了方便启动Minecraft客户端和服务端而设计的. 他们依赖于`jar`任务，从而使得mod已经被完全编译和打包了，但是在这个任务执行之前没有进行重混淆. 这两个任务的类型都是[JavaExec](https://gradle.org/docs/current/dsl/org.gradle.api.tasks.JavaExec.html)，并且他们是可以一样自定义的，代码块分别是 `runClient { .. }` 和 `runServer { .. }`.

### build

这个任务构建了mod并且创建了一个重混淆的可发行的Jar文件. 如果这个任务失败了并且错误是`'forgeBin' was not found` 你必须先运行之前3个配置环境的三个指令之一.根据构建脚本(Buildscript)，这个任务将会尝试自动配置开发环境，但不是会每次都成功. 这个问题的根源是配置任务创建了一个编译需要使用的Minecraft的jar文件，一些build.gradle文件尝试在那个jar文件创建之前引用它. **这个任务是默认在Gradle的 `java` 插件里的，而不是被ForgeGradle添加的.**

### cleanCache

因为Minecraft mod工程的性质，很常见的会有某些人拥有不同的ForgeGradle工程，并且都使用一样的Minecraft/Forge/FML版本. 因为这个ForgeGradle在USER_HOME/.gradle/caches/minecraft里缓存了许多东西，从而使得它们可以被多个工程使用. 这个任务完整地清理了缓存并删除了文件夹里所有的内容. 在这个任务运行之后，所有的工程(译注:不论什么版本)都必须重新重新运行配置任务来重新创建Minecraft的依赖.

### reobf

这个任务重混淆了已经编译的和打包的mod从而使得它能够被安装到混淆之后的Minecraft环境(译注:也就是Forge运行的环境). 默认情况下，这个任务是被 `build` 任务所依赖的，并且依赖于其它的Jar任务. 这是ForgeGradle想让最终用户设置得很少的任务之一. 这个任务的更多信息可以在这里找到.(未完成)
