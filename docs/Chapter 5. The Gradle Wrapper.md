Chapter 5. The Gradle Wrapper
===



大多数工具需要在您的计算机上安装，然后才能使用它们。 如果安装很容易，你可能认为这很好。 但这对于构建的用户来说可能是不必要的负担。 同样重要的是，用户安装了正确版本的构建工具吗？ 如果他们正在构建一个旧版本的软件呢？

Gradle Wrapper（以下简称“Wrapper”）解决了这两个问题，是开始 Gradle 构建的首选方法。

## 5.1. Executing a build with the Wrapper 通过 Wrapper 来执行一个构建

如果 Gradle 项目已经设置了 Wrapper（并且我们建议所有项目都这样做），则可以使用以下命令之一从项目根目录执行构建：

* ./gradlew <task>（在类似 Unix 的平台上，如 Linux 和 Mac OS X）
* gradlew <task>（在 Windows 上使用 gradlew.bat 批处理文件）

每个 Wrapper 都绑定到一个特定版本的 Gradle，所以当你第一次在给定 Gradle 版本下运行上面的命令之一时，它将下载相应的 Gradle 发布包，并使用它来执行构建。
 
>IDE

>当通过其  Wrapper 导入 Gradle 项目时，IDE 可能会要求使用 Gradle “all”发布包。这是完全正常的，并帮助 IDE 提供构建文件代码补全。

这不仅意味着您不必手动安装 Gradle，而且您还必须使用该版本所设计的 Gradle 版本。这使得您的历史构建更可靠。只要在本用户指南、Stack Overflow 等任何地方看到以`gradle ...`开头的命令行时，就是在使用上面的相应语法。

为了完整性，并确保您不删除任何重要的文件，这里是 Gradle 项目中组成 Wrapper 的文件和目录：

* gradlew（Unix Shell脚本）
* gradlew.bat（Windows批处理文件）
* gradle/wrapper/gradle-wrapper.jar（Wrapper JAR）
* gradle/wrapper/gradle-wrapper.properties（Wrapper 属性文件）

如果你想知道 Gradle 发布包的存储位置，你可以在`$USER_HOME/.gradle/wrapper/dists`下的用户主目录中找到它们。


## 5.2. Adding the Wrapper to a project 给项目添加 Wrapper

Wrapper 是你应该检查版本控制的东西。通过将 Wrapper 与您的项目一起分发，任何人都可以使用它，而无需事先安装 Gradle。更好的是，保证了构建用户使用构建设计工作时所使用的 Gradle 版本。当然，这对于持续集成服务器（即定期构建项目的服务器）也是很好的，因为它不需要在服务器上配置。

通过运行`wrapper` task 将 Wrapper 安装到项目中。 （此任务始终可用，即使您不将其添加到您的构建）。要指定 Gradle 版本，请在命令行中使用`--gradle-version`。默认情况下，Wrapper 将使用 bin 发布包。这是最小的 Gradle 发布包。某些工具（如 Android Studio 和 Intellij IDEA）在与 all 发布包一起使用时提供额外的上下文信息。您可以使用`--distribution-type`选择不同的 Gradle 分发类型。您还可以设置通过`--gradle-distribution-url` URL 来直接下载  Gradle。如果未指定版本或发布包的URL，则 Wrapper 将配置为使用执行 Wrapper task 的 gradle 版本。因此，如果您使用 Gradle 2.4 运行 Wrapper task，则 Wrapper 配置将默认为2.4版本。

Example 5.1. Running the Wrapper task

执行 gradle wrapper --gradle-version 2.0 输出如下：

```
> gradle wrapper --gradle-version 2.0
:wrapper

BUILD SUCCESSFUL

Total time: 1 secs
```

可以通过在构建脚本中添加和配置 Wrapper task，然后执行它来进一步定制 Wrapper。


Example 5.2. Wrapper task

build.gradle

```
task wrapper(type: Wrapper) {
    gradleVersion = '2.0'
}
```


在这样执行后，您在项目目录中找到以下新的或更新的文件（如果使用 Wrapper task 的默认配置的话）。

Example 5.3. Wrapper generated files

构建布局:

```
simple/
  gradlew
  gradlew.bat
  gradle/wrapper/
    gradle-wrapper.jar
    gradle-wrapper.properties
```

所有这些文件都应该提交到您的版本控制系统。 这只需要做一次。 将这些文件添加到项目后，应使用添加的 gradlew 命令构建项目。 gradlew 命令的使用方式与 gradle 命令完全相同。

