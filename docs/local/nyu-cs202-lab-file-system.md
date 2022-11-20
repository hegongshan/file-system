本文对NYU CS202中的Lab 5 文件系统进行了梳理。

### 练习2：实现alloc_block()

任务描述：实现bitmap.c中的函数alloc_block()。

```c
// Search the bitmap for a free block and allocate it.  When you
// allocate a block, immediately flush the changed bitmap block
// to disk.
//
// Return block number allocated on success,
// -ENOSPC if we are out of blocks.
//
// Hint: use free_block as an example for manipulating the bitmap.
int alloc_block(void);
```

先来看下free_block是如何实现的：

```c
// Check to see if the block bitmap indicates that block 'blockno' is free.
// Return 1 if the block is free, 0 if not.
bool block_is_free(uint32_t blockno)
{
    if (super == 0 || blockno >= super->s_nblocks)
        return 0;
    if (bitmap[blockno / 32] & (1 << (blockno % 32)))
        return 1;
    return 0;
}

// Mark a block free in the bitmap. 
// Silently does nothing if passed blockno == 0
void free_block(uint32_t blockno)
{
    // Blockno zero is the null pointer of block numbers.
    // Simplify code paths elsewhere by silently doing nothing
    // on free_block(0).
    if (blockno == 0)
        return;

    bitmap[blockno / 32] |= 1 << (blockno % 32);
}
```

1.`bitmap`和`super`定义在disk_map.h中：

```c
extern uint32_t			*bitmap;
extern struct superblock	*super;

void	 flush_block(void *addr);
```

2.`struct superblock`定义在fs_types.h中：

```c
struct superblock {
    uint32_t	s_magic; // Magic number: FS_MAGIC.
    uint32_t	s_nblocks; // Total number of blocks on disk.
    uint32_t	s_root; // Inum of the root directory inode.
};
```

3.根据上述代码可以发现，当块处于空闲状态时，位图中的相应位为1；否则，相应位为0。

`blockno / 32`确定了块的相应位在哪个整数里，而`blockno % 32`确定了块的相应位是该整数中的第几位。

为什么是32呢？因为bitmap是32位无符号整型指针`uint32_t *`。

```c
// 判断块是否空闲
bitmap[blockno / 32] & (1 << (blockno % 32))
```

基于上述理解，可以着手实现函数alloc_block()：

```c

```

5分到手>_<
$$
\begin{cases}
1 + x + y = N \\\\
x \times \mathrm{BLKBITSIZE} = y
\end{cases} \\\\
\Rightarrow x = \frac{N - 1}{\mathrm{BLKBITSIZE + 1}}, y=\frac{(N - 1) \times \mathrm{BLKBITSIZE}}{\mathrm{BLKBITSIZE} + 1}
$$


### 练习3：实现inode_block_walk和inode_get_block

任务描述：实现inode.c中的函数inode_block_walk和inode_get_block。

> `inode_block_walk`  finds the disk block number slot for the 'filebno'th block in inode 'ino', and sets '*ppdiskbno' to point to that slot. `inode_get_block` goes one step further and sets `*blk` to the start of the block, such that by using `*blk`, we can access the contents of the block. It also allocates a new block if necessary.

#### inode_block_walk

```c
// Find the disk block number slot for the 'filebno'th block in inode 'ino'.
// Set '*ppdiskbno' to point to that slot.  The slot will be one of the
// ino->i_direct[] entries, an entry in the indirect block, or an entry
// in one of the indirect blocks referenced by the double-indirect block.
//
// When 'alloc' is set, this function will allocate an indirect block or
// a double-indirect block (and any indirect blocks in the double-indirect
// block) if necessary.
//
// Returns:
//	0 on success (but note that **ppdiskbno might equal 0).
//	-ENOENT if the function needed to allocate an indirect block, but
//		alloc was 0.
//	-ENOSPC if there's no space on the disk for an indirect block.
//	-EINVAL if filebno is out of range (it's >= N_DIRECT + N_INDIRECT +
//               N_DOUBLE).
//
// Hints:
//  - You may find it helpful to draw pictures.
//  - Don't forget to clear any block you allocate.
//  - Recall that diskaddr() converts from a disk block to an in-memory address
//  - You may end up writing code with a similar structure three times.
//  It may simplify your life to factor it into a helper function. 
int inode_block_walk(struct inode *ino, uint32_t filebno, uint32_t **ppdiskbno, bool alloc);
```

