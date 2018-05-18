# 版本化
一个API服务可能提供多个[API接口](https://cloud.google.com/apis/design/glossary#api_interface)，[API版本](https://cloud.google.com/apis/design/glossary#api_version)策略是应用在API 接口层面上而不是API服务层面上的。为了方便起见，下面所有的API均指API接口。

网络API应该使用语义化版本控制（[Semantic Versioning](http://semver.org/)）。对于某一个版本号`MAJOR.MINOR.PATCH`：

* 当你有不兼容的更改时，增加 `MAJOR`版本号。
* 当你以向后兼容的方式添加新的功能时，增加`MINOR`版本号。
* 当你修复了可以向后兼容的bug时，增加`PATCH`版本号。

当API所处的版本不一样时，增加`MAJOR`版本的规则也会不一样：

* 对于API的第一个版本（v1），其`MAJOR`版本号应该加在proto包名称的末尾，例如`google.pubsub.v1`。如果在版本一时，API包含稳定的类型和接口，并且他们在未来也可能会保持不变，这时`MAJOR`版本号就可以被省略，不过这只是极少数的情况，例如`google.protobuf`和`google.longrunning`。
* 对于所有不是v1的版本，`MAJOR`版本号必须加在proto包的末尾，例如，`google.pubsub.v2`。

对于pre-GA版本（如alpha和beta），我们推荐在其版本号后面加上一个相应的后缀。这个后缀应该由pre-release版本名称（例如 alpha，beta）和一个可选的pre-release版本号组成。

版本演进的例子：

 版本      | Proto包   | 描述                              |
|-----------|-----------|-----------------------------------|
| v1alpha   | v1alpha1  | v1 alpha版本.                     |
| v1beta1   | v1beta1   | v1 beta1版本.                    |
| v1beta2   | v1beta2   | v1 beta2版本.               |
| v1test    | v1test    | 用假数据进行测试的内部测试v1版本. |
| v1        | v1        | v1主版本，基本稳定.               |
| v1.1beta1 | v1p1beta1 | v1微小更改后的第一个beta版本.     |
| v1.1      | v1        | v1.1版本的一个小升级.             |
| v2beta1   | v2beta1   | v2 beta 1版本.                    |
| v2        | v2        | v2主版本, 基本稳定.               |

`MINOR`和`PATCH`版本号应该在API的配置和文档里面反映出来，但是他们一定不可以放在proto包名称里面。

注：Google API平台目前没有原生支持`MINOR` 和`PATCH`版本号。每一个 `MAJOR`版本只有一套文档和客户端的库。API的作者需要通过文档和发布日志手动记录`MINOR`和`PATCH`版本号。

API新的主版本一定不能依赖于`这个API`先前的主版本。在了解了依赖性和稳定性风险后，API可以依赖于其他的API，不过一个稳定的API版本一定只能依赖于其他API最新的稳定版本。

在某段时间内，相同API的不同版本必须能够同时在单个客户端中工作。这样才能帮助客户端从旧版API平滑迁移到新版API。

只有过了弃用期（deprecation period），旧的API版本才能被移除。

在多个API中共享的通用稳定的数据类型，例如日期和时间等，应该定义在单独的proto包里面。如果有必要进行不兼容的修改，就必须引入带有新的类型名称或者新的包名称的`MAJOR`版本。

## 向后兼容性
有时候很难去判断一个改变是否是向后兼容的。

下面列出了一些供你快速检索的类型，如果你有疑惑，可以点击查看[兼容页面](https://git.garena.com/shopee/space/api-design-guide/blob/3edf15b1cb386c08d64512a2e1c733f41f34eee0/Compatibility.md)以获取更多的内容。

### 保持向后兼容的修改

* 添加API接口到API服务
* 添加方法到API接口
* 添加HTTP绑定到方法
* 添加字段到请求消息
* 添加字段到响应消息
* 添加值到某个枚举类型
* 添加只输出（output-only）的资源字段

### 不能向后兼容的修改

* 删除/重命名服务、接口、字段名、方法或枚举值
* 修改 HTTP 绑定
* 修改字段类型
* 修改资源名的格式
* 修改已有请求的可见性（visible behavior）
* 修改 HTTP定义中的URL 格式
* 在资源消息中添加读/写字段