# 配置存储

<a name="TeXh6"></a>
## 存储

Athens 代理支持许多存储类型：

1. 内存
1. 磁盘
1. Mongo
1. Google Cloud Storage
1. AWS S3
1. Minio
  1. DigitalOcean Spaces
  1. 阿里云 OSS
  1. 以及其他 S3 / Minio 兼容接口
7. Azure Blob Storage

它们全部都可以通过 `config.toml` 进行配置，你需要给 `StorageType` 设置一个正确的驱动，或者你可以在你的服务器设置 `ATHENS_STORAGE_TYPE` 环境变量。 此外，大多数驱动需要你提供额外的配置数据，下面将对其进行说明。

<a name="nc5i4"></a>
## 内存

该存储不需要任何特定的配置，默认情况下 Athens 项目也会使用这种存储。它将所有数据写入到本地磁盘的 `tmp` 目录。

**这种存储类型只应在开发时使用！**

**配置：**

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "memory"
```


<a name="Cc6ME"></a>
## 磁盘

磁盘存储允许模块被存储在文件系统中。可以设置存储模块的磁盘位置。

配置：

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "disk"

[Storage]
    [Storage.Disk]
        RootPath = "/path/on/disk"
```


<a name="T1uZ4"></a>
## Mongo

该驱动使用 Mongo 服务器作为数据存储。启动这个驱动时将会在 Mongo 服务器创建一个名为 `ahtens` 的数据库以及名为 `module` 的集合。

**配置：**

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "mongo"

[Storage]
    [Storage.Mongo]
        # 完整的 mongo 存储的 URL
        # 重写环境变量：ATHENS_MONGO_STORAGE_URL
        URL = "mongodb://127.0.0.1:27017"

        # 非必须参数
        # 用于 mongo 连接的证书的路径
        # 重写环境变量：ATHENS_MONGO_CERT_PATH
        CertPath = "/path/to/cert/file"

        # 非必须参数
        # 允许 mongo 存储使用不安全的 SSL/http 连接
        # 应该只在测试或开发时使用
        # 重写环境变量：ATHENS_MONGO_INSECURE
        Insecure = false

        # 非必须参数
        # 允许使用自定义数据库
        # 重写环境变量：ATHENS_MONGO_DEFAULT_DATABASE
        DefaultDBName = athens

        # 非必须参数
        # 允许使用自定义集合
        # 重写环境变量：ATHENS_MONGO_DEFAULT_COLLECTION
        DefaultCollectionName = modules
```


<a name="IzqnB"></a>
## Google Cloud Storage

该驱动使用 [Google Cloud Storage](https://cloud.google.com/storage/) 并假设你已经有账户和 `bucket` 。如果你从未使用过 Google Cloud Storage，这里是[快速入门指南](https://cloud.google.com/storage/docs/creating-buckets#storage-create-bucket-console)，里面有如何创建 `bucket` 。

**配置：**

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "gcp"

[Storage]
    [Storage.GCP]
        # 用于 GCP 存储的ProjectID 
        # 重写环境变量：GOOGLE_CLOUD_PROJECT
        ProjectID = "YOUR_GCP_PROJECT_ID"

        # 用于 GCP 存储的 Bucket
        # 重写环境变量：ATHENS_STORAGE_GCP_BUCKET
        Bucket = "YOUR_GCP_BUCKET"
```


<a name="i4eA4"></a>
## AWS S3

