---
layout: post
title: AWS ELB Listener Error
date:   2020-06-12
categories: Amazon
---
While trying to communicate with an AWS ELB I've set up for Mongo Mongos, I've got the following error:
```bash
ubuntu@xxxxxx:~/reset-organization$ mongo --host xxxx.xxxxx.xx <database> --eval "db.<name>.remove({})"
MongoDB shell version v4.2.7
connecting to: mongodb://xxxxxxx:27017/xxxxx?compressors=disabled&gssapiServiceName=mongodb
2020-06-09T08:04:22.212+0000 I  NETWORK  [js] recv(): message msgLen 1347703880 is invalid. Min 16 Max: 48000000
2020-06-09T08:04:22.212+0000 I  NETWORK  [js] DBClientConnection failed to receive message from xxxxxxxx:27017 - ProtocolError: recv(): message msgLen 1347703880 is invalid. Min 16 Max: 48000000
2020-06-09T08:04:22.212+0000 E  QUERY    [js] Error: network error while attempting to run command 'isMaster' on host 'xxxxxxxx:27017'  :
connect@src/mongo/shell/mongo.js:341:17
@(connect):2:6
2020-06-09T08:04:22.214+0000 F  -        [main] exception: connect failed
2020-06-09T08:04:22.214+0000 E  -        [main] exiting with code 1
```

To overcome this error, go to the ELB:
```bash
AWS Console -> EC2 -> Load Balancer -> <ELB Name> -> Listeners
```

Change the listener from HTTP to TCP.
