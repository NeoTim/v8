

V8 是个很好的 JS 引擎，我们可以用它做很多好玩的事情，比如包装一些系统API给JS调用。
在使用它之前，我们的首要任务是编译它。
非 win 平台编译 v8 很容易，win 下就麻烦些。
下文简要说明 win 下如何编译 v8 引擎。

先决条件：
安装VS，我整的是 VS2008。

V8 项目已经改由 GYP 生成了，
如果你看了 google 搜索出的资料，那么恭喜你！折腾各个包的路径去和 Scone 去吧！！

现今为止正确方法如下：

1. 得到 V8 项目源码
svn checkout http://t.cn/zWgRq3D

2. V8 源码 build/README.txt 文件看一下。

To generate Visual Studio solution and project files on Windows:
----------------------------------------------------------------

On Windows an additional third party component is required. This is cygwin in
the same version as is used by the Chromium project. This can be checked out
from the Chromium repository. From the root of the V8 project do the following:

> svn co http://t.cn/zWgRq3s third_party/cygwin

To run GYP Python is required and it is recommended to use the same version as
is used by the Chromium project. This can also be checked out from the Chromium
repository. From the root of the V8 project do the following:

> svn co http://t.cn/zWgRq1P third_party/python_26

Now generate Visual Studio solution and project files for the ia32 architecture:

> third_party\python_26\python build/gyp_v8

Now open build\All.sln in Visual Studio.

步骤就是：
1、在 V8 源码目录下建立 third_party 。

2、在此目录下建立 python_26 和 cygwin 目录。

3、在以上目录中分别
svn co http://t.cn/zWgRq3e
svn co http://t.cn/zWgRq3k

4、得到 gyp ：
svn co http://t.cn/zWgRq3F build/gyp
将其放在 V8 源码目录/build/gyp 目录下。

4、进入 cmd ，定位到 V8 源码目录，执行 ：
third_party\python_26\python ..\..\build\gyp_v8
生成工程文件 All.sln

5、VS2008 中打开此工程，执行 bulid。


资料连接：
http://t.cn/zWgRq3g
https://developers.google.com/v8/


第三方 VS 工程中引入 V8 需要注意下面几点：
1、把上面编译好的lib加入到 "工程/属性/配置属性/连接器/命令行" 的附加依赖项中，如：
D:\project\v8_test\v8_project\build\Release\lib\v8_base.lib
D:\project\v8_test\v8_project\build\Release\lib\preparser_lib.lib
D:\project\v8_test\v8_project\build\Release\lib\v8_nosnapshot.lib
D:\project\v8_test\v8_project\build\Release\lib\v8_snapshot.lib
ws2_32.lib
winmm.lib
依赖中必须存在winmm.lib、ws2_32.lib。
代码中 #pragma comment(lib,"ws2_32.lib") 等等也可以 ……

2、把v8原码目录中的 include 目录指定到 "工程/属性/配置属性/C++/常规/附加包含目录"中，如：
D:\project\v8_test\v8_project\include

此外 http://t.cn/zWgRq1v 里面有 hello world 实例可用来测试是否引入成功。

当 V8 是静态库时，如果 v8 版本修正，我们编译新的 v8 后还要编译使用 v8 的工程源码。使它们成为一个执行文件。这样有时候很麻烦。
此时可能需要的是采用动态编译，让 V8 成为一个动态链接库（dll），以后 V8 更新，就单独编译它，覆盖老的链接库就好了，而不用再重新编译我们的工程。

V8 dll 化编译要点：
1、单独生成 v8_nosnapshot 为 v8_nosnapshot.lib 因为 v8_base 依赖 v8_nosnapshot.lib 。
2、修改 v8_base 属性 "工程/属性/配置属性/常规/配置类型" 为 动态库.dll
3、在 v8_base 属性 "工程/属性/配置属性/C++/预处理器/预处理定义" 内添加 "BUILDING_V8_SHARED" 定义项
4、在 v8_base 属性 "工程/属性/配置属性/连接器/命令行" 的附加依赖项中加入：
D:\project\v8_test\v8_project\build\Release\lib\v8_nosnapshot.lib
ws2_32.lib
winmm.lib
5、生成 v8_base 。

V8 dll 的使用：
生成的 v8_base 动态链接库项目中存在两个文件 ：
v8_base.lib
v8_base.dll
其中 v8_base.lib 是在 VS 工程中隐式调用 v8_base.dll 使用的，工程编译后可不需要。
隐式调用 v8_base.dll 方法为：
1、将以上两个文件放置到你的工程文件夹中。
2、在 v8_base 属性 "工程/属性/配置属性/连接器/命令行" 的附加依赖项中加入 v8_base.lib 。
3、把v8原码目录中的 include 目录指定到 "工程/属性/配置属性/C++/常规/附加包含目录"中，如：
D:\project\v8_test\v8_project\include
* 当然，此时你还需要在工程中 #include "v8.h"
4、bulid 工程。
5、编译完成后将 v8_base.dll 放置到 exe 所在目录即可。

当然，你也可以不用隐式调用，使用 LoadLibrary(" v8_base.dll") 在代码动态调用也可以。


V8 JavaScript Engine
=============

V8 is Google's open source JavaScript engine.

V8 implements ECMAScript as specified in ECMA-262.

V8 is written in C++ and is used in Google Chrome, the open source
browser from Google.

V8 can run standalone, or can be embedded into any C++ application.

V8 Project page: https://github.com/v8/v8/wiki


Getting the Code
=============

Checkout [depot tools](http://www.chromium.org/developers/how-tos/install-depot-tools), and run

        fetch v8

This will checkout V8 into the directory `v8` and fetch all of its dependencies.
To stay up to date, run

        git pull origin
        gclient sync

For fetching all branches, add the following into your remote
configuration in `.git/config`:

        fetch = +refs/branch-heads/*:refs/remotes/branch-heads/*
        fetch = +refs/tags/*:refs/tags/*


Contributing
=============

Please follow the instructions mentioned on the
[V8 wiki](https://github.com/v8/v8/wiki/Contributing).
