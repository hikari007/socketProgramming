# 简介

- **一个简单的时间获取客户程序**

    - **结构体sockaddr_in**

        ```c
        struct sockaddr_in
          {
            __SOCKADDR_COMMON (sin_);
            in_port_t sin_port;			/* Port number.  */
            struct in_addr sin_addr;		/* Internet address.  */
            
            /* Pad to size of `struct sockaddr'.  */
            unsigned char sin_zero[sizeof (struct sockaddr) -
                 __SOCKADDR_COMMON_SIZE -
                 sizeof (in_port_t) -
                 sizeof (struct in_addr)];
          };
        ```
        `sockaddr_in`一共占16个字节，同`sockaddr`，成员如下
        | 成员|字节 |说明 |
        |:---:|:---:|:---:|
        |sin_family|2|地址族|
        |sin_port  |2|端口  |
        |sin_addr  |4|IP地址|
        |sin_zero  |8|填充  |

        ```c
        #define	__SOCKADDR_COMMON(sa_prefix) \
          sa_family_t sa_prefix##family
        ```
        宏定义中`##`表示拼接字符串
        所以`__SOCKADDR_COMMON (sin_)`等同于`sa_family_t sin_family`

    - **套接字函数socket()**
        
        使用<sys/socket.h>头文件的socket()函数来创建套接字,接口如下
    
        '''c
        int socket(int af, int type, int protocol);
        '''

        af(Address Family)，IP地址类型
            |地址族|表示|
            |:---:|:---:|
            |AF_INET|IPV4|
            |AF_INET6|IPV6|





