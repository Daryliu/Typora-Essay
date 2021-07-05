# OSS对象存储

### Bucket（存储空间）

- 用于存储对象（Object）的容器；
- 所有的对象都必须隶属于某个存储空间；
- 存储空间具有各种配置属性，包括地域、访问权限、存储类型等。用户可以根据实际需求，创建不同类型的存储空间来存储不同的数据。
- 小写字母、数据开头，可加短横线

### 对象

- OSS存储数据的基本单元；
- 没有文件目录层级结构的关系；
- <font color = "bluegreen">由（描述）元信息（Object Meta），用户数据（Data）和文件名（Key=ObjectKey）组成；（key用于标识；meta是键值对,表示了对象的一些属性，比如最后修改时间、大小等信息，同时用户也可以在元信息中存储一些自定义的信息）</font>
  - ObjectKey表示上传的Object所在存储空间的完整名称，即包含文件后缀在内的完整路径，如填写为abc/efg/123.jpg。

### Region（地域）

表示OSS的<font color = "bluegreen">数据中心所在物理位置</font>；Region是在创建Bucket的时候指定的，一旦指定之后就不允许更改。

### Endpoint（访问域名）

表示<font color = "bluegreen">OSS对外服务的访问域名</font>；

### AccessKey（访问密钥）

包含Access Key ID、Access Key Secret

用户注册OSS时，系统会给用户分配一对Access Key ID和Access Key Secret，称为ID对，<font color = "bluegreen">Access Key ID用于标识用户</font>，AccessKeySecret是用户用于加密签名字符串和OSS用来<font color = "bluegreen">验证签名字符串的密钥</font>，必须保密。

### 强一致性

Object操作在OSS上具有原子性，操作要么成功要么失败，不会存在有中间状态的Object。

### 数据冗余机制

OSS使用基于纠删码、多副本的数据冗余存储机制，将每个对象的不同冗余存储在同一个区域内多个设施的多个设备上，确保硬件失效时的数据持久性和可用性。

| 对象存储 OSS                | 文件系统       |
| :-------------------------- | :------------- |
| Object                      | 文件           |
| Bucket                      | 主目录         |
| Region                      | 无             |
| Endpoint                    | 无             |
| AccessKey                   | 无             |
| 无                          | 多级目录       |
| GetService                  | 获取主目录列表 |
| GetBucket                   | 获取文件列表   |
| PutObject                   | 写文件         |
| AppendObject                | 追加写文件     |
| GetObject                   | 读文件         |
| DeleteObject                | 删除文件       |
| 无                          | 修改文件内容   |
| CopyObject （目的和源相同） | 修改文件属性   |
| CopyObject                  | 复制文件       |
| 无                          | 重命名文件     |

#### OSS术语表

| 英文                      | 中文                                           |
| :------------------------ | :--------------------------------------------- |
| Bucket                    | 存储空间                                       |
| Object                    | 对象或者文件                                   |
| Endpoint                  | OSS 访问域名                                   |
| Region                    | 地域或者数据中心                               |
| AccessKey                 | AccessKeyId和AccessKeySecret的统称，访问密钥   |
| Put Object                | 简单上传                                       |
| Post Object               | 表单上传                                       |
| Multipart Upload          | 分片上传                                       |
| Append Object             | 追加上传                                       |
| Get Object                | 简单下载                                       |
| Callback                  | 回调                                           |
| Object Meta               | 文件元信息。用来描述文件信息，例如长度，类型等 |
| Data                      | 文件数据                                       |
| Key                       | 文件名                                         |
| ACL (Access Control List) | 存储空间或者文件的权限                         |