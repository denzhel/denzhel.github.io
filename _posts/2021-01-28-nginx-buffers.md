---
layout: post
title: Increase Nginx buffers due to errros
date:   2021-01-12
categories: Nginx
---
If you're getting the following error in your nginx logs:
```bash
[error] 12345#0: upstream sent too big header while reading response header from upstream,
```

You need to increase the buffers for that specific location inside nginx configuration:
```bash
server {
  listen        80;
  server_name   example.com;

  location / {
    proxy_pass       http://someUpstream;
    proxy_buffer_size          128k;
    proxy_buffers              4 256k;
    proxy_busy_buffers_size    256k;
  }
}
```
