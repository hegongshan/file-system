### 逻辑卷管理



LVM（Logical Volume Manager，逻辑卷管理）


#### 基本概念

PV：Physical Volume，物理卷

VG：Volume Group，卷组

LV：Logical Volume，逻辑卷

PE：Physical Extent

LE：Logical Extent

图：三层结构

### 实践

|                                 |    PV     |    VG     |    LV     |
| ------------------------------- | :-------: | :-------: | :-------: |
| 创建`*create`                   | pvcreate  | vgcreate  | lvcreate  |
| 查看卷/卷组的概要信息`*s`       |    pvs    |    vgs    |           |
| 列出所有的卷/卷组`*scan`        |  pvscan   |  vgscan   |  lvscan   |
| 查看卷/卷组的详细信息`*display` | pvdisplay | vgdisplay | lvdisplay |
| 扩容`*extend`                   |    ——     | vgextend  | lvextend  |
| 删除`*remove`                   | pvremove  | vgremove  | lvremove  |

dmsetup
