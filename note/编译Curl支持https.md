## 1.1.0之前的OpenSSL

[参考链接](<http://www.cnblogs.com/openiris/p/3812443.html> )

### 编译OpenSSL

#### 环境

* 下载并安装 [ActivePerl ](http://www.activestate.com/activeperl/downloads)

  >使用版本： ActivePerl-5.26.3.2603-MSWin32-x64-a95bce075.exe

* 下载并安装 [Nasm 汇编器](http://www.nasm.us)，将安装路径添加到系统环境变量 Path 中 

  > Latest version --> Stable(稳定版) -->Win32
  >
  > 使用版本：nasm-2.14.02-installer-x86.exe
  >
  > 设置环境变量：D:\Program Files (x86)\NASM（你的安装路径）

* 下载OpenSSL源码 [OpenSSL](<https://www.openssl.org/source/> )

  > 使用版本：openssl-1.0.2e.tar.gz

* 下载libcurl源码 [libcurl](<https://curl.haxx.se/download/> )

  >  使用版本：curl-7.63.0

#### 使用汇编器NASM编译OpenSSL库

1. 点击 **开始**->**所有程序**->**Microsoft Visual Studio 2010**->**Visual Studio Tools**->**Visual Studio 命令提示(2010)** 

2. 进入OpenSSL的工作目录 

   ```
   cd /d E:\MyProgram\libcurl\openssl-1.0.2e\openssl-1.0.2e
   ```

3. 新建一个编译好的程序的输出目录 

   ```
   mkdir E:\MyProgram\libcurl\openssl-1.0.2e\openssl-1.0.2e_dll
   ```

4. 配置OpenSSL的安装目录 

   ```
   perl Configure VC-WIN32 --prefix=E:\MyProgram\libcurl\openssl-1.0.2e\openssl-1.0.2e_dll
   ```

5. 生存Makefile 文件 

   ```
   ms\do_nasm
   ```

6. 开始编译 

   * 动态编译		E:\MyProgram\libcurl\openssl-1.0.2e\openssl-1.0.2e_dll

     ```
     nmake -f ms\ntdll.mak			//开始编译
     nmake -f ms\ntdll.mak test		//可以测试有没有编译成功
     nmake -f ms\ntdll.mak clean		//可以清理编译结果
     nmake -f ms\ntdll.mak install	//安装到第四步设置的输出目录  
     ```

   	 静态编译		E:\MyProgram\libcurl\openssl-1.0.2e\openssl-1.0.2e_lib

     ```
     nmake -f ms\nt.mak
     nmake -f ms\nt.mak test
     nmake -f ms\nt.mak clean
     nmake -f ms\nt.mak install
     ```

7. 编译完成，安装目录结构

   > bin		: 动态库，主要有 libeay32.dll ssleay32.dll
   >
   > include	: 头文件
   >
   > lib		: 静态库，主要有 libeay32.lib ssleay32.lib
   >
   > ssl		:



### 编译 libcurl 支持https

#### 使用nmake编译libcurl 

1. 点击 **开始**->**所有程序**->**Microsoft Visual Studio 2010**->**Visual Studio Tools**->**Visual Studio 命令提示(2010)** 

2. 进入libcurl 的工作目录 

   ```
   cd /d E:\MyProgram\libcurl\curl-7.63.0\winbuild
   ```

3. 开始编译

   ```
   //静态OpenSSL：
   nmake /f Makefile.vc mode=dll VC=9 WITH_DEVEL=E:\MyProgram\libcurl\openssl-1.0.2e\openssl-1.0.2e_lib WITH_SSL=static ENABLE_SSPI=no ENABLE_IPV6=no ENABLE_IDN=no debug=no
   
   // ENABLE_IDN=no 
   
   //动态OpenSSL：
   nmake /f Makefile.vc mode=dll VC=9 WITH_DEVEL=E:\MyProgram\libcurl\openssl-1.0.2e\openssl-1.0.2e_dll WITH_SSL=dll ENABLE_SSPI=no ENABLE_IPV6=no ENABLE_IDN=no debug=no
   ```
   **不加debug=no 会导致出现： libcurl error LNK2019: 无法解析的外部符号 impIdnToAscii@20 ？？？**

   ```
   命令注释
   
   - mode=dll
     编译libcurl位动态链接库，如果static 就是把libcurl编译位静态库 
   - VC=9
     代表使用的是VC2008
   - WITH_DEVEL=E:\MyProgram\libcurl\openssl-1.0.2e\openssl-1.0.2e_lib
     表示用到第三方开发包的目录，本例上面已经将openssl编译好的开发包，安装到此目录
     默认目录是deps，多个库使用的话可以把库集中到此目录（未测试）
   - WITH_SSL=static
     代表使用libssl库 是静态库
   - ENABLE_SSPI=no
     禁用SSPI功能
   - ENABLE_IPV6=no
     禁用ipV6功能
   - debug=no
     编译release版库，如果是yes则是debug版
     默认是no，编译的时候没有加这一句报错，不知道为什么
   
   ```

4. 编译完成，清理

   ```
   nmake clean 
   ```

5. 生成目录: E:\MyProgram\libcurl\curl-7.63.0\builds



#### 使用vs2008编译libcurl

1. 将编译生成的OpenSSL存放到指定目录

   新建目录openssl\inc32，将生成的include\openssl复制到这个目录

   新建目录openssl\build\Win32\VC9\DLL Debug和openssl\build\Win32\VC9\DLL Release，将libeay32.lib和ssleay32.lib复制到这个目录

   > openssl与curl-7.63.0同级目录，具体如何存放看libcurl的项目属性
   >
   > 也可以自己添加对应属性，不用复制文件

2. libcurl项目属性 - 链接器 - 输入 - 附加依赖项 ：添加Crypt32.lib，否则会报错

   > 无法解析的外部符号 __imp__CertFreeCertificateContext@4 等错误

3. 生成解决方案，生成成功后，文件在build\Win32\VC9\DLL Debug - DLL OpenSSL目录下



----------------------------------------------------------------------------------------------------------------------------

------

------

## 1.1.0之后的OpenSSL

### OpenSSL的版本差别

* [参考链接]( <https://blog.csdn.net/xiongya8888/article/details/84640400> )

* 使用旧版本openssl时，需要设置两个回调

  ```
  The documentation on OpenSSL threads states (at least for version 1.0.2):
  OpenSSL can safely be used in multi-threaded applications provided that at least two
  callback functions are set, locking_function and threadid_func.
  ```

* 但是在1.1.0以及更新的版本中，不再需要这个设置了

  ```
  The ChangeLog of version 1.1.0 states:
  OpenSSL now uses a new threading API. It is no longer necessary to
  set locking callbacks to use OpenSSL in a multi-threaded environment. There
  are two supported threading models: pthreads and windows threads. It is
  also possible to configure OpenSSL at compile time for “no-threads”. The
  old threading API should no longer be used. The functions have been
  replaced with “no-op” compatibility macros.
  ```

* 主要是为了线程安全地使用 OpenSSL

### 编译openssl-1.1.1b

* [参考链接](<https://blog.csdn.net/lolzhzhzh/article/details/81217828> )

* 第一次编译的时候需要安装  dmake 

  ppm install dmake 

1. 动态编译
   * cd /d E:\MyProgram\libcurl\openssl-1.1.1b\openssl-1.1.1b
   * perl Configure VC-WIN32 no-asm no-tests --prefix=E:\MyProgram\libcurl\openssl-1.1.1b\openssl-1.1.1b_dll
   * nmake
   * nmake install
   * nmake clean 

2. 静态编译
   - cd /d E:\MyProgram\libcurl\openssl-1.1.1b\openssl-1.1.1b
   - perl Configure VC-WIN32 no-shared no-asm no-tests --prefix=E:\MyProgram\libcurl\openssl-1.1.1b\openssl-1.1.1b_lib
   - nmake
   - nmake install
   - nmake clean 
3. 重新编译的时候先nmake clean 

```
perl Configure VC-WIN32 [no-shared] [no-asm] [no-tests] [--debug] -D_WIN32_WINNT=0x0501 --prefix=E:\MyProgram\libcurl\openssl-1.1.1b\openssl-1.1.1b_dll

/**
 * 
 * -D_WIN32_WINNT=0x0501	xp需要，其他版本不清楚
 * no-shared为编译静态库，不加此项默认编译出的是动态库
 * no-tests为不需要tests功能，如果只需要openssl的库可以加上此项，否则可能会出很多错误导致编译不过
 * --debug为编译debug版，不加此项默认编译出的是release版
 */

```

 

### 编译libcurl 支持 https

#### VS2008编译libcurl

1. 选择DLL Debug - DLL OpenSSL

   配置属性 - C/C++ - 常规 - 附加包含目录

   `..\..\..\..\..\openssl\inc32` ---> `..\..\..\..\..\openssl-1.1.1b\openssl-1.1.1b_lib\include`

2. 配置属性 - 链接器 - 常规 - 附加库目录

   `..\..\..\..\..\openssl\build\Win32\VC9\DLL Debug`  --->  `..\..\..\..\..\openssl-1.1.1b\openssl-1.1.1b_lib\lib`

3. 配置属性 - 链接器 - 输入 - 附加依赖项

   ws2_32.lib wldap32.lib libeay32.lib ssleay32.lib --> ws2_32.lib wldap32.lib libcrypto.lib libssl.lib Crypt32.lib

4. 清理解决方案，重新生成解决方案，生成路径：curl-7.64.1\build\Win32\VC9\DLL Debug - DLL OpenSSL



## libcurl 进行HTTPS传输





