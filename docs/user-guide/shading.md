# Shading

## 是什么?

Shade一个库就是将这个库里面的所有内容放到你自己的jar文件里面，**并且**改变他们的包(Package). 这与普通的**打包(Packaging)**是不同的，打包只是简单地将库文件扔到jar文件里，并没有改变包的位置. **FatJar**这个术语就是用来表示同时包含应用(Application)与它的依赖的jar文件.

## 为什么?

当一个mod使用了像EJML或其它第三方的库，通常在运行的时候这些库也需要被加载从而避免 `ClassDefNotFoundError` Minecraft和Forge使用了一些库，他们能够保证出现在运行的时候，像是GSON和Google Guava，但是如果是其它额外的没有被包含的库呢? **Shading**就是能够保证这些额外的库在运行时出现的一种方法.

**打包(Packaging)**也能同样解决问题. 将你的库放到jar文件里使得它们在运行时存在，之后就行了. 那我们为什么还要将库转移(Relocate)呢? 这个问题的答案存在于别人也会写不同的mod这一事实当中. 也许你随同你的mod一起打包了AbrarLib 1.0，它只有一个叫做 `abrar.lib.AbrarClass` 的类. 我们假设另外一个人制作了一个不同的mod，他打包了AbrarLib 2.0到jar里去，2.0版本包含了两个类: `abrar.lib.AbrarClass` 和 `abrar.lib.AbrarHelper`. 当你的mod想要引用`AbrarClass`会发生什么呢? 当其它mod想使用`AbrarClass`时候会发生什么呢? 不能保证都能正确地工作，也许都会导致预想不到的结果. 转移就解决了这个问题. Shade到你的mod里的AbrarLib文件将会叫 `my.mod.abrarlib.AbrarClass`，在其他mod里的AbrarLib文件将会叫 `other.mod.abrarlib.AbrarClass` 和 `other.mod.abrarlib.AbrarHelper`. 因为这些类有不同的名字，在它们之间就不会有混淆了. 这也就是为什么我们需要Shade而不是简单地打包运行时的依赖了.

## 怎样去做?

```
configurations {
    shade
    compile.extendsFrom shade
}
```

这定义了一个独立于 *compile* 配置的新的配置(依赖容器(Dependency Container))，但是**compile**配置继承我们的新配置，所以我们在**shade**配置里定义的所有东西都将存在于**compile**配置里. 这允许我们从即将被编译依赖里面分离出来我们需要shade的依赖.

```
dependencies {
    shade 'com.googlecode.efficient-java-matrix-library:ejml:0.25'
}
```

注意这个依赖声明行由**shade**开头，而不是 **compile**，这意味着我们正在将一特定依赖加到我们之前定义的**shade**配置中，而不是**compile**配置中.这一个dependency{}代码块**必须**在configurations{}代码块的后面. Gradle脚本是有顺序的，我们不能使用还没有被定义的东西.

```
jar {
    configurations.shade.each { dep ->
        from(project.zipTree(dep)){
            exclude 'META-INF', 'META-INF/**'
        }
    }
}
```

这一部分告诉Gradle去包含在**shade**配置当中的依赖的内容到你的jar文件当中, 当这个步骤完成的时候，库还没有被转移. 所以任何库的重复都可能会很麻烦. 这一部分可以放在build.gradle文件的任何地方.

```
minecraft {
    srgExtra "PK:org/ejml your/new/package/here/ejml"
    srgExtra "PK:org/ejml/alg your/new/package/here/ejml/alg"
}
```

这一部分告诉了Gradle你想将什么包转移到哪里. 它利用了ForgeGradle的重混淆(Reobfuscation)机制，所以这几行也只有在构建当中重混淆的步骤才能生效. 这些**srgExtra**字符串是SRG行()，它们也可以被定义为单独的类、字段、方法和包. 这一部分可以放在build.gradle的任何地方.

!!! Note "译注"

	关于SRG行(SRG Lines)一些东西: 

	SRG名是以Searge命名的，为了多版本通用而创建的名字，类似于field_12345_a之类的字段或者方法什么的，也是现在Forge运行时使用的名字，SRG Lines简单来说就是能够替换Java字节码内容的指令，具体写法什么的可以参考MCP的conf/joined.srg文件.