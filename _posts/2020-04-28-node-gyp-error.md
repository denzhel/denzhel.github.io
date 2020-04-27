---
layout: post
title: "macOS: gyp: No Xcode or CLT version detected!"
date:   2020-04-28
categories: Node
---

If you've upgraded your mac to Catalina 10.15.4, xcode probably got broken. 

I realized it when I tried running:

```
npm install
```

This is what I got in return:

```
gyp: No Xcode or CLT version detected!
gyp ERR! configure error 
gyp ERR! stack Error: `gyp` failed with exit code: 1
gyp ERR! stack     at ChildProcess.onCpExit (/Users/dennis/.nvm/versions/node/v12.16.2/lib/node_modules/npm/node_modules/node-gyp/lib/configure.js:351:16)
gyp ERR! stack     at ChildProcess.emit (events.js:310:20)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:275:12)
gyp ERR! System Darwin 19.4.0

```

To fix it, we need to remove the old xcode and reinstall it:

```
xcode-select --print-path

sudo rm -rf OutPutFromPreviousCommand

xcode-select --install
```

After xcode has been installed, try running npm with the latest version of openssl(brew):

```
CPPFLAGS=-I/usr/local/opt/openssl@1.1/include LDFLAGS=-L/usr/local/opt/openssl@1.1/lib npm install
```

Have fun :)
