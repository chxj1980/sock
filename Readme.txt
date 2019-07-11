##### 封装以下socket接口
```javascript
inline int socket_init(void);
inline int socket_cleanup(void);
inline int socket_geterror(void);

inline socket_t socket_tcp(void);
inline socket_t socket_udp(void);
inline socket_t socket_raw(void);
inline socket_t socket_rdm(void);
inline int socket_close(socket_t sock);

inline int socket_connect(IN socket_t sock, IN const struct sockaddr* addr, IN socklen_t addrlen);
inline int socket_connect_ipv4(IN socket_t sock, IN const char* ip_or_dns, IN unsigned short port);
inline int socket_connect_ipv4_by_time(IN socket_t sock, IN const char* ip_or_dns, IN unsigned short port, IN int timeout);

// MSDN: When using bind with the SO_EXCLUSIVEADDR or SO_REUSEADDR socket option,
//       the socket option must be set prior to executing bind to have any affect
inline int socket_bind(IN socket_t sock, IN const struct sockaddr* addr, IN socklen_t addrlen);
inline int socket_bind_any(IN socket_t sock, IN unsigned short port);

inline int socket_listen(IN socket_t sock, IN int backlog);
inline socket_t socket_accept(IN socket_t sock, OUT struct sockaddr* addr, INOUT socklen_t* addrlen);

// socket read/write
inline int socket_send(IN socket_t sock, IN const void* buf, IN size_t len, IN int flags);
inline int socket_recv(IN socket_t sock, OUT void* buf, IN size_t len, IN int flags);
inline int socket_sendto(IN socket_t sock, IN const void* buf, IN size_t len, IN int flags, IN const struct sockaddr* to, IN socklen_t tolen);
inline int socket_recvfrom(IN socket_t sock, OUT void* buf, IN size_t len, IN int flags, OUT struct sockaddr* from, OUT socklen_t* fromlen);

/// @return <0-timeout/error, >0-read bytes
inline int socket_send_by_time(IN socket_t sock, IN const void* buf, IN size_t len, IN int flags, IN int timeout); // timeout: ms, -1==infinite
/// @return <0-timeout/error, >0-read bytes
inline int socket_send_all_by_time(IN socket_t sock, IN const void* buf, IN size_t len, IN int flags, IN int timeout); // timeout: ms, -1==infinite
/// @return 0-connection closed, <0-timeout/error, >0-read bytes
inline int socket_recv_by_time(IN socket_t sock, OUT void* buf, IN size_t len, IN int flags, IN int timeout); // timeout: ms, -1==infinite
/// @return 0-connection closed, <0-timeout/error, >0-read bytes(always is len)
inline int socket_recv_all_by_time(IN socket_t sock, OUT void* buf, IN size_t len, IN int flags, IN int timeout);  // timeout: ms, -1==infinite

inline int socket_send_v(IN socket_t sock, IN const socket_bufvec_t* vec, IN size_t n, IN int flags);
inline int socket_recv_v(IN socket_t sock, IN socket_bufvec_t* vec, IN size_t n, IN int flags);
inline int socket_sendto_v(IN socket_t sock, IN const socket_bufvec_t* vec, IN size_t n, IN int flags, IN const struct sockaddr* to, IN socklen_t tolen);
inline int socket_recvfrom_v(IN socket_t sock, IN socket_bufvec_t* vec, IN size_t n, IN int flags, IN struct sockaddr* from, IN socklen_t* fromlen);

// Linux: select may update the timeout argument to indicate how much time was left
inline int socket_select(IN int n, IN fd_set* rfds, IN fd_set* wfds, IN fd_set* efds, IN struct timeval* timeout);
inline int socket_select_readfds(IN int n, IN fd_set* fds, IN struct timeval* timeout);
inline int socket_select_writefds(IN int n, IN fd_set* fds, IN struct timeval* timeout);
inline int socket_select_read(IN socket_t sock, IN int timeout); // 1-read able, 0-timeout, <0-forever
inline int socket_select_write(IN socket_t sock, IN int timeout); // 1-write able, 0-timeout, <0-forever
inline int socket_readable(IN socket_t sock); // 1-read able, 0-can't, <0-error
inline int socket_writeable(IN socket_t sock); // 1-write able, 0-can't, <0-error

// socket options
inline int socket_setkeepalive(IN socket_t sock, IN int enable); // keep alive
inline int socket_getkeepalive(IN socket_t sock, OUT int* enable);
inline int socket_setlinger(IN socket_t sock, IN int onoff, IN int seconds); // linger
inline int socket_getlinger(IN socket_t sock, OUT int* onoff, OUT int* seconds);
inline int socket_setsendbuf(IN socket_t sock, IN size_t size); // send buf
inline int socket_getsendbuf(IN socket_t sock, OUT size_t* size);
inline int socket_setrecvbuf(IN socket_t sock, IN size_t size); // recv buf
inline int socket_getrecvbuf(IN socket_t sock, OUT size_t* size);
inline int socket_setsendtimeout(IN socket_t sock, IN size_t seconds); // send timeout
inline int socket_getsendtimeout(IN socket_t sock, OUT size_t* seconds);
inline int socket_setrecvtimeout(IN socket_t sock, IN size_t* seconds); // recv timeout
inline int socket_getrecvtimeout(IN socket_t sock, OUT size_t* seconds);
inline int socket_setreuseaddr(IN socket_t sock, IN int enable); // reuse addr.
inline int socket_getreuseaddr(IN socket_t sock, OUT int* enable);

// socket status
inline int socket_setnonblock(IN socket_t sock, IN int noblock); // non-block io, 0-block, 1-nonblock
inline int socket_setnondelay(IN socket_t sock, IN int nodelay); // non-delay io(Nagle Algorithm), 0-delay, 1-nodelay
inline int socket_getunread(IN socket_t sock, OUT size_t* size); // MSDN: Use to determine the amount of data pending in the network's input buffer that can be read from socket s

inline int socket_getname(IN socket_t sock, OUT char* ip, OUT unsigned short* port);
inline int socket_getpeername(IN socket_t sock, OUT char* ip, OUT unsigned short* port);

// socket utility
inline int socket_isip(IN const char* ip); // socket_isip("192.168.1.2") -> 0, socket_isip("www.google.com") -> -1
inline int socket_ip(IN const char* ip_or_dns, OUT char* ip);
inline int socket_addr_ipv4(OUT struct sockaddr_in* addrin, IN const char* ip_or_dns, IN unsigned short port);

inline void socket_setbufvec(INOUT socket_bufvec_t* vec, IN int idx, IN void* ptr, IN size_t len);

// multicast
inline int socket_multicast_join(IN socket_t sock, IN const char* group, IN const char* source, IN const char* local);
inline int socket_multicast_leave(IN socket_t sock, IN const char* group, IN const char* source, IN const char* local);
inline int socket_multicast_join_source(IN socket_t sock, IN const char* group, IN const char* source, IN const char* local);
inline int socket_multicast_leave_source(IN socket_t sock, IN const char* group, IN const char* source, IN const char* local);
```
