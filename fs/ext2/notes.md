
# mount

mount -t ramfs -o size=1M ramfs /home/pi/point
mount /dev/sdb /home/pi/point/

# trigger
echo g > /proc/sysrq-trigger


# callstack

## create a inode

#0  ext2_alloc_inode (sb=0xffff88812384f800) at fs/ext2/super.c:182                                    //ext2 sb code, called by vfs if existed
#1  0xffffffff812cf090 in alloc_inode (sb=0xffff88812384f800) at fs/inode.c:211
#2  0xffffffff812d1291 in new_inode_pseudo (sb=<optimized out>) at fs/inode.c:911
#3  0xffffffff812d12f6 in new_inode (sb=<optimized out>) at fs/inode.c:940                             //vfs code,called manually in ext2
#4  0xffffffff813b0a54 in ext2_new_inode (dir=<optimized out>, mode=33188, qstr=<optimized out>) at fs/ext2/ialloc.c:448
#5  0xffffffff813b47bf in ext2_create (dir=0xffff8880abf47c38, dentry=0xffff888108de7800, mode=33188, excl=<optimized out>) at fs/ext2/namei.c:104   //this is registered in the inode_operations
#6  0xffffffff812c1b02 in lookup_open (got_write=<optimized out>, op=<optimized out>, file=<optimized out>, path=<optimized out>, nd=<optimized out>) at fs/namei.c:3235
#7  do_last (op=<optimized out>, file=<optimized out>, nd=<optimized out>) at fs/namei.c:3327
#8  path_openat (nd=0xffffc90002453da0, op=0xffffc90002453ed4, flags=<optimized out>) at fs/namei.c:3536
#9  0xffffffff812c3159 in do_filp_open (dfd=<optimized out>, pathname=<optimized out>, op=0xffffc90002453ed4) at fs/namei.c:3567
#10 0xffffffff812acf84 in do_sys_open (dfd=-100, filename=<optimized out>, flags=<optimized out>, mode=<optimized out>) at fs/open.c:1085
#11 0xffffffff812ad091 in __do_sys_open (mode=<optimized out>, flags=<optimized out>, filename=<optimized out>) at fs/open.c:1103
#12 __se_sys_open (mode=<optimized out>, flags=<optimized out>, filename=<optimized out>) at fs/open.c:1098
#13 __x64_sys_open (regs=<optimized out>) at fs/open.c:1098
#14 0xffffffff8100425a in do_syscall_64 (nr=<optimized out>, regs=0x81a4 <gdt_page+420>) at arch/x86/entry/common.c:293


## write to a inode

#0  ext2_file_write_iter (iocb=0xffffc9000199fe28, from=0xffffc9000199fe50) at fs/ext2/file.c:175
#1  0xffffffff812b0d9d in call_write_iter (file=<optimized out>, iter=<optimized out>, kio=<optimized out>) at ./include/linux/fs.h:1823
#2  new_sync_write (ppos=<optimized out>, len=<optimized out>, buf=<optimized out>, filp=<optimized out>) at fs/read_write.c:474
#3  __vfs_write (file=0xffffc9000199fe28, p=<optimized out>, count=<optimized out>, pos=0xffffc9000199fee8) at fs/read_write.c:487
#4  0xffffffff812b0fd8 in vfs_write (file=0xffff88811e148e00, buf=0xd0a408 "12\n", '\337' <repeats 196 times>, <incomplete sequence \337>..., count=3,
    pos=0xffffc9000199fee8) at fs/read_write.c:549
#5  0xffffffff812b129c in ksys_write (fd=<optimized out>, buf=0xd0a408 "12\n", '\337' <repeats 196 times>, <incomplete sequence \337>..., count=3) at fs/read_write.c:599
#6  0xffffffff812b133a in __do_sys_write (count=<optimized out>, buf=<optimized out>, fd=<optimized out>) at fs/read_write.c:611
#7  __se_sys_write (count=<optimized out>, buf=<optimized out>, fd=<optimized out>) at fs/read_write.c:608
#8  __x64_sys_write (regs=<optimized out>) at fs/read_write.c:608
#9  0xffffffff8100425a in do_syscall_64 (nr=<optimized out>, regs=0xffffc9000199fe50) at arch/x86/entry/common.c:293
#10 0xffffffff81c00088 in entry_SYSCALL_64 () at arch/x86/entry/entry_64.S:238

# different inode

- inode            //vfs inode
- ext2_inode_info  //like a adaptor
- ext2_inode       //disk inode

# different ops(a set of callback)

- ext2_sops                     // a member of super block block, it will implement how to allocate and r/w inode.
- ext2_dir_inode_operations     // a member of inode, it will call ext2_sops finally in the implementation of ext2.
- ext2_dir_operations           // a member of inode, //TODO, in the view of ext2.



