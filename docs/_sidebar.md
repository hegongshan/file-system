* 存储设备
    * 设备管理
        * [数据存储单位](/device/data-storage-unit.md)
        * [设备查看](/device/disk-list.md)
        * [设备检测](/device/disk-check.md)
        * [设备分区](/device/disk-partition.md)
        * [RAID](/device/raid.md)
        * [LVM](/device/lvm.md)
        * [dd](/device/dd.md)
* 文件系统基础
    * [文件与目录管理](/basic/file-and-directory-manage.md)
    * 删除文件
        * [粉碎文件](/basic/shred.md)
    * 文件系统操作
        * [元数据操作](/basic/posix-metadata-operation.md)
        * [数据操作](/basic/posix-data-operation.md)
    * 文件属性
        * [基本属性](/basic/attr.md)
        * [扩展属性](/basic/xattr.md)
        * [其他属性](/basic/hidden-attr.md)
    * 权限管理
        * [保护位](/basic/rwx.md)
        * [访问属性列表](/basic/acl.md)
* 本地文件系统
  * 内核文件系统
    * e2fsprog
      * [mklost+found](/mklost+found.md)
    * [Ext2](/file-system-ext2.md) 
  * 用户态文件系统
    * [FUSE](/fuse.md)
  * 文件系统Lab
    * [Lab1: NYU CS202 ls](/nyu-cs202-lab-ls.md)
    * [Lab2: NYU CS202 File System](/nyu-cs202-lab-file-system.md)
  * 常用命令
    * [文件系统的使用](/file-system-usage.md)
* [分布式文件系统](/distributed-file-system.md)
    * 文件共享
        * [网络文件系统](/network-file-system.md)
        * [Samba](/samba.md)
  * 可靠性
    * [复制](/replication.md)
    * 纠删码
      * [Reed Solomon编码](/erasure-coding/reed-solomon.md)
      * [LRC编码](/erasure-coding/locally-repairable-code.md)
      * [再生码](/erasure-coding/regenerating-code.md)
  * [共识算法](/consensus/README.md)
  * [数据同步](/sync/README.md)
    * [rsync](/sync/rsync.md)
* 开源系统
    * Ceph
        * [介绍](/ceph/intro.md)
        * [安装CephFS](/ceph/install.md)
        * [OSD管理](/ceph/osd.md)
        * [Cluster Map工具](/ceph/cluster-map.md)
        * [共识](/ceph/mon/paxos.md)
        * 测试工具
            * [RADOS bench](/ceph/rados-bench.md)
            * [RBD bench](/ceph/rbd-bench.md)
* 并行处理
    * [OpenMPI](/mpi/install.md)
* 数据压缩
    * [gzip](/compression/gzip.md)
    * [稀疏矩阵](/compression/sparse-matrix.md)
* Storage for AI
    * [DNN训练流程](/storage-for-ai/README.md)
    * [GNN](/storage-for-ai/nn/GNN.md)
    * HDF5
        * [介绍](/hdf5/introduction.md)
        * [安装HDF5](/hdf5/install.md)
        * 实用命令行工具
            * [h5dump](/hdf5/tool/h5dump.md)
            * [h5ls](/hdf5/tool/h5ls.md)
            * [h5import](/hdf5/tool/h5import.md)
        * [HDFView](/hdf5/tool/HDFView.md)
        * API
            * [C API](/hdf5/c-api.md)
            * [Python API](/hdf5/python-api.md)
        * [文件格式](/hdf5/file-format.md)
        * [文件锁](/hdf5/file-locking.md)
        * 测试工具
            * [h5perf](/hdf5/tool/h5perf.md)
            * [h5bench](/hdf5/h5bench.md)
    * TensorFlow
        * [tf.data](/storage-for-ai/tensorflow/data.md)
        * [TFRecord格式](/storage-for-ai/tensorflow/TFRecord.md)
        * [支持HDF5](/storage-for-ai/tensorflow/HDF5.md)
        * [输入流水线](/storage-for-ai/tensorflow/input-pipeline.md)
        * [Model Garden](/storage-for-ai/tensorflow/model-garden.md)
    * PyTorch
        * [Dataloader](/storage-for-ai/pytorch/dataloader.md)
* 测试工具
    * [FIO](/benchmark/fio.md)
    * [Filebench](/benchmark/filebench.md)
    * [mdtest](/benchmark/mdtest.md)
* 重要会议
    * [FAST](/venue/fast.md)
    * [SOSP](/venue/sosp.md)
    * [OSDI](/venue/osdi.md)
    * [ATC](/venue/atc.md)
    * [SC](/venue/sc.md)
