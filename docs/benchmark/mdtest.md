mdtest是一个元数据测试基准测试程序。

### 简介

> the mdtest benchmark which specifically tests the peak metadata rates of storage systems under different directory structures.

### 编译mdtest

mdtest已经被合并进IOR中，因此，只需要按照IOR的编译过程执行即可。

### 基本概念

```shell
Hierarchical directory structure (tree)
   
                                   =======
                                  |       |  (tree node)
                                   =======
                                  /   |   \
                            ------    |    ------
                           /          |          \
                       =======     =======     =======
                      |       |   |       |   |       |    (leaf level)
                       =======     =======     =======
        
    In this example, the tree has a depth of one (z=1) and branching factor of 
    three (b=3).  The node at the top of the tree is the root node.  The level 
    of nodes furthest from the root is the leaf level.  All trees created by 
    mdtest are balanced.
    
    To see how mdtest operates, do a simple run like the following:
    
        mdtest -z 1 -b 3 -I 10 -C -i 3
    
    This command will create a tree like the one above, then each task will 
    create 10 files/dirs per tree node.  Three of these trees will be created 
    (one for each iteration).
```

### 文档

1.https://github.com/MDTEST-LANL/mdtest/tree/master/old

2.https://ior.readthedocs.io/en/latest/
