===================
接口使用
===================


----------------
getopt_long 命令行解析 
----------------

::

 #include <getopt.h>
 #include <stdio.h>
 #include <stdlib.h>

 void print_usage (FILE* stream, int exit_code,const char* program_name)
 {

    fprintf (stream, "Usage: %s options [ inputfile ... ]\n", program_name);
    fprintf (stream,
            " -h --help Display this usage information.\n"
            " -o --output filename Write output to file.\n"
            " -v --verbose Print verbose messages.\n");
    exit (exit_code);
 }

 int main (int argc, char* argv[])
 {
    int next_option;
    const char* const short_options = "ho:v";
    const struct option long_options[] = {
        { "help", 0, NULL, 'h' },
        { "output", 1, NULL, 'o' },
        { "verbose", 0, NULL, 'v' },
        { NULL, 0, NULL, 0 }
    };
    const char* output_filename = NULL;
    int verbose = 0;
    const char* program_name = argv[0];

    do {
        next_option = getopt_long (argc, argv, short_options,
                long_options, NULL);
        switch (next_option)
        {

            case 'h':
                print_usage (stdout, 0,program_name);
                break;
            case 'o':
                output_filename = optarg;
                if(output_filename)
                    printf("output filename is :%s\n", output_filename);
                break ;
            case 'v':
                verbose = 1;
                 break  ;
            case -1:
                break ;
            default:
                abort ();
                break ;
        }
    }
    while (next_option != -1);

    if (verbose) {
        printf ("Argument: %s\n", "xiaohei");
    }
 }

----------------
dlopen 运行时,加载库
----------------
::

 /* fun.c */
 #include <dlfcn.h> 
 void fun(void)
 {
        printf("xiaohei\n");
 }
 gcc -fPIC -c fun.c
 gcc -shared -o libfun.so fun.o

 /* app.c */
 #include <dlfcn.h>
 int main(void)
 {
        void (*func)(void);
        void *handler = dlopen("./libfun.so",RTLD_LAZY);
        if(!handler)
        {
                printf("%s\n",dlerror());
                return 1;
        }
        func = dlsym(handler, "fun");
     if(!func)
        {
                printf("%s\n",dlerror());
          dlclose(handler);
                return 1;
        }
        func();
        dlclose(handler);
        return 0;
 }

 gcc app.c -o app -ldl //链接libdl 库


----------------
TCP client/server
----------------
::

 TCP client
 #include <stdio.h>
 #include <strings.h>
 #include <sys/types.h>
 #include <sys/socket.h>
 #include <netinet/in.h>
 #include <string.h>

 #define INIT_VALUE 0
 #define BUF_LEN 256

 int main(void)
 {
    int socket_fd = INIT_VALUE, ret = INIT_VALUE;
    ssize_t send_size = INIT_VALUE, read_size = INIT_VALUE;
    char buf[BUF_LEN] = "hello server!";
    struct sockaddr_in addr_in;

    socket_fd = socket(AF_INET, SOCK_STREAM, 0);
    if(-1 == socket_fd)
        perror("call socket failed!");

    bzero(&addr_in, sizeof(addr_in));
    addr_in.sin_family = AF_INET;
    addr_in.sin_port = htons(2000);
    inet_pton(AF_INET, "10.240.197.172",&addr_in.sin_addr);

    ret = connect(socket_fd,(struct sockaddr *)&addr_in, sizeof(addr_in));
    if(-1 == ret)
        perror("call connect failed!");

    send_size = send(socket_fd, buf, strlen(buf), 0);
    printf("write length:%d %s\n",send_size, buf);
    bzero(buf, BUF_LEN);
    read_size = read(socket_fd, buf, BUF_LEN);
    buf[read_size] = 0;
    printf("read length:%d %s\n",read_size, buf);

    close(socket_fd);
    
    return;
 }

::

 TCP server
 #include <stdio.h>
 #include <string.h>
 #include <sys/types.h>
 #include <sys/socket.h>
 #include <netinet/in.h>

 #define INIT_VALUE 0
 #define BUF_LEN 256
 int main(void)
 {
    int socket_fd = INIT_VALUE, connect_fd = INIT_VALUE, ret = INIT_VALUE, output_len = INIT_VALUE, flag = 1;
    ssize_t send_size = INIT_VALUE,read_size = INIT_VALUE;
    char buf[BUF_LEN] = "hello,world!";
    struct sockaddr_in addr_in,addr_output;

    socket_fd = socket(AF_INET, SOCK_STREAM, 0);
    if(-1 == socket_fd)
        perror("call socket failed!");

    bzero(&addr_in, sizeof(addr_in));
    addr_in.sin_family = AF_INET;
    addr_in.sin_port = htons(2000);
    addr_in.sin_addr.s_addr = htonl(INADDR_ANY);

    setsockopt(socket_fd, SOL_SOCKET, SO_REUSEADDR, &flag, sizeof(int));
    ret = bind(socket_fd, (const struct sockaddr *)&addr_in, sizeof(addr_in));
    if(-1 == ret)
        perror("call bind failed!");

    ret = listen(socket_fd, 5);
    if(-1 == ret)
        perror("call listen failed!");

    bzero(&addr_output, sizeof(addr_output));
    output_len = sizeof(addr_output);
    connect_fd = accept(socket_fd, (struct sockaddr *)&addr_output, &output_len);
    if(-1 == connect_fd)
        perror("call accept failed!");

    bzero(buf, BUF_LEN);
    read_size = recv(connect_fd, buf, BUF_LEN, 0);
    printf("read length:%d %s\n",read_size, buf);

    strncat(buf, "-xiaohei", strlen("-xiaohei"));
    send_size = write(connect_fd, buf, strlen(buf));
    printf("write length:%d %s\n",send_size, buf);

    close(connect_fd);
    close(socket_fd);

    return;
 }

