#食谱(The Cookbook)

这一节包含了很多的配方. 在这里"配方(Recipe)"指的是为了完成特定任务的一系列详细的指令. 大部分的配方都是很简单的Gradle脚本代码，当然我们也会有少量的解释帮助初学者使用这些代码.


### 创建反混淆的jar文件

```
task deobfJar(type: Jar, dependsOn: 'jar') {
    from "build/source/main"
    classifier "dev"
}
artifacts {
    archives deobfJar
}
```

### 创建源码jar文件

(译注：这个任务在ForgeGradle 2.0之后自带了，不需要自己新建了)

```
task sourceJar(type: Jar, dependsOn: 'sourceMainJava') {
    from "build/sources/java"
    from "build/resources/main/java"
    classifier "sources"
}
artifacts {
    archives sourceJar
}
```

### 创建Javadoc的jar文件

```
task javadocJar(type: Jar, dependsOn: 'javadoc') {
    from "build/docs/javadoc"
    classifier "javadoc"
}
artifacts {
    archives javadocJar
}
```

### 使用环境变量

在这里，`BUILD_NUMBER` 是我们使用的环境变量

```
version = "1.2.3." + System.env.BUILD_NUMBER
```

### 获取一个Git的哈希值(Hash)

不是所有人都安装了Git并且把它加到PATH里面，所以最好只在缺少环境变量或者怎么样的情况下使用这个方法. 在这个例子中环境变量 `BUILD_NUMBER` 在运行Git指令之前被先检查是否存在，如果存在直接返回 `BUILD_NUMBER`. 如果Git指令运行失败了，我们将会使用字符串 `"GITBROK"` 而不是版本号.


```
def getVersionAppendage() {
    if (System.env.BUILD_NUMBER)
        return System.env.BUILD_NUMBER

    def proc = "git rev-parse --short HEAD".execute()
    proc.waitFor()
    return "DEV." + proc.exitValue() ? "GITBORK" : proc.text.trim()
}

version = "1.2.3_" + getVersionAppendage()
```

### Shading

在这个例子当中，我们需要打包EJML库到我们的jar里去. 当然，你可以对任何被当做依赖的jar随同代码进行打包. 每个库的文件都在不同的包(Package)里面，在这里EJML的根包(Root Package)是 `org.ejml`，并且我想把它重定位到 `your.new.package.here.ejml` 去. 注意包名称里的点(`.`)被替换成了斜线(`/`).

```
minecraft {
    srgExtra "PK: org/ejml your/new/package/here/ejml"
}

configurations {
    shade
    compile.extendsFrom shade
}

dependencies {
    shade 'com.googlecode.efficient-java-matrix-library:ejml:0.25'
}

jar {
    configurations.shade.each { dep ->
        from(project.zipTree(dep)){
            exclude 'META-INF', 'META-INF/**'
            // 如果你愿意也可以在这里排除(Exclude)其它文件，或者复制META-INF
        }
    }
}
```
