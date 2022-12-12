> Abhishek Vijaya Kumar, Muthian Sivathanu, Quiver: An Informed Storage Cache for Deep Learning. FAST'20



### 设计

![](../../img/paper-fast20-quiver-architecture.png ':size=400x300')

Quiver有三个组成部分：

1. Cache Server：运行在一个单独的容器中
2. Cache Manager：协调多个Cache Server之间的操作
3. Cache Client：与每个DLT任务位于相同的容器中

#### 基于内容寻址的缓存

对于数据集中的每个数据项，使用SHA1等方法计算其内容的哈希值。使用基于内容的寻址方式，其好处在于，多个副本（例如，不同用户存储账号下的ImageNet副本）中的相同数据项将会映射到相同的哈希值，这允许在不同的用户之间重用数据。

为了保证数据隔离，对于输入的训练数据，Quiver使用了`Digest File`（下文称为摘要文件）的概念。对于用户拥有的每个数据集，用户计算好每项的内容哈希，并将哈希值存在一个摘要文件中。摘要文件的格式为

> <content_hash: file_location>

其中，file_location表示特定的数据项在用户的云存储账号中所在的路径和偏移量。

因为DLT任务只会对用户能够访问的数据计算哈希摘要，所以哈希值的存在就表示用户能够访问相应的内容。

由于摘要文件比很小（仅有几MB），他被存储在DLT任务的容器中。

DLT任务首先使用哈希值查找缓存，如果内容不在缓存中，它将使用file_location从远程存储中获取数据然后使用哈希值作为键，将其添加到缓存中。

#### Cache Server

Cache Server是一个分布式的服务，它运行在集群中的所有GPU VM中。其作为一个单独的特权用户（例如，组织的管理员），运行在自己的容器中，因此，运行DLT任务的其他用户不能访问该容器。

DLT任务通过用于lookup和insert的RPC接口与Cache Server进行交互。

从内部来看，Cache Server是一个维护在本地SSD上的键值存储。

#### Cache Manager

它有以下两个职责：

第一，与Cache Clients和Cache Servers进行交互，协调缓存驱逐和替换；

第二，分许DLT任务，测量每个任务从缓存中可能获得的收益。它通过如下的方式分析收益：

对于一些mini-batches，它指示Cache Server对DLT任务读取的所有数据，临时返回缓存不命中。

然后，比较不带缓存的执行时间与带有缓存的执行，使用这一结果来确定缓存放置的优先级。

#### Cache Client

Cache Client作为DLT任务的一部分，运行在用户的容器中，并插入在深度学习框架用于访问数据的接口中。

例如，在PyTorch中，使用DataSet来遍历训练数据，它有一个Next接口去得到下一批训练数据。从内部来看，DataSet维护了一个随机的索引列表，该列表决定了获取数据的顺序。

Quiver扩展了DataSet，使其能够管理摘要文件。当需要从存储中获取一批索引，它首先使用摘要文件中的哈希值查询缓存。