----------------
UDP client/server
----------------
::

 UDP client
 #include <stdio.h>
 #include <strings.h>
 #include <sys/types.h>
 #include <sys/socket.h>
 #include <netinet/in.h>
 #include <string.h>

 #define INIT_VALUE 0
 #define BUF_LEN 256

 int main(void)
 {
    int socket_fd = INIT_VALUE;
    ssize_t send_size = INIT_VALUE;
    char buf[BUF_LEN] = { 0 };
    struct sockaddr_in addr_in;

    socket_fd = socket(AF_INET, SOCK_DGRAM, 0);
    if(-1 == socket_fd)
        perror("call socket failed!");

    bzero(&addr_in, sizeof(addr_in));
    addr_in.sin_family = AF_INET;
    addr_in.sin_port = htons(2000);
    inet_pton(AF_INET, "10.240.197.172",&addr_in.sin_addr);

    strncpy(buf, "hello server!", strlen("hello server!"));
    send_size = sendto(socket_fd, buf, strlen(buf), 0, (struct sockaddr *)&addr_in, sizeof(addr_in));
    printf("write length:%d %s\n",send_size, buf);

    bzero(buf, BUF_LEN);
    recvfrom(socket_fd, buf, BUF_LEN, 0, NULL, NULL);
    printf("read length:%d %s\n",strlen(buf), buf);

    close(socket_fd);

    return;
 }

::

 UDP server
 #include <stdio.h>
 #include <string.h>
 #include <sys/types.h>
 #include <sys/socket.h>
 #include <netinet/in.h>

 #define INIT_VALUE 0
 #define BUF_LEN 256
 int main(void)
 {
    int socket_fd = INIT_VALUE, ret = INIT_VALUE, addr_in_len = BUF_LEN, flag = 1;
    ssize_t send_size = INIT_VALUE;
    char buf[BUF_LEN] = { 0 };
    struct sockaddr_in addr_in, addr_output;

    socket_fd = socket(AF_INET, SOCK_DGRAM, 0);
    if(-1 == socket_fd)
        perror("call socket failed!");

    bzero(&addr_in, sizeof(addr_in));
    addr_in.sin_family = AF_INET;
    addr_in.sin_port = htons(2000);
    addr_in.sin_addr.s_addr = htonl(INADDR_ANY);

    setsockopt(socket_fd, SOL_SOCKET, SO_REUSEADDR, &flag, sizeof(int));
    ret = bind(socket_fd, (const struct sockaddr *)&addr_in, sizeof(addr_in));
    if(-1 == ret)
        perror("call bind failed!");

    addr_in_len = sizeof(struct sockaddr_in);

    bzero(buf, BUF_LEN);
    recvfrom(socket_fd, buf, BUF_LEN, 0, (struct sockaddr *)&addr_in, &addr_in_len);
    printf("read length:%d %s\n",strlen(buf), buf);

    strncat(buf, "-xiaohei", strlen("-xiaohei"));
    send_size = sendto(socket_fd, buf, strlen(buf), 0, (struct sockaddr *)&addr_in, addr_in_len);
    printf("write length:%d %s\n",send_size, buf);

    close(socket_fd);

    return;
 }

