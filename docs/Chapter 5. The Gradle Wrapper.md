Chapter 5. The Gradle Wrapper
===



大多数工具需要在您的计算机上安装，然后才能使用它们。 如果安装很容易，你可能认为这很好。 但这对于构建的用户来说可能是不必要的负担。 同样重要的是，用户安装了正确版本的构建工具吗？ 如果他们正在构建一个旧版本的软件呢？

Gradle Wrapper（以下简称“Wrapper”）解决了这两个问题，是开始 Gradle 构建的首选方法。

5.1. Executing a build with the Wrapper 通过 Wrapper 来执行一个构建

如果 Gradle 项目已经设置了 Wrapper（并且我们建议所有项目都这样做），则可以使用以下命令之一从项目根目录执行构建：

* ./gradlew <task>（在类似 Unix 的平台上，如 Linux 和 Mac OS X）
* gradlew <task>（在 Windows 上使用 gradlew.bat 批处理文件）

每个 Wrapper 都绑定到一个特定版本的 Gradle，所以当你第一次在给定 Gradle 版本下运行上面的命令之一时，它将下载相应的 Gradle 发布包，并使用它来执行构建。
 

当通过其  Wrapper 导入 Gradle 项目时，IDE 可能会要求使用 Gradle “all”发布包。这是完全正常的，并帮助 IDE 提供构建文件代码补全。

这不仅意味着您不必手动安装 Gradle，而且您还必须使用该版本所设计的 Gradle 版本。这使得您的历史构建更可靠。只要在本用户指南、Stack Overflow 等任何地方看到以`gradle ...`开头的命令行时，就是在使用上面的相应语法。

为了完整性，并确保您不删除任何重要的文件，这里是 Gradle 项目中组成 Wrapper 的文件和目录：

* gradlew（Unix Shell脚本）
* gradlew.bat（Windows批处理文件）
* gradle/wrapper/gradle-wrapper.jar（Wrapper JAR）
* gradle/wrapper/gradle-wrapper.properties（Wrapper 属性文件）

如果你想知道 Gradle 发布包的存储位置，你可以在`$USER_HOME/.gradle/wrapper/dists`下的用户主目录中找到它们。