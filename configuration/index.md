## 配置 Athens
在这里，我们将使用各种场景下的配置来配置 Athens 应用。

>本节中，涵盖了许多常用的配置变量，但还有更多的配置变量，如果你想看到所有可以被使用的变量，我们把它们都整理到了[这个配置文件](https://github.com/gomods/athens/blob/master/config.dev.toml)。

### 认证方式
作为开发者，有许多版本控制系统可供我们使用。在本节中，我们将概述如何为 Athens 提供必须的、格式各异的凭证来使用它们。
 
### 存储
Athens 支持很多存储的选项。在本节中，我们将说明如何配置它们。

### 上游代理
在本节中，我们将说明怎样通过配置上游代理来获取 Go 模块仓库中的所有模块，如 [GoCenter](https://gocenter.io), [Go 模块镜像](https://proxy.golang.org), 或者其他的 Athens 服务器。

### 代理 Checksum 数据库

在本节中，我们将说明如何按照以下方式代理 Checksum 数据库 https://go.googlesource.com/proposal/+/master/design/25530-sumdb.md

  - [Checksum](/configuration/sumdb.md)