该驱动使用 [AWS S3](https://aws.amazon.com/cn/s3/) 并假设你已经有账户和 `bucket`。如果你从未使用过 Amazon Web 服务，这里是[快速入门指南](https://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html)，里面有如何创建 `bucket`。之后，你可以可以在 `config.toml` 文件设置凭证。如果 key ID 和 secret acess key 没有在 `config.toml` 中指定，驱动将尝试从安装期间创建的[AWS CLI  配置文件](https://docs.aws.amazon.com/zh_cn/cli/latest/userguide/cli-configure-files.html)去加载默认配置文件的凭证。

**配置：**

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "s3"

[Storage]
        [Storage.S3]
        ### S3 的身份认证模型如下所示，按照下面的排序
        ### 如果指定了 AWS_CREDENTIALS_ENDPOINT 环境变量，并且它返回一个有效的结果，则使用它进行认证。
        ### 如果设置了配置变量，并且它们都合法，若它们返回有效的结果，则使用它进行认证。
        ### 否则，它将默认为默认配置，如下所示
        # 尝试在环境中寻找凭证，在共享的配置（~/.aws/credentials）和 ec2 实例的角色凭证。
        # 查看 https://godoc.org/github.com/aws/aws-sdk-go#hdr-Configuring_Credentials
        # 和 https://godoc.org/github.com/aws/aws-sdk-go/aws/session#hdr-Environment_Variables
        # 用于作用于 aws 配置的环境变量。
        # 设置 UseDefaultConfiguration 将只使用默认配置，在将来的发行版本中将会过期，建议不要使用它。

        # S3 存储使用的地区(region)
        # 重写环境变量：AWS_REGION
        Region = "MY_AWS_REGION"

        # S3 存储使用的 Access Key
        # 重写环境变量：AWS_ACCESS_KEY_ID
        Key = "MY_AWS_ACCESS_KEY_ID"

        # S3 存储使用的 Secret Key
        # 重写环境变量：AWS_SECRET_ACCESS_KEY
        Secret = "MY_AWS_SECRET_ACCESS_KEY"

        # S3 使用的 Session Token
        # 非必要参数
        # 重写环境变量：AWS_SESSION_TOKEN
        Token = ""

        # S3 使用的 Bucket
        # 重写环境变量：ATHENS_S3_BUCKET_NAME
        Bucket = "MY_S3_BUCKET_NAME"

        # 如果为 true，则默认的 aws 配置将被使用。
        # 这将会尝试在环境中寻找凭证，在共享的配置（~/.aws/credentials）和 ec2 实例的角色凭证。
        # 查看 https://godoc.org/github.com/aws/aws-sdk-go#hdr-Configuring_Credentials
        # 和 https://godoc.org/github.com/aws/aws-sdk-go/aws/session#hdr-Environment_Variables
        # 用于作用于 aws 配置的环境变量。
        # 重写环境变量：AWS_USE_DEFAULT_CONFIGURATION
        UseDefaultConfiguration = false

        # https://docs.aws.amazon.com/sdk-for-go/api/aws/credentials/endpointcreds/
        # 请注意，设置 AwsContainerCredentialsRelativeURI时，此 URI 不应以 / 结尾
        # 重写环境变量：AWS_CREDENTIALS_ENDPOINT
        CredentialsEndpoint = ""

        # 重写环境变量：AWS_CONTAINER_CREDENTIALS_RELATIVE_URI
        # 如果你计划使用 AWS Fargate，请为 CredentialsEndpoint 为 http://169.254.170.2
        # 参考：https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-metadata-endpoint-v2.html
        AwsContainerCredentialsRelativeURI = ""

        # 一个可选的 endpoint URL（仅主机名或完全限定的 URI）
        # 它将为 S3 存储客户端覆盖默认生成的 endpoint。
        # 重写环境变量：AWS_ENDPOINT
        Endpoint = ""
```


<a name="Minio"></a>
## Minio

[Minio](https://www.minio.io/) 是一个开源对象存储服务器，为 S3 兼容块存储提供接口。你过你从未使用过 minio，你可以阅读这个[快速入门指南](https://docs.minio.io/)。Athens 通过 minio 接口支持任何 S3 兼容的对象存储。在下面，你可以找到我们为 Minio 提供的不同配置选项。下面提供了 Digital Ocean 和阿里云 OSS 块存储的示例配置。

<a name="41ce711f"></a>
##### 配置:

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "minio"

[Storage]
    [Storage.Minio]
        # Minio 存储使用的 Endpoint
        # 重写环境变量：ATHENS_MINIO_ENDPOINT
        Endpoint = "127.0.0.1:9001"

        # Minio 存储使用的 Access Key
        # 重写环境变量：ATHENS_MINIO_ACCESS_KEY_ID
        Key = "YOUR_MINIO_ACCESS_KEY"

        # Minio 存储使用的 Secret Key
        # 重写环境变量：ATHENS_MINIO_SECRET_ACCESS_KEY
        Secret = "YOUR_MINIO_SECRET_KEY"

        # 为 Minio 连接启用 SSL
        # 默认值为 true
        # 重写环境变量：ATHENS_MINIO_USE_SSL
        EnableSSL = false

        # Minio 存储使用的 Bucket
        # 重写环境变量：ATHENS_MINIO_BUCKET_NAME
        Bucket = "gomods"
```

<a name="fe0bd49f"></a>
#### DigitalOcean Spaces

为了使 Athens 与 [DigitalOcean Spaces](https://www.digitalocean.com/products/spaces/) 进行通信，我们使用 Minio 驱动，因为 DigitalOcean Spaces 试图[完全兼容它](https://developers.digitalocean.com/documentation/spaces/)。

<a name="41ce711f-1"></a>
##### 配置:

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "minio"

[Storage]
    [Storage.Minio]
        # DO Spaces 存储使用的 Endpoint
        # 重写环境变量：ATHENS_MINIO_ENDPOINT
        Endpoint = "YOUR_ADDRESS.digitaloceanspaces.com"

        # DO Spaces 存储使用的 Access Key
        # 重写环境变量：ATHENS_MINIO_ACCESS_KEY_ID
        Key = "YOUR_DO_SPACE_KEY_ID"

        # DO Spaces 存储使用的 Secret Key
        # 重写环境变量：ATHENS_MINIO_SECRET_ACCESS_KEY
        Secret = "YOUR_DO_SPACE_SECRET_KEY"

        # 启用 SSL
        # 重写环境变量: ATHENS_MINIO_USE_SSL
        EnableSSL = true

        # DO Spaces 存储使用的 Space 名称
        # 重写环境变量：ATHENS_MINIO_BUCKET_NAME
        Bucket = "YOUR_DO_SPACE_NAME"

        # DO Spaces 存储使用的地区(region)
        # 重写环境变量：ATHENS_MINIO_REGION
        Region = "YOUR_DO_SPACE_REGION"
```

<a name="1defc666"></a>
#### 阿里云 OSS

为了使 Athens 与[阿里云 OSS](https://www.alibabacloud.com/product/oss) 进行通信，我们使用 Minio 驱动。

<a name="41ce711f-2"></a>
##### 配置:

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "minio"

[Storage]
    [Storage.Minio]
        # OSS 存储使用的 Endpoint
        # 重写环境变量：ATHENS_MINIO_ENDPOINT
        Endpoint = "YOUR_ADDRESS.aliyuncs.com"

        # OSS 存储使用的 Access Key
        # 重写环境变量：ATHENS_MINIO_ACCESS_KEY_ID
        Key = "YOUR_OSS_KEY_ID"

        # OSS 存储使用的 Secret Key
        # 重写环境变量：ATHENS_MINIO_SECRET_ACCESS_KEY
        Secret = "YOUR_OSS_SECRET_KEY"

        # 启用 SSL
        # 重写环境变量: ATHENS_MINIO_USE_SSL
        EnableSSL = true

        # OSS 存储的父目录
        # 重写环境变量：ATHENS_MINIO_BUCKET_NAME
        Bucket = "YOUR_OSS_FOLDER_PREFIX"
```

<a name="a7c6a598"></a>
## 
<a name="PA2Jf"></a>
## Azure Blob Storage

该驱动使用 [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)。

> 如果你从未使用过 Azure Blog Storage，这里是一个[快速入门](https://aka.ms/azureblob-quickstart)。


假设你具备了如下条件:

- [一个 Azure storage 账户](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [凭证 (storage account key)](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)
- 一个容器 (用于存储 blobs)

<a name="41ce711f-3"></a>
##### 配置:

```
# StorageType 设置代理将会使用的存储后端
# 重写环境变量：ATHENS_STORAGE_TYPE
StorageType = "azureblob"

[Storage]
    [Storage.AzureBlob]
    		# Azure Blob 使用的存储账户名
        # 重写环境变量：ATHENS_AZURE_ACCOUNT_NAME
        AccountName = "MY_AZURE_BLOB_ACCOUNT_NAME"

        # 存储账户使用的 Account Key
        # 重写环境变量：ATHENS_AZURE_ACCOUNT_KEY
        AccountKey = "MY_AZURE_BLOB_ACCOUNT_KEY"

        # blob 存储使用的容器名称
        # 重写环境变量：ATHENS_AZURE_CONTAINER_NAME
        ContainerName = "MY_AZURE_BLOB_CONTAINER_NAME"
```
