===================
结构体
===================

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

----------------
struct passwd
----------------
::

 struct passwd {
     char *pw_name; /* 登录名称 */
     char *pw_passwd; /* 登录口令 */
     uid_t pw_uid; /* 用户 ID */
     gid_t pw_gid; /* 用户组 ID */
     char *pw_gecos; /* 用户的真名 */
     char *pw_dir; /* 用户的目录 */
     char *pw_shell; /* 用户的 SHELL */
 };
 
 
----------------
struct sockaddr_in/in_addr/sockaddr/sockaddr_in6/in6_addr/sockaddr_un
----------------
::

 #include <netinet/in.h>
 /usr/include/linux/in.h
 sizeof = 16
 struct sockaddr_in {
  sa_family_t           sin_family;     /* Address family               */
   __be16                sin_port;       /* Port number                  */
  struct in_addr        sin_addr;       /* Internet address             */

  /* Pad to size of struct sockaddr'. */
  unsigned char         __pad[__SOCK_SIZE__ - sizeof(short int) -
                        sizeof(unsigned short int) - sizeof(struct in_addr)];
 };
 sa_family_t:TCP/IP 网络通信协议:AF_INET
::

 struct in_addr {
    uint32_t       s_addr;     /* address in network byte order */
 }
 s_addr服务端常使用宏INADDR_ANY
::

 #include <sys/socket.h>
 /usr/src/linux-version-number/include/linux/socket.h
 sizeof = 16
 struct sockaddr {
        sa_family_t     sa_family;      /* address family, AF_xxx       */
        char            sa_data[14];    /* 14 bytes of protocol address */
 };
::

 #include <linux/in6.h> or #include <netinet/in.h>
 /usr/include/linux/in6.h
 sizeof = 28
 struct sockaddr_in6 {
     unsigned short int  sin6_family;    /* AF_INET6 */
     __be16          sin6_port;      /* Transport layer port # */
     __be32          sin6_flowinfo;  /* IPv6 flow information */
     struct in6_addr     sin6_addr;      /* IPv6 address */
     __u32           sin6_scope_id;  /* scope id (new in RFC2553) */
 };
 TCP/IP 网络通信协议:AF_INET6
::

 struct in6_addr {
    unsigned char   s6_addr[16];   /* IPv6 address */
 };
::

 #include <sys/socket.h>
 #include <sys/un.h> or #include <linux/un.h>
 /usr/include/linux/un.h:struct sockaddr_un {
 /usr/src/linux-2.6.32.12-0.7/include/linux/un.h:struct sockaddr_un {
 #define UNIX_PATH_MAX   108
 struct sockaddr_un {
    sa_family_t sun_family;               /* AF_UNIX */
    char        sun_path[UNIX_PATH_MAX];  /* pathname */
 };
 UNIX 域协议（文件系统套接字）:A/PF_LOCAL或者A/PF_UNIX
 
----------------
struct msghdr/iovec/hostent/timeval
----------------
::

 #include <sys/socket.h>
 struct msghdr
 {
     void            *msg_name;     //存数据包的目的地址，网络包指向sockaddr
     int             msg_namelen;   //地址长度,通常为 sizeof(sockaddr)或sizeof(sockaddr_in)
     struct iovec    *msg_iov;      //消息内容
      __kernel_size_t msg_iovlen;   //消息内容长度
     void            *msg_control;  //控制信息
     __kernel_size_t msg_controllen;//控制信息长度
     unsigned        msg_flags;     //接收信息标记位
 };
::

 struct iovec
 {
     void __user     *iov_base;
     __kernel_size_t iov_len;
 };
::

 #include <netdb.h>
 /usr/include/netdb.h
 struct hostent
 {
    char    *h_name;                //主机的规范名。例如www.google.com的规范名其实是www.l.google.com。
    char    **h_aliases;            //主机的别名.www.google.com就是google他自己的别名。
    int     h_addrtype;             //主机ip地址的类型，到底是ipv4(AF_INET)，还是pv6(AF_INET6)
    int     h_length;               //主机ip地址的长度
    char    **h_addr_list;          //主机的ip地址，注意，这个是以网络字节序存储的。所以到真正需要打印出这个IP的话，需要调用inet_ntop()。
 };
::

 #include <linux/time.h>
 /usr/include/linux/time.h
 struct timeval {
        __kernel_time_t         tv_sec;         /* seconds */
        __kernel_suseconds_t    tv_usec;        /* microseconds */
 };




