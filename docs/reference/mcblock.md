# Minecraft{}拓展对象

在minecraft{}拓展对象里有许多公共方法，它们是被ForgeGradle内部调用的，也不太可能会被大部分用户使用. 这些方法将不会列在这里. 你可以去看代码.

- **不要忘记**
    - `object.someMethod(someArg)` === `object.someMethod someArg`
    - `object.someProp = "thing"` === `object.setSomeProp("thing")`
    - `object.someProp = "thing"` === `object.setSomeProp("thing")`

### 版本

- `String getApiVersion()`
    - 明确返回MinecraftForge或者是FML版本，版本的格式为`McVersion-#.#.#.#`. 如果存在有分支(Branch)，分支将会附加在版本的后面，它会变成为`McVersion-#.#.#.#-branch`.
- `void setVersion(String version)`
    - 传到这个方法里的参数将会被解析并且会根据[Forge](http://files.minecraftforge.net/maven/net/minecraftforge/forge/json)或[FML](http://files.minecraftforge.net/maven/net/minecraftforge/fml/json)的json文件验证.
    - 可能的参数标记:
        - 一个Forge/FML的构建序号，例如: `1232`
        - 一个Forge/FML的推广版本，例如: `1.7.10-latest`, `1.7.10-recommended`
        - 一个Forge/FML版本字符串，它可能会包含一个附加的分支，例如: `10.12.0.1048`, `11.14.0.1288-1.8`
        - 一个完整的Minecraft + Forge/FML版本字符串，可能会包含一个附加的分支，格式为`mcVersion-forge/fmlversion(-branch)`，例如: `1.7.2-10.12.0.1048`, `1.8-11.14.0.1288-1.8`
- `String getVersion()`
    - 只返回Minecraft的版本. 例如: `1.7.10`, `1.8`. 如果你想获取Forge/FML的版本，请使用getApiVersion().
- `String getMappings()`, `void setMappings(String mappings)`
    - 这个方法的参数只有一个标记(Notation)，那就是 `channel_version`，它将会根据[MCP json](http://export.mcpbot.bspk.rs/versions.json)来进行验证. 合法的通道目前有 `stable`、`stable_nodoc`、`snapshot`和`snapshot_nodoc`. `nodoc` 版本不包含Javadoc注释. 如果参数是 `something_custom`的形式的话，验证过程会被省略，并且我们将认为用户有他们自己的MCP映射版本. 更多关于自定义MCP快照的信息可以在[这里](https://gist.github.com/AbrarSyed/0d1f7ebea8767e264038)找到.


### 替换源码

- `void replace(Object token, Object replacement)`
    - 添加标记替换(Token Replacement)的条目. 第一个对象是在源码里的对象，第二个对象是将要被替换为的对象.
- `void replace(Map<Object, Object> map)`
    - 和上面一样，只是现在你能使用以下的标记形式了: `minecraft { replace "key": "val", "key2", "val2" }`
- `void replaceIn(String path)`
    - 添加一个文件包含. 如果没有任何东西使用这个方法包含了，则默认会对所有文件都执行替换操作. 否则只会处理被包含的文件. 文件将会根据包含路径使用`file.endsWith(path)`被检查，直到有能够配对的文件出现. 所以你可以只设定一个文件名，也可以是整个路径.

### 其它

- `void srgExtra(String srgLine)`
    - 用来包含任意SRG行(译注:见Shading最后)到重混淆(Reobfuscation)的SRG文件中. 使用它的一个例子是Shade依赖.虽然它还有很多用处.
- `String getRunDir()` `void getRunDir()`
    - 允许设定和获取配置过的运行目录. 它设置了当runClient和runServer任务运行时工作目录的位置. 所有的Minecraft文件将会生成并储存在这个文件夹，记得在gitignore文件里将这个文件夹忽略.
- 访问级转换器(Access Transformers), 下面所有的方法都能添加文件到访问级转换器里.
    - `void at(Object obj)`
    - `void at(Object... objs)`
    - `void accessT(Object obj)`
    - `void acessTs(Object... objs)`
    - `void accessTransformers(Object obj)`
    - `void accessTransformers(Object... objs)`
- `int getMaxFuzz()` `void setMaxFuzz(int fuzz)`
    - 一个很少会用到的方法，它能够设定应用Forge/FML补丁(Patch)时模糊的量. 默认为0，补丁程序会因为任何一点不匹配就失败.将fuzz设定到1以上可以解决由不完美的补丁和干扰的访问级转换器产生的问题.
