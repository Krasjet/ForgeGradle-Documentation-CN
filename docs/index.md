# ForgeGradle 1.2

ForgeGradle是一个为了方便构建Minecraft mod和插件而制作的Gradle插件. 原本它只是为MinecraftForge API定制的，但是现在它也能辅助其他API的构建，比如说LiteLoader和Sponge. ForgeGradle“当前的”版本为`1.2`，也就是说最新的MinecraftForge使用的就是这个版本，并且这个也是当前正在被维护的版本.

---

## 支持版本
 - **Minecraft**
    - 1.7.2
    - 1.7.10
    - 1.8
 - **MinecraftForge**
    - 10.12.0.1048+
 - **ForgeModLoader**
    - 7.2.136.885+

---

## 本文档的章节

在侧边栏你可以找到这个文档的三个部分.

#### 食谱(Cookbook)

食谱(Cookbook)部分是一系列的配方(Recipes). 在这里，配方是一个关于完成一个特定任务(Task)的详细说明. 大部分的配方都包括简单的Gradle脚本代码片段和足够一个初学者使用的少量的解释.

#### 用户指南(User Guide)

这一部分很像食谱部分，但是这一章包含了对每一个任务的详细的教程. 这些教程的目标不止为了教你如何完成相应的任务，更是为了让你理解代码是如何工作的以及我们为什么需要这个任务. 理想情况下，能够阅读并理解这一章所有的教程并会一些Gradle的人就能够利用好进阶参考部分了.

#### 进阶参考(Adept Reference)

这一部分是针对那些使用Gradle时知道他们在干什么的人的. 我们在这一章我们扒开ForgeGradle的糖衣列举了它**真正**在做得工作，没有任何附加信息.