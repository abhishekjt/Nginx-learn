#### Issue:

Seeing Forbidden error messages in `/var/log/nginx/error.log` while trying to serve content from a path other than nginx default path:

```
curl -v -H "Host: example.com" localhost

> Host: example.com
>
< HTTP/1.1 403 Forbidden
< Server: nginx/1.17.5
< Date: Sat, 09 Nov 2019 14:39:44 GMT
< Content-Type: text/html
< Content-Length: 153
< Connection: keep-alive
<
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.17.5</center>
</body>
</html>
```

```
2019/11/09 14:21:18 [error] 30188#30188: *6 "/var/www/example.com/html/index.html" is forbidden (13: Permission denied), client: 127.0.0.1, server: example.com, request: "GET / HTTP/1.1", host: "example.com"
2019/11/09 14:21:22 [error] 30188#30188: *7 "/var/www/example.com/html/index.html" is forbidden (13: Permission denied), client: 127.0.0.1, server: example.com, request: "GET / HTTP/1.1", host: "example.com"
2019/11/09 14:22:34 [error] 30188#30188: *8 "/var/www/example.com/html/index.html" is forbidden (13: Permission denied), client: 127.0.0.1, server: example.com, request: "GET / HTTP/1.1", host: "example.com"
2019/11/09 14:39:44 [error] 30188#30188: *9 "/var/www/example.com/html/index.html" is forbidden (13: Permission denied), client: 127.0.0.1, server: example.com, request: "GET / HTTP/1.1", host: "example.com"
```

#### Root Cause:
> SELinux: When SELinux is enabled, any path other than the default nginx ie `/usr/share/nginx/html/` will not have permission to serve the content. 


#### Fix:
> Need to add the fcontext(Manage file context mapping definitions) for the custom path, If u want to serve the content from `/var/www/example.com/html`

```
semanage fcontext -a -t httpd_sys_content_t '/var/www/example.com/html(/.*)?'
```
