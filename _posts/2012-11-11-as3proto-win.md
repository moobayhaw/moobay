---
layout: post
title: "在WIN系统使用AS3版protobuf"
date: 2012-11-11 17:39
comments: true
categories: 
---

[1]: http://code.google.com/p/protobuf-actionscript3/ "http://code.google.com/p/protobuf-actionscript3/"
[2]: http://mingw.org/wiki/Getting_Started "http://mingw.org/wiki/Getting_Started"
[3]: http://sourceforge.net/projects/mingw/files/Installer/mingw-get-inst/ "http://sourceforge.net/projects/mingw/files/Installer/mingw-get-inst/"
[4]: https://github.com/larryhou/as3proto-osx  "Actionscript3.0版protobuf"

本文介绍了在WIN系统布置AS3版protobuf运行环境的方法，在阅读本文之前需要先下载[AS3版protobuf源码][4]。<!--more-->
  
## 安装[MinGW][2]  
* 下载[mingw-get installer][3]最新版本，保持默认配置一路“Next”直至安装完成
* 把安装目录C:\MinGW\bin添加PATH环境变量，注销电脑重新进入系统
* 打开命令提示符运行  

```sh
mingw-get install gcc g++ mingw32-make msys-base
```
	
		
* 初始化MSYS shell并启动  

```bat
cd C:\MSYS\1.0
		
rem 启动MSYS shell
msys.bat
		
rem 初始化MSYS shell，第一次启动时运行
/postinstall/pi.sh
```
	
	
## 编译安装 protobuf 命令行
* CD到源码目录     
  
如果源码在D:/as3proto-osx/sdk/project目录，需要使用

```sh
cd /d/as3proto-osx/sdk/project
```
		
* 编译安装protobuf  

```sh
./configure --prefix=/mingw
		
make		
make check		
make install
```
	
* 检查是否安成功
  
打开命令提示符，输入

```sh
protoc --help
```
		
安装成功后应该出现如下信息，包含--as3_out参数

<pre>
Usage: protoc [OPTION] PROTO_FILES
Parse PROTO_FILES and generate output based on the options given:
-IPATH, --proto_path=PATH   Specify the directory in which to search for
                          imports.  May be specified multiple times;
                          directories will be searched in order.  If not
                          given, the current working directory is used.
--version                   Show version info and exit.
-h, --help                  Show this text and exit.
--encode=MESSAGE_TYPE       Read a text-format message of the given type
                          from standard input and write it in binary
                          to standard output.  The message type must
                          be defined in PROTO_FILES or their imports.
--decode=MESSAGE_TYPE       Read a binary message of the given type from
                          standard input and write it in text format
                          to standard output.  The message type must
                          be defined in PROTO_FILES or their imports.
--decode_raw                Read an arbitrary protocol message from
                          standard input and write the raw tagalue
                          pairs in text format to standard output.  No
                          PROTO_FILES should be given when using this
                          flag.
-oFILE,                     Writes a FileDescriptorSet (a protocol buffer,
--descriptor_set_out=FILE defined in descriptor.proto) containing all of
                          the input files to FILE.
--include_imports           When using --descriptor_set_out, also include
                          all dependencies of the input files in the
                          set, so that the set is self-contained.
--error_format=FORMAT       Set the format in which to print errors.
                          FORMAT may be 'gcc' (the default) or 'msvs'
                          (Microsoft Visual Studio format).
--plugin=EXECUTABLE         Specifies a plugin executable to use.
                          Normally, protoc searches the PATH for
                          plugins, but you may specify additional
                          executables not in the path using this flag.
                          Additionally, EXECUTABLE may be of the form
                          NAME=PATH, in which case the given plugin name
                          is mapped to the given executable even if
                          the executable's own name differs.
--as3_out=OUT_DIR           Generate ActionScript source file.
--cpp_out=OUT_DIR           Generate C++ header and source.
--java_out=OUT_DIR          Generate Java source file.
--python_out=OUT_DIR        Generate Python source file.
</pre>

## 使用 protobuf 生成AS3代码
* 直接使用命令行 
 
```sh
protoc --proto_path=[proto path] --as3_out=[folder for as3 saving] [proto file]
```
		
* 使用 protobuf.bat 生成      
进入sdk目录，打开setup.bat文件，初始化一下配置
	* 把PROTO_DIR修改为\*.proto文件的存储目录
	* 把OUTPUT_DIR修改为生成AS3代码的输出目录
	
其实在默认配置下，直接运行protobuf.bat输入hello既可以在OUTPUT_DIR目录生成实例代码  

```bat
----------------------------
Type proto file name:
hello

... ... ...

D:\C++\as3proto-osx\sdk>protoc --proto_path="..\proto" --as3_out="..\output" "..\proto\hello.proto"

DONE! Press any key to process another proto file...
```
		
	

