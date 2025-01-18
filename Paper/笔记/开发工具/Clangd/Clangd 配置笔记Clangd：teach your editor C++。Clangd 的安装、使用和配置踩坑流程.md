这篇文章是关于 Clangd 配置的笔记，涵盖 Clangd 介绍、下载安装方式、找不到头文件的情况及解决办法、获取编译指令的类型和方法、配置方法（包括全局和局部）、代码风格提示、卡顿处理以及快捷按键等内容，主要针对 Unix 环境。

关联问题: Windows下clangd咋配置 clangd卡如何优化 clangd能支持Python吗

> 公欲善其事，必先利其器。（这个真是孔夫子说的）

读前提醒，这里针对的是 `*unix` 环境，Windows 我没怎么折腾过。

## 一、什么是 Clangd

Clangd（读音：刻朗德）是 C 和 C++ 语言对应的 [LSP（Language Server Protocal）](https://link.juejin.cn/?target=https%3A%2F%2Fmicrosoft.github.io%2Flanguage-server-protocol%2F "https://microsoft.github.io/language-server-protocol/")Server，它的作用是即时地将代码编译到中间语法树 AST 的方式以提供**实时的**语法检查、智能提示、代码格式化等功能，有了 Clangd，你的编辑器（作为 LSP Client）就像 IDE 一样起飞。

## 二、怎么下载 Clangd

通过包管理器，比如 `apt install clangd` 就能快速安装 clangd 了，但是我不太建议这样做，因为它可能不是较新的版本。

在 Visual Studio Code 中，直接安装 clangd 插件即可，在你打开一个 C/C++ 文件的时候，右下角就会跳出 clangd 下载的提示了。等下载完毕，代码解析就会启动了。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c522cca33d764e0c9d642c76a41ea19c~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=327&h=144&s=15303&e=png&b=181818)

需要注意的一点是，clangd 的代码提示和官方的 C/C++ 插件功能冲突，最好选一个用，不过貌似可以关闭官方插件的一些功能，同时享用两者。我没有折腾过，毕竟 clangd 够用了。你可以都尝试一下，喜欢哪个用哪个，不过其他的编辑器可能就只有 clangd 这么一个选项了。

在 Neovim 中，可以通过 [Mason](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fwilliamboman%2Fmason.nvim "https://github.com/williamboman/mason.nvim") 来安装 clangd，这个插件能够安装各种语言的 LSP，并自动配置，可以说是 nvim 用户的必备插件了，直接 `:MasonInstall clangd` 即可，默认好像是放在 `~/.local/share` 里吧。

