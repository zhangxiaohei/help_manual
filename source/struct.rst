----------------
struct stat
----------------

查找方法:find /usr/include/ -name '*.h'|xargs grep 'struct stat {'

::

  #include <sys/stat.h>
  struct stat {
        unsigned long   st_dev;       //文件的设备编号     
        unsigned long   st_ino;       //节点
        unsigned long   st_nlink;     //连到该文件的硬连接数目，刚建立的文件值为1
        unsigned int    st_mode;      //文件的类型和存取的权限
        unsigned int    st_uid;       //用户ID
        unsigned int    st_gid;       //组ID
        unsigned int    __pad0;
        unsigned long   st_rdev;      //(设备类型)若此文件为设备文件，则为其设备编号
        long            st_size;      //文件字节数(文件大小)
        long            st_blksize;   //块大小(文件系统的I/O 缓冲区大小)
        long            st_blocks;    //块数      /* Number 512-byte blocks allocated. */
        unsigned long   st_atime;     //最后一次访问时间
        unsigned long   st_atime_nsec;
        unsigned long   st_mtime;     //最后一次修改时间
        unsigned long   st_mtime_nsec;
        unsigned long    st_ctime;     //最后一次改变时间(指属性)
        unsigned long    st_ctime_nsec;
        long                    __unused[3];
  };