如果要切换到 Gradle 的新版本，则不需要重新运行 wrapper 任务。 更改`gradle-wrapper.properties`文件中的相应条目就已经足够，但是如果要利用 Gradle wrapper  中的新功能，则需要重新生成 wrapper  文件。

## 5.3. Configuration 配置


如果您使用 gradlew 运行 Gradle ，则 Wrapper 会检查 Wrapper 的 Gradle 发布包是否可用。如果是这样，它会将所有最初传递给 gradlew 命令的参数委派给这个发布包的 gradle 命令。如果没有找到 Gradle 包，它会首先下载它。

配置 Wrapper 任务时，可以指定要使用的 Gradle 版本。 gradlew 命令将从 Gradle 存储库下载合适的发布包。或者，您可以指定 Gradle 发布包的下载 URL。 gradlew 命令将使用此 URL 下载发布包。如果您既未指定 Gradle 版本也未指定下载 URL，则 gradlew 命令将下载用于生成 Wrapper 文件的 Gradle 版本。

有关如何配置 Wrapper 的详细信息，请参阅 API 文档中的 Wrapper 类。

如果您不希望在通过 gradlew 构建项目时发生任何下载，只需将 Gradle 发布 zip 文件添加到由您的 Wrapper 配置指定的位置的版本控制中。支持相对 URL——您可以指定相对于 gradle-wrapper.properties 文件的位置的发布文件。

如果通过 Wrapper 构建，则会忽略机器上安装的任何现有 Gradle 发布包。

## 5.4. Authenticated Gradle distribution download  验证Gradle分发下载

Gradle Wrapper可以使 用HTTP 基本认证从服务器下载 Gradle 发行版。 这使您能够在专用受保护的服务器上托管 Gradle 分发。 您可以根据使用情况以两种不同的方式指定用户名和密码：作为系统属性或直接嵌入在 distributionUrl 中。 系统属性中的凭据优先于嵌入在 distributionUrl 中的凭证。

>安全警告

>HTTP基本身份验证应仅与HTTPS URL一起使用，而不是纯HTTP身份验证。 使用基本身份验证，用户凭据以明文形式发送。

使用系统属性可以在用户主目录中的`.gradle/gradle.properties`文件中完成，或者通过其他方式完成，请参见第12.1节“通过gradle.properties配置构建环境”。


Example 5.4. Specifying the HTTP Basic Authentication credentials using system properties

gradle.properties

```
systemProp.gradle.wrapperUser=username
systemProp.gradle.wrapperPassword=password
```

将凭据嵌入`gradle/wrapper/gradle-wrapper.properties`文件中的 distributionUrl 中也可以。 请注意，此文件将提交到源代码控制系统。 嵌入在 distributionUrl 中的共享凭据只应在受控环境中使用。

Example 5.5. Specifying the HTTP Basic Authentication credentials in distributionUrl

gradle-wrapper.properties

```
distributionUrl=https://username:password@somehost/path/to/gradle-distribution.zip
```

这可以与代理一起使用，验证与否。 有关如何配置 Wrapper 以使用代理的更多信息，请参见第12.3节“通过代理访问 Web”。

## 5.5. Verification of downloaded Gradle distributions  验证下载的 Gradle 发布包

Gradle Wrapper 允许通过 SHA-256 哈希和比较来验证下载的Gradle 发布包。 这通过防止中间人攻击者篡改下载的 Gradle发布包来提高针对目标攻击的安全性。

要启用此功能，您需要首先计算已知 Gradle 发布包的SHA-256散列。 您可以使用`shasum`命令从 Linux 和 OSX 或 Windows（通过[Cygwin](https://www.cygwin.com/)）生成SHA-256散列。

Example 5.6. Generating a SHA-256 hash

```
> shasum -a 256 gradle-2.4-all.zip
371cb9fbebbe9880d147f59bab36d61eee122854ef8c9ee1ecf12b82368bcf10  gradle-2.4-all.zip
```

将返回的散列和添加到 gradle-wrapper.properties的 distributionSha256Sum 属性。


Example 5.7. Configuring SHA-256 checksum verification

gradle-wrapper.properties

```
distributionSha256Sum=371cb9fbebbe9880d147f59bab36d61eee122854ef8c9ee1ecf12b82368bcf10
```

## 5.6. Unix file permissions 关于 Unix 文件权限

Wrapper 任务添加适当的文件权限，以允许执行 gradlew * NIX 命令。 Subversion 保留此文件的权限。 我们不知道其他版本控制系统如何处理这个。 如果想任何情况下都能工作，就执行`sh gradlew`。
