---
layout: post
title:  Embedding files into ELF executable
date:   2014-01-01 00:00:01
categories: general
coverimage: /img/covers/boxcat.jpg
weight: 16
---

There are several ways to embed data into ELF binaries, but none were suitable for my needs. So I created [elfdataembed](https://github.com/foxx/elfdataembed), a small library that embeds files into 32/64bit ELF sections, and provides a simple C interface for the runtime to [extract them](http://stackoverflow.com/questions/2900936/packing-a-file-into-an-elf-executable).

This approach allows you to extract embedded data with tiny memory overhead, or pass position/offset reference to an external tool for direct usage without having to extract at all. You can create memory efficient self extracting binaries, or even create read-only loop mounts directly from the binary without any extraction at all. 

Alternative methods are not suitable for large files as they load the entire binary into memory, and make it difficult for external applications to reference the data directly.

### Usage

This project contains a small example app which demonstrates how to use it;

```
$ make

$ build/app
[x] embedded image found at offset 13756, size 1500

$ dd if=build/app of=build/test bs=1 skip=13756 count=1500
1500 bytes (1.5 kB) copied, 0.00259477 s, 578 kB/s

$ md5sum build/image.file build/test
09441e5fd94bf722af5b5bfc2676638f  build/image.file
09441e5fd94bf722af5b5bfc2676638f  build/test
```

There are many alternative ways to embed files into executable, but depending on your use case they may not be suitable.

#### Compile as symbol and link

This is [detailed here](http://www.linuxjournal.com/content/embedding-file-executable-aka-hello-world-version-5967) and [here](http://stackoverflow.com/questions/6785214/how-to-embed-a-file-into-an-executable-file), however this becomes troublesome for large files as the entire binary must be loaded into memory in order to read it (even if you read sequentially). An example project can be found [here](https://github.com/andresmusetti/elf-data).

#### Append to end of file

This is [detailed here](http://stackoverflow.com/questions/4864866/c-c-with-gcc-statically-add-resource-files-to-executable-library) but can cause some anti virus / integrity tools to throw warnings. It also breaks any hash checking you put into your binary.

#### Convert to data array

This is [shown here](http://www.cocoanetics.com/2010/10/embedding-binary-resources/) and [here](http://blog.theroyweb.com/embedding-a-binary-file-as-an-array-in-firmware), but has the same problems as compiling to a symbol afaik.


### Acknowledgements

Thanks to [harryr](https://github.com/harryr) and `freenode/##c` for code review and assistance