至于其他的编辑器，可以自行查找一下使用方法 [Getting started (llvm.org)](https://link.juejin.cn/?target=https%3A%2F%2Fclangd.llvm.org%2Finstallation "https://clangd.llvm.org/installation")，基本上都很简单的，Clion 的话，内部就集成了 Clangd，不需要折腾（听说有独家调教）。

## 三、怎么找不到头文件

### Type1. 可能是本来就没有库

如果你是 Windows 用户，因为电脑上根本没有 glibc，你可以尝试用 Mingw 装一个 gcc，msvc 的话可能也可以，至于怎么告知 clangd 库的路径，请看后面 compile\_commands.json 部分。如果你是 Linux 用户，用包管理器安装 gcc 或者 clang 编译器即可。

### Type2. 你有自己的编译工具链

在 Linux 上 clangd 会自动从 `/usr/include` 或者 `/usr/local/include` 找头文件，但是你不希望从这里找，你有自己的编译工具链，请看后面的 compile\_commands.json 部分。

## 四、compile\_commands.json 是什么

这个文件就如它的名字一样，记录了一堆编译指令，就如 `gcc -c main.c -o main.o` 这样的指令。只需要在项目的 **根目录或者 build 目录** 中存在一个 compile\_commands.json，通过这个文件，clangd 就知道需要找的库在哪里，你用的是哪个编译器（比如 `xxxx-gcc ... -I/xxx/xxx`）。

如果你的项目挺简单的，那么照着标准格式写一下也没关系，但是如果项目很大，那写起来是要死的。

## 五、怎么获得 compile\_commands.json

### Type1. 项目本身提供了构建指令

例如 Linux 内核仓库的构建脚本，本身提供了 `make compile_commands.json`，通过修改 Makefile 配置好编译工具链，直接执行这条指令就行了。有了 clangd 加持内核模块（驱动）开发，不再让人害怕了。参考 [stackoverflow.com/a/59325448](https://link.juejin.cn/?target=https%3A%2F%2Fstackoverflow.com%2Fa%2F59325448 "https://stackoverflow.com/a/59325448") 。

### Type2. 项目有 CMake 构建脚本

如果你的项目是用 CMake 构建的，那么在调用 CMake 的时候加上一个 Flag 就可以了：`cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1`，当然你的 cmake 版本不要太老。

### Type3. 项目是纯 Makefile 的

不要慌，这可能是最常见的情景。这个时候，只需要用 [bear](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Frizsotto%2FBear "https://github.com/rizsotto/Bear") 这个工具，在任何使用 `make` 的地方，替换成 `bear make` 即可，一点不会影响正常的编译。在编译目标文件一遍后，就会生成 compile\_commands.json 在 Makefile 的旁边了。~当然，这个方法通用性这么好，自然坑就来得多点，比如还是找不到头文件什么的，这个时候可以修改 Makefile，添加 `-isystem` 手动指定编译工具链路径试试。~ 修改 clangd 的启动参数 `--query-driver=<gcc路径>,<g++路径>`，这个启动参数一般时编辑器的配置文件中设置，因为针对每个代码项目的编译工具链路径不同，所以 VSCode 保存在 Workspace 的层级的 `settings.json`比较好：

```json
{ "clangd.arguments": [ "--query-driver=/opt/arm-ca9-linux-gnueabihf-6.5/bin/arm-ca9-linux-gnueabihf-gcc,/opt/arm-ca9-linux-gnueabihf-6.5/bin/arm-ca9-linux-gnueabihf-g++" ] }
```

## 六、怎么配置 clangd

clangd 其实也是可以配置的，配置文件可以是全局的，也可以是项目局部的。我一般采用全局配置，存放到 `~/.config/clangd/config.yaml` 文件中，最常用的就是添加几个编译指令，解除最多提醒 20 个错误的限制，以及忽略某些错误（别人的代码不好随便改，明明错误一堆，却喜欢在 Makefile 加上 `-Wall`）。

```yaml
CompileFlags:   Add: [-Wno-implicit-function-declaration, -Wno-int-conversion, -ferror-limit=500]
```

配置选项有很多，自己看官方文档吧：[clangd.llvm.org/config](https://link.juejin.cn/?target=https%3A%2F%2Fclangd.llvm.org%2Fconfig "https://clangd.llvm.org/config") ，从配置开始了解 Clangd 的全部功能也是一个不错的主意。

## 七、代码风格提示怎么做

clang-tidy 是代码风格检查、代码格式化工具，可以通过 `apt install clang-tidy` 安装，当然了，你不需要安装它，因为 clangd 提供了实时检查，随时格式化的功能。

在项目的根目录下创建一个 `.clang-format` 文件，然后添加内容：

```yaml
--- BasedOnStyle: Google IndentWidth: 4 DerivePointerAlignment: false PointerAlignment: Left
```

此时使用编辑器的代码格式化功能，就会按照你的配置进行（如果没有效果，打开 Clangd 的日志看看，是不是配置格式错误）。

如果没有这个文件配置，默认的配置是 LLVM 风格。

关于代码风格和细节配置，请查看：[Clang-Format Style Options — Clang 18.0.0git documentation (llvm.org)](https://link.juejin.cn/?target=https%3A%2F%2Fclang.llvm.org%2Fdocs%2FClangFormatStyleOptions.html "https://clang.llvm.org/docs/ClangFormatStyleOptions.html")，我的建议是尽量和团队保持一致风格，配置一次，然后保留起来就行。

## 八、clangd 用着好卡怎么办

刚开始进入项目文件夹是会卡的，因为在构建 index 目录缓存，这些文件会在 .cache 文件夹下保存，记得版本控制器中将它排除。

之后如果还卡也是可能的，如果你是虚拟机运行的 Linux，建议加大点内存，clangd 还是很吃内存的：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fef27eb8256641d0ada5adb3a3a5868f~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=957&h=548&s=157764&e=png&b=020202)

如果是 nvim 开发的话，就尽量不要总是进入和退出，尝试使用 nvim 中嵌入的终端。

## 九、快捷按键都是哪些

这个其实是看各个编辑器 （LSP Client）的了，和 Clangd（LSP Server）没太大关系，一般的编辑器都已经设计好了针对 LSP 的快捷按键绑定，比如 VSCode 的这些都会替换到 LSP 的功能上去：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b58d2dc8488f449c83544b1bd5b0e542~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=258&h=204&s=24933&e=png&b=f9f8f8)

neovim 的话我装了 nvchad，它的按键绑定设计如此：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed3322cac33b4a139ef97594c77aeaf7~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=602&h=601&s=64448&e=png&b=252931)

习惯这些快捷键可以大大加快开发效率。

本文收录于以下专栏

![cover](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1fbac436217e44f59cd2d1e61e0123f3~tplv-k3u1fbpfcp-jj:100:75:0:0:q75.avis#?w=896&h=561&s=796718&e=png&b=302d28)
