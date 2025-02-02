

在使用 CMake 构建系统时，指定头文件的包含路径是非常常见的一步。对于这个任务，CMake 提供了两种[主要的](https://so.csdn.net/so/search?q=%E4%B8%BB%E8%A6%81%E7%9A%84&spm=1001.2101.3001.7020)命令：`INCLUDE_DIRECTORIES` 和 `target_include_directories`。虽然它们看似类似，但它们的作用范围、应用方式以及适用场景却有很大差别。

### 1\. 什么是头文件包含目录？

在编译 C++ 项目时，头文件提供了声明和接口，是源代码文件间相互引用的关键部分。为了告诉编译器去哪里查找这些头文件，通常需要指定一个或多个**包含目录**（include directories）。如果没有正确设置头文件路径，编译器会报出类似 "file not found" 的错误。因此，如何有效管理和设置这些路径对于构建项目至关重要。

CMake 作为一个跨平台的构建系统，提供了多种方式来为项目设置包含目录，最常用的就是 `INCLUDE_DIRECTORIES` 和 `target_include_directories`。

### 2\. `INCLUDE_DIRECTORIES` —— 全局包含目录

`INCLUDE_DIRECTORIES` 是 CMake 中最早的用于设置包含路径的命令，它的效果是**全局性的**。也就是说，当你在 CMake 文件中调用 `INCLUDE_DIRECTORIES` 时，它会将指定的包含路径应用到当前作用域下的所有目标（targets）。所有在该命令之后的目标都会继承这些包含路径。

#### 语法：

> **`INCLUDE_DIRECTORIES(<directories>)`**

#### 使用示例：

> **`INCLUDE_DIRECTORIES(${CMAKE_SOURCE_DIR}/include)`**

这条指令会将项目根目录下的 `include` 目录添加为全局的包含目录。这样，所有的目标（如库、可执行文件）都可以从这个目录中查找头文件。

#### 适用场景：

-   **小型项目**：在简单项目中，只有少量的源文件和库，头文件目录也很集中。此时使用 `INCLUDE_DIRECTORIES` 是一个快速且方便的选择。
-   **简单的全局依赖**：如果项目中的所有目标都依赖于同一组头文件路径，这种全局设置方式非常有效。

#### 优点：

-   **简单易用**：只需一次调用，即可为所有目标设置头文件搜索路径。
-   **快速实现**：适合快速搭建或验证简单项目。

#### 缺点：

-   **全局性**：一旦设置，后续所有的目标都会使用这些包含目录，难以做到细粒度的控制。这会增加项目规模和复杂性时的管理难度。
-   **可维护性差**：如果项目变得复杂，全局的包含目录可能会导致不必要的依赖，或者由于头文件冲突而导致编译问题。
-   **潜在污染**：多个模块共用一组全局的头文件路径，可能会引发意想不到的问题，如头文件覆盖或模块间的耦合度过高。

___

### 3\. `target_include_directories` —— 精确控制每个目标的包含目录

为了克服 `INCLUDE_DIRECTORIES` 的全局性问题，CMake 引入了 `target_include_directories` 命令，它允许开发者为**特定的目标**设置包含目录。与 `INCLUDE_DIRECTORIES` 不同，这个命令具有更强的作用范围控制能力，并且支持三种不同的作用域：`PRIVATE`、`PUBLIC` 和 `INTERFACE`，帮助管理目标之间的依赖关系。

#### 语法：

> **`target_include_directories(<target> [PRIVATE|PUBLIC|INTERFACE] <directories>)`**

-   **`PRIVATE`**：仅对当前目标可见，依赖于这个目标的其他目标不会继承这些包含目录。
-   **`PUBLIC`**：对当前目标和依赖于该目标的其他目标都可见。
-   **`INTERFACE`**：当前目标不会使用这些包含目录，但依赖于它的其他目标可以使用。

#### 使用示例：

> **`add_library(MyLibrary mylibrary.cpp) target_include_directories(MyLibrary PUBLIC ${CMAKE_SOURCE_DIR}/include PRIVATE ${CMAKE_SOURCE_DIR}/src )`**

在这个例子中，`MyLibrary` 会使用 `include` 和 `src` 目录中的头文件：

-   **PUBLIC** 的 `include` 目录不仅会对 `MyLibrary` 可见，还会对依赖于 `MyLibrary` 的目标可见。
-   **PRIVATE** 的 `src` 目录则仅对 `MyLibrary` 自己可见，外部目标不会继承这个路径。

#### 适用场景：

-   **大型项目**：在复杂的项目中，每个库或模块可能都有自己的依赖和头文件路径。这时，`target_include_directories` 能提供更加精细的控制。
-   **模块化开发**：当项目采用模块化或库的方式进行构建时，每个模块的头文件路径通常是独立的。使用 `target_include_directories` 可以避免头文件路径之间的冲突，并且可以很好地管理库与库之间的依赖关系。
-   **公共和私有头文件区分**：当一个库需要向外部暴露部分头文件（比如库的 API），但不希望暴露其内部实现的头文件时，可以利用 `PUBLIC` 和 `PRIVATE` 来进行管理。

#### 优点：

-   **精确控制**：每个目标可以独立管理自己的包含目录，减少不必要的全局污染。
-   **作用域管理**：通过 `PRIVATE`、`PUBLIC` 和 `INTERFACE`，可以更灵活地控制头文件路径的可见性，从而更好地管理模块间的依赖关系。
-   **可维护性强**：在大型项目中，能够清晰地定义每个模块的头文件依赖，提升代码的可维护性。

#### 缺点：

-   **语法复杂**：相比 `INCLUDE_DIRECTORIES`，`target_include_directories` 的语法和概念相对复杂，特别是对于新手来说，可能需要更多的学习时间。
-   **初期搭建繁琐**：在项目早期搭建阶段，可能会觉得使用 `target_include_directories` 比较繁琐，因为需要为每个目标单独配置头文件路径。

___

### 4\. `INCLUDE_DIRECTORIES` 和 `target_include_directories` 的对比

| 特性 | `INCLUDE_DIRECTORIES` | `target_include_directories` |
| --- | --- | --- |
| **作用范围** | 全局 | 仅影响指定目标 |
| **细粒度控制** | 无 | 可以使用 `PRIVATE`、`PUBLIC`、`INTERFACE` |
| **适用项目** | 小型项目，简单头文件依赖 | 大型项目，复杂模块化开发 |
| **维护成本** | 随着项目增大，维护难度增加 | 清晰的依赖管理，维护性较好 |
| **推荐使用场景** | 简单的构建流程 | 精细的目标依赖管理 |

___

### 5\. 最佳实践

1.  **优先使用 `target_include_directories`**：在现代 CMake 项目中，尤其是模块化和大型项目，尽量使用 `target_include_directories` 来精确控制头文件的包含目录。它能提供更清晰的依赖关系管理，减少不必要的全局污染。
    
2.  **分清 `PUBLIC`、`PRIVATE` 和 `INTERFACE`**：合理使用这三者来管理库的头文件依赖。`PUBLIC` 适合对外暴露的 API 头文件，`PRIVATE` 用于内部实现细节，`INTERFACE` 则适合不直接使用但会传递依赖的情况。
    
3.  **减少全局依赖**：避免滥用 `INCLUDE_DIRECTORIES`，尤其是在大型项目中，全局的头文件路径可能会引发意想不到的依赖和冲突问题。
    
4.  **模块化设计**：当项目逐渐复杂化，考虑将项目划分为多个独立模块，每个模块只依赖于自己需要的头文件，使用 `target_include_directories` 来管理这些模块的头文件路径。
    

___

### 结论

在 CMake 中，`INCLUDE_DIRECTORIES` 和 `target_include_directories` 都是管理头文件路径的重要工具，但它们适用于不同的场景。`INCLUDE_DIRECTORIES` 简单直观，但在大型项目中会变得难以维护。相反，`target_include_directories` 提供了更强大的控制能力，能够为每个目标精确地管理头文件路径。

在现代 CMake 项目中，**推荐使用 `target_include_directories`**，因为它能够更好地管理目标之间的依赖，并且有助于保持项目的清晰性和可维护性。
