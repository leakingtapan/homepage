---
layout: post
title:  "Dynamically link to Go std library using customized path"
date:   2017-08-28
desc: "Dynamically link to Go std library using customized path"
keywords: "Golang,link,gh-pages,website,blog"
categories: [golang]
tags: [golang,linker]
icon: icon-html
---
While I was learning golang's new feature, sharing golang packages as `.so` file, I found this nice [blog](http://blog.ralch.com/tutorial/golang-sharing-libraries/). It describes how to create a shared library object (DSO) in go and link to it from Go or C. However, the created go std library's link path turned out to be golang's default installation path. In my case it is `/usr/local/go/pkg/linux_amd64_dynlink/libstd.so`. If you are developping software release, this is usually not what you want. There are several problems of this hard coded linking path:
1. What if the host machine doesn't have golang installed?
2. What if the host machine installed golang in a different path?
3. Even if golang is installed in the expected location, how to instruct user to generate the libstd.so file which needs to be generated by running `go install -buildmode=shared -linkshared std`

Then how to link to the shared library that using customized path?

What you need is `ldflags` of `go build` command:
`go build -x -linkshared -ldflags '-L ./lib'` in which `-L` specifis the location of `.so` file to link to. In my case it is a relative path to the exeutable, `./lib`. Then you just need to copy the `libstd.so` into `./lib`. You can verify the link is correct by running:

```
>$ ldd app-d
linux-vdso.so.1 =>  (0x00007fff7f9b1000)
libstd.so => lib/libstd.so (0x00007f824a1d9000)
libcalc.so => /home/ec2-user/project/pkg/linux_amd64_dynlink/libcalc.so (0x00007f8249fd5000)
libc.so.6 => /lib64/libc.so.6 (0x00007f8249c0a000)
libdl.so.2 => /lib64/libdl.so.2 (0x00007f8249a06000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f82497e9000)
/lib64/ld-linux-x86-64.so.2 (0x000055616c3fd000)
```