----------------
inet_aton/inet_addr/inet_ntoa/inet_pton/inet_ntop 地址转换函数
----------------
::

 #include <stdio.h>
 #include <strings.h>
 #include <arpa/inet.h>
 #include <netinet/in.h>

 int main(void)
 {
    int ret = 0;
    struct sockaddr_in addr_in;
    char *str = NULL;
    char str_addr[256] = { 0 };
    char *ip_string = "10.240.197.172";
    unsigned int ip_int = 0xacc5f00a;

    /* inet_aton */
    addr_in.sin_addr.s_addr = 0;
    ret = inet_aton(ip_string, &addr_in.sin_addr);
    printf("inet_aton 000, ret = %d, addr = %x\n",ret, addr_in.sin_addr.s_addr);

    addr_in.sin_addr.s_addr = 0;
    ret = inet_aton(ip_string, NULL);
    printf("inet_aton 111, ret = %d, addr = %x\n",ret, addr_in.sin_addr.s_addr);

    addr_in.sin_addr.s_addr = 0;
    ret = inet_aton("aaa", &addr_in.sin_addr);
    printf("inet_aton 222, ret = %d, addr = %x\n",ret, addr_in.sin_addr.s_addr);

    /* inet_addr */
    addr_in.sin_addr.s_addr = 0;
    addr_in.sin_addr.s_addr = inet_addr("aaa");
    printf("inet_addr aaa, addr = %x\n", addr_in.sin_addr.s_addr);

    addr_in.sin_addr.s_addr = 0;
    addr_in.sin_addr.s_addr = inet_addr(ip_string);
    printf("inet_addr bbb, addr = %x\n", addr_in.sin_addr.s_addr);

    /* inet_ntoa */
    addr_in.sin_addr.s_addr = 123;
    str = inet_ntoa(addr_in.sin_addr);
    printf("inet_ntoa 333, addr = %s\n", str);

    addr_in.sin_addr.s_addr = ip_int;
    str = inet_ntoa(addr_in.sin_addr);
    printf("inet_ntoa 444, addr = %s\n", str);

    /* inet_pton */
    addr_in.sin_addr.s_addr = 0;
    ret = inet_pton(AF_INET, ip_string, &addr_in.sin_addr);
    printf("inet_pton ccc, ret = %d, addr = %x\n",ret, addr_in.sin_addr.s_addr);

    addr_in.sin_addr.s_addr = 0;
    ret = inet_pton(AF_INET, "aaa", &addr_in.sin_addr);
    printf("inet_pton ddd, ret = %d, addr = %x\n",ret, addr_in.sin_addr.s_addr);

    addr_in.sin_addr.s_addr = 0;
    ret = inet_pton(AF_INET6, ip_string, &addr_in.sin_addr);
    printf("inet_pton eee, ret = %d, addr = %x\n",ret, addr_in.sin_addr.s_addr);

    addr_in.sin_addr.s_addr = 0;
    ret = inet_pton(AF_LOCAL, ip_string, &addr_in.sin_addr);
    printf("inet_pton fff, ret = %d, addr = %x\n", ret, addr_in.sin_addr.s_addr);

    /* inet_ntop */
    addr_in.sin_addr.s_addr = ip_int;
    str = (char *)inet_ntop(AF_INET, &addr_in.sin_addr, str_addr, INET_ADDRSTRLEN);
    printf("inet_ntop 555, str = %s, addr = %s\n", str, str_addr);

    bzero(str_addr, 256);
    str = (char *)inet_ntop(AF_INET6, &addr_in.sin_addr, str_addr, INET6_ADDRSTRLEN);
    printf("inet_ntop 666, str = %s, addr = %s\n", str, str_addr);

    bzero(str_addr, 256);
    str = (char *)inet_ntop(AF_LOCAL, &addr_in.sin_addr, str_addr, INET_ADDRSTRLEN);
    printf("inet_ntop 777, str = %s, addr = %s\n", str, str_addr);

    return;
 }

 --result
 inet_aton 000, ret = 1, addr = acc5f00a
 inet_aton 111, ret = 1, addr = 0
 inet_aton 222, ret = 0, addr = 0
 inet_addr aaa, addr = ffffffff
 inet_addr bbb, addr = acc5f00a
 inet_ntoa 333, addr = 123.0.0.0
 inet_ntoa 444, addr = 10.240.197.172
 inet_pton ccc, ret = 1, addr = acc5f00a
 inet_pton ddd, ret = 0, addr = 0
 inet_pton eee, ret = 0, addr = 0
 inet_pton fff, ret = -1, addr = 0
 inet_ntop 555, str = 10.240.197.172, addr = 10.240.197.172
 inet_ntop 666, str = af0:c5ac:850a:4000::a0fb:a02a, addr = af0:c5ac:850a:4000::a0fb:a02a
 inet_ntop 777, str = (null), addr =


----------------
gethostbyname 函数
----------------
::

 #include <stdio.h>
 #include <netdb.h>
 #include <arpa/inet.h>
 #include <strings.h>

 /* 通过域名得到IP地址 */
 int main(int argc, char **argv)
 {
    struct hostent *host;
    char **pptr;
    char str[32];

    if((host = gethostbyname("www.baidu.com")) == NULL)
    {
        printf(" gethostbyname error.\n");
        return 0; 
    }

    switch(host->h_addrtype)
    {
        case AF_INET:
        case AF_INET6:
            for(pptr = host->h_addr_list; NULL != *pptr; pptr++)
            {
                    printf("address:%s\n", inet_ntop(host->h_addrtype, *pptr, str, sizeof(str)));
                    bzero(str, 32);
            }

        break;
        default:
            printf("unknown address type\n");
        break;
    }

    return 0;
 }

