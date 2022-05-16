Second Extended Filesystem (Ext2)

### 磁盘布局

#### 块组（Block Group）

```c
struct ext2_group_desc
{
    __le32  bg_block_bitmap;        /* Blocks bitmap block */
    __le32  bg_inode_bitmap;        /* Inodes bitmap block */
    __le32  bg_inode_table;     /* Inodes table block */
    __le16  bg_free_blocks_count;   /* Free blocks count */
    __le16  bg_free_inodes_count;   /* Free inodes count */
    __le16  bg_used_dirs_count; /* Directories count */
    __le16  bg_pad;
    __le32  bg_reserved[3];
};
```

```
------------------------------------------------------
| Block Group 1 | Block Group 2 | ... | Block Group N |
------------------------------------------------------
|                \
|									  \
|										  \
|											  \
|												  \
|														\
| Superblock | FS descriptors | Block Bitmap | Inode Bitmap | Inode Table | Data Blocks |
```

#### 超级块（Superblock）

```c
struct ext2_super_block {
	__le32	s_inodes_count;		/* Inodes count */
	__le32	s_blocks_count;		/* Blocks count */
	__le32	s_r_blocks_count;	/* Reserved blocks count */
	__le32	s_free_blocks_count;	/* Free blocks count */
	__le32	s_free_inodes_count;	/* Free inodes count */
	__le32	s_first_data_block;	/* First Data Block */
	__le32	s_log_block_size;	/* Block size */
	__le32	s_log_frag_size;	/* Fragment size */
	__le32	s_blocks_per_group;	/* # Blocks per group */
	__le32	s_frags_per_group;	/* # Fragments per group */
	__le32	s_inodes_per_group;	/* # Inodes per group */
  
  // ...
  
}
struct ext2_sb_info {}
```



#### 索引节点（Inode）

```c
struct ext2_inode {
    // ...

    __le32  i_block[EXT2_N_BLOCKS];/* Pointers to blocks */
    
    // ...
}
```

其中，`EXT2_N_BLOCKS`：https://github.com/torvalds/linux/blob/master/fs/ext2/ext2.h#L228

```c
/*
 * Constants relative to the data blocks
 */
#define	EXT2_NDIR_BLOCKS		12
#define	EXT2_IND_BLOCK			EXT2_NDIR_BLOCKS
#define	EXT2_DIND_BLOCK			(EXT2_IND_BLOCK + 1)
#define	EXT2_TIND_BLOCK			(EXT2_DIND_BLOCK + 1)
#define	EXT2_N_BLOCKS			(EXT2_TIND_BLOCK + 1)
```

15个磁盘块：12个直接块 + 一级块引用（indirect block） + 二级块引用（doubly-indirect block）+ 三级块引用（trebly-indirect block）

#### 目录项（Dentry）

inode号和文件名存储在dentry中：

```c
struct ext2_dir_entry_2 {
    __le32  inode;          /* Inode number */
    __le16  rec_len;        /* Directory entry length */
    __u8    name_len;       /* Name length */
    __u8    file_type;
    char    name[];         /* File name, up to EXT2_NAME_LEN */
};
```

其中，文件名的最大长度`EXT2_NAME_LEN`定义在[linux/include/linux/ext2_fs.h](https://github.com/torvalds/linux/blob/master/include/linux/ext2_fs.h#L23)中：

```c
#define EXT2_NAME_LEN 255
```

https://github.com/torvalds/linux/blob/master/fs/ext2/ext2.h#L163

```c
/*
 * Special inode numbers
 */
#define	EXT2_BAD_INO		 1	/* Bad blocks inode */
#define EXT2_ROOT_INO		 2	/* Root inode */
#define EXT2_BOOT_LOADER_INO	 5	/* Boot loader inode */
#define EXT2_UNDEL_DIR_INO	 6	/* Undelete directory inode */
```

### 问题

1.文件名是如何存储在磁盘中的？


参考文献

[1] Design and Implementation of the Second Extended Filesystem
