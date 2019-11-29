## 代理 Checksum 数据库

依照 Go 团队的[这份提案](https://go.googlesource.com/proposal/+/master/design/25530-sumdb.md '这份提案')，Athens Proxy 可以代理 Checksum 数据库。

Athens 默认会接受代理 `https://sum.golang.org`。尽管如此，如果你想重写这个默认行为或是代理更多的 Checksum 数据库，你可以通过 SumDBs 配置或者使用等价的环境变量: `ATHENS_SUM_DBS`。

例如你运行下面这个命令：
```bash
GOPROXY=<athens-url> go build
```

这个 Go 命令会像这样：`<athens-url>/sumdb/sum.golang.org` 代理请求到 `sum.golang.org`，请随意阅读上面的提案链接，以获取使 Athens 成功代理 Checksum 数据库 API 的确切请求。

注意，原文编写时（2019年5月），你需要显示设置 `GOSUMDB=https://sum.golang.org`，但 Go 团队计划默认启用该设置。（我在翻译时，2019年11月，Go 已经默认启用了该设置）

## 为什么需要 Checksum 数据库？
为什么需要 Checksum 数据库的原因可以查看上文提到的提案。尽管如此，下文会有更详细的代理 Checksum 数据库的解释。

## 为什么代理 Checksum 数据库？
这非常重要。假设你是一家正在运行 Athens 实例的公司，并且你不想全世界都知道你的仓库存放在哪儿。举个例子，假设你有一个存放在 `github.com/mycompany/secret-repo` 的私有仓库。
为了确保不让 Go 客户端把请求发送到 `https://sum.golang.org/lookup/github.com/mycompany/secret-repo@v1.0.0` 从而将你的私有导入路径公开泄露，你需要确保你告诉了 Go 跳过
指定导入路径，就像这样：
```bash
GONOSUMDB=github.com/mycompany/* go build
```

这将确保 Go 不会为你的私有导入路径发送任何请求到 Checksum 数据库。尽管如此，要怎样确保你的所有员工都使用了正确的配置来构建私有代码呢？

Athens 在这种情况下，能够确保所有流经它的私有代码永远不会进入 Checksum 数据库。因此，只要你的员工使用 Athens，他们将获得有用的提醒以确保他们的 `GONOSUMDB` 正确的配置。

作为维护 Athens 的公司，你可以使用下面的配置来运行 Athens：
```bash
NoSumPatterns = ["github.com/mycompany/*"] # 或者使用以逗号分隔的环境变量：ATHENS_GONOSUM_PATTERNS
```

这将确保了 Go 发送请求到 `<athens-url/sumdb/sum.golang.org/github.com/mycompany/secret-repo@v1.0.0>` 时，Athens 会返回 403 并且让构建失败，以确保使用者知道有一些配置项不正确且不会泄露那些导入路径。
