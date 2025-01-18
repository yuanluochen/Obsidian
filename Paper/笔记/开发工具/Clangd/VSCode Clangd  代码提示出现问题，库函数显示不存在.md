### 你有自己的编译工具链

在 Linux 上 clangd 会自动从 `/usr/include` 或者 `/usr/local/include` 找头文件，但是你不希望从这里找，你有自己的编译工具链，请看后面的 compile\_commands.json 部分。

## cmake添加 compile_command.json
默认不开启，开启的两种方法：

在CMakeLists.txt中添加 
```cmake
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```
在命令行中添加-DCMAKE_EXPORT_COMPILE_COMMANDS=1
```bash
cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1 ..
```
开启后，其生成的文件compile_commands.json，包含所有编译单元所执行的指令