1.注释中提到的`diskaddr()`定义在disk_map.h中：

```c
uint8_t                 *diskmap;

// Maps a block number to an address.  The pointer returned
// points to the first byte of the specified block in mapped memory.
void *diskaddr(uint32_t blockno)
{
    if (blockno == 0 || (super && blockno >= super->s_nblocks))
        panic("bad block number %08x in diskaddr", blockno);
    return (char *)(diskmap + blockno * BLKSIZE);
}
```

2.参数中的`struct inode *ino`定义在fs_types.h中:

```c
struct inode {

    // Block pointers.
    // A block is allocated iff its value is != 0.
    uint32_t	i_direct[N_DIRECT]; // Direct blocks.
    uint32_t	i_indirect; // Indirect block.
    uint32_t	i_double; // Double-indirect block.
};
```

下面着手实现：

```c

```



#### inode_get_block

```c
// Set *blk to the address in memory where the filebno'th block of
// inode 'ino' would be mapped.  Allocate the block if it doesn't yet
// exist.
//
// Returns 0 on success, < 0 on error.  Errors are:
//	-ENOSPC if a block needed to be allocated but the disk is full.
//	-EINVAL if filebno is out of range.
//
// Hint: Use inode_block_walk and alloc_block.
int inode_get_block(struct inode *ino, uint32_t filebno, char **blk);
```

### 练习4

> Implement `inode_truncate_blocks` in `inode.c`. `inode_truncate_blocks` frees data and metadata blocks that an inode allocated but no longer needs. This function is used, for instance, when an inode is deleted; the space reserved by the inode must be freed so that other files can be created on the system.



```c
// Remove any blocks currently allocated for inode "ino" that would
// not be needed for an inode of size "newsize" (where newsize is smaller
// than ino->i_size).  Do not change ino->i_size.
//
// For both the old and new sizes, compute the number of blocks required,
// and then free the blocks from new_nblocks to old_nblocks.  If new_nblocks
// is no more than NDIRECT and the indirect block has been allocated, then
// free the indirect block.  Do the same for the double-indirect block if
// new_nblocks is no more than NDIRECT + NINDIRECT.  Don't forget to free
// the indirect blocks allocated in the double-indirect block!
//
// Hints:
// - Use inode_free_block to free all the data blocks, then use
// free_block to free the meta-data blocks (for example, the indirect block).
// - the ROUNDUP macro may be helpful
// - Note that we do not need to explicitly free the blocks pointed to
// by the indirect block (ask yourself: where are those blocks freed?)
static void
inode_truncate_blocks(struct inode *ino, uint32_t newsize);
```

实现：

```c

```

### 练习5

#### inode_unlink

```c
// Unlink an inode by decrementing its link count and zeroing the name
// and inum fields in its associated struct dirent.  If the link count
// of the inode reaches 0, free the inode.
//
// Returns 0 on success, or -ENOENT if the file to be unlinked does
// not exist.
//
// Hint: Use walk_path and inode_free.  You will need to take advantage
// of walk_path setting the pdent parameter to point to the directory
// entry associated with the file to be unlinked.
int inode_unlink(const char *path);
```

#### inode_link

```c
// Link the inode at the location srcpath to the new location dstpath.
// Increment the link count on the inode.
//
// Returns 0 on success, < 0 on failure.  In particular, the function
// should fail with -EEXIST if a file exists already at dstpath.
//
// Hint: Use walk_path and dir_alloc_dirent.
int inode_link(const char *srcpath, const char *dstpath)
```



### 参考资料

1.CS202: Lab 5: File system，https://cs.nyu.edu/~mwalfish/classes/21sp/labs/lab5.html

2.labs-21sp仓库，https://github.com/nyu-cs202/labs-21sp

3.我的实现：https://github.com/hegongshan/nyu-cs202-labs

