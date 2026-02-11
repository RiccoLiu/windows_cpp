
# Mobaxterm 配置

## SSH Key

```
ssh-keygen -t rsa -C "liuchong12233@163.com"
ssh key: /home/Administrator/.ssh/id_rsa.pub
```

## Git 配置

```
git config --global user.email "liuchong12233@163.com"
git config --global user.name "liuchong"

git config --global core.editor vim
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --global color.ui auto
```

## SVN

查看版本信息
    svn --version

查看文件状态	
    svn status | findstr "^M"

# Visual Studio

## 基础配置

### 配置启动项（标题栏 -> 项目）

单个解决方案下面挂多个项目，每个项目可能是exe应用程序，也可能是Lib。

启动项目可以配置启动单个项目或多个项目，以及项目的依赖项。

### 配置管理器（标题栏 -> 项目）

配置各个项目在不同平台不同配置的编译选项

### 项目配置

新建项目
```
应用程序类型: 单文档
项目类型: MFC标准
MFC使用: 在共享 DLL 中使用MFC

命令行：使用经典菜单
```

预处理指令

```
C/C++ 传统函数scanf,strcpy,sprintf在 MSVC 平台会报C4996 错误，建议使用 _s 结尾的函数，当使用_s结尾的函数时需要加入预处理指令: _CRT_SECURE_NO_WARNINGS

项目
    -> 属性
        -> c/c++
            -> 预处理器
                -> 预处理器定义： _CRT_SECURE_NO_WARNINGS

```

常用属性
```
->属性
    -> 配置属性
        -> 常规
            - 输出目录： 输出exe所在目录
            - 目标文件名： 输出exe文件名
            - c++语言标准: 编译c++文件标准
            - c语言标准: 编译c文件标准
        
        -> 调试
            - 命令参数: 程序运行时命令行参数
            - 工作目录: 程序运行在哪个目录
            
        -> vc++目录
            - 包含目录： 依赖的第三方头文件路径
            - 外部包含目录： 依赖的系统级头文件 或者 只读的第三方头文件路径
            - 库目录： 依赖的第三方库文件路径 (不建议在这里配置)

        -> C/C++
            - 预编译头

    -> 链接器
        -> 常规
            -> 附加到库目录： 依赖的第三方库的文件路径 
        
        -> 输入
            -> 附加依赖项： 依赖第三方库的具体名称
```

## 新建一个Common项目

## VCPKG

```
# 将vcpkg安装的软件包
vcpkg integrate install

# 列出安装的所有软件
vcpkg list

# 安装软件
vcpkg install eigen3:x64-windows
vcpkg install spdlog:x64-windows

# 卸载软件
vcpkg remove spdlog
```

## MSVC 使用 AddressSanitizer 工具

- 开启 AddressSanitizer  
```
-> c/c++
    -> 常规
        -> 启动地址擦除系统： 
            否 -> 是 (/fsanitize=address)
        -> 调试信息格式
            用于“编辑并继续”的程序数据库 (/ZI) -> 程序数据库 (/Zi)

    ->优化
        -> 已禁用(/Od)

-> 链接器
    -> 调试
        -> 生成调试信息
            生成经过优化以共享和发布的调试信息 (/DEBUG:FULL) -> 生成调试信息 (/DEBUG)
```

- 手动开启一个控制台接收 ASan 报告
```
#include <windows.h>
#include <iostream>

void EnableConsole()
{
    AllocConsole();
    FILE* pCout;
    freopen_s(&pCout, "CONOUT$", "w", stdout);
    freopen_s(&pCout, "CONOUT$", "w", stderr);
    freopen_s(&pCout, "CONIN$", "r", stdin);
    std::ios::sync_with_stdio();
    std::cout.clear();
    std::cerr.clear();
    std::cin.clear();
}
```

- 运行：开始执行(不调试)


## 项目结构

```
+--- include                // 头文件
|   +--- app
|   |   +--- DFScan.h
|   +--- ui
|   |   +--- DFScanDlg.h
+--- src                    // 源文件
|   +--- app
|   |   +--- DFScan.cpp
|   +--- common
|   +--- core
|   +--- services
|   +--- ui
|   |   +--- DFScanDlg.cpp
+--- test                   // 测试文件
+--- thirdparty             // 第三方库
+--- res                    // 资源文件
|   +--- bmp00001.bmp
+--- framework.h            // 框架头文件
+--- pch.cpp
+--- pch.h
+--- resource.h
+--- targetver.h

```

## windows 环境下制作动态库

Common.h 导出的示例代码如下所示：

```
//  编译器预定义：DLL_EXPORTS
#include <cstdint>

#ifdef _WIN32
#ifdef DLL_EXPORTS
#define _API_ __declspec(dllexport)
#else
#define _API_ __declspec(dllimport)
#endif
#else
#define _API_ 
#endif

#ifdef __cplusplus  // 如果此工程使用c++编译器，这个函数会按照C函数去导出
extern "C" {
#endif

// 跨模块传递的对象使用 POD (Plain Old Data: 清楚旧数据) 或者 句柄 (void*)
// 常见的POD类型: int, float, double, bool, char*, struct等

_API_ void WriteImgToFile(const char* filename, std::uint16_t* img_buffer, int img_width, int img_height);

#ifdef __cplusplus
}
#endif
```

总结:  
1. window CRT 统一使用 /MD 或者 /MT (推荐使用/MD)
2. MSVC 不承诺 STL ABI 稳定，跨模块传递的对象不能使用STL对象，只能是POD或者句柄(void*), 使用STL的场景需要使用PIMPL结构封装起来


```
// 1. C 风格的句柄 + POD类型的

// .h 定义对外 C 类型 句柄 + POD 的接口
typedef void* SEGMENT_HANDLE;

#ifdef __cplusplus  // 如果此工程使用c++编译器，这个函数会按照C函数去导出
extern "C" {
#endif

typedef void* SEGMENT_HANDLE; // 也可以使用结构体 typedef struct SegmentHandleTag* SEGMENT_HANDLE;

// C 风格 API
SEGMENT_HANDLE create_handle();
void do_something(SEGMENT_HANDLE h);
void destroy_handle(SEGMENT_HANDLE h);


#ifdef __cplusplus
}
#endif

// .cpp 内部实现, class SegmentRegionImpl 转换成void*, 如果是 struct SegmentHandleTag* SEGMENT_HANDLE; cpp中要实现 SegmentHandleTag 这个结构
class SegmentRegionImpl {
public:
    SegmentRegionImpl() {}
    virtual ~SegmentRegionImpl() {}
    void do_something() {}
};

SEGMENT_HANDLE create_handle(...) {
    return new SegmentRegionImpl();
}

void detroy_handle(SEGMENT_HANDLE h) {
    delete static_cast<SegmentRegionImpl*>(h);
}

void do_something(SEGMENT_HANDLE h) {
    SegmentRegionImpl* pimpl = static_cast<SegmentRegionImpl*>(h);
    pimpl->do_something();
}

// 2. PIMPL 结构封装导外部直接构造 SegmentRegion，头文件会有 Impl 的信息
class _API_ SegmentRegion {
public:
    SegmentRegion();
    ~SegmentRegion();

    int do_something() const;

private:
    struct Impl;  // 所有的STL结构都封装到Impl结构里，对外的头文件中不暴露
    Impl* impl_;  
};

// 3. 对外SDK 更建议使用 接口 + 工厂 这种可以保证ABI更稳定
// .h 设定接口 
class SegmentRegion {
public:
    ~SegmentRegion();

    virtual int do_something() = 0;
    virtual rvoid release() = 0;
};

_API_ SegmentRegion* CreateSegmentRegion();

// .cpp 实现
class SegmentRegionImpl : public SegmentRegion {
public:
    SegmentRegionImpl() {}
    ~SegmentRegionImpl() {}

    virtual int do_something() overide {
    }
    
    virtual void release() overide {
        delete this;
    }
}

SegmentRegion* CreateSegmentRegion() {
    return new SegmentRegionImpl();
}

// 外部使用
SegmentRegion* sr = CreateSegmentRegion();
sr->do_something();
sr->release();


```
3. 静态库是直接打包进exe的，如果MSVC版本一致、CRT一致、STL一致、Debug\Release版本一致，静态库是可以暴露STL的接口
4. 静态库链接到多个DLL或EXE时，会导致一个进程出现多个实例，如果希望全进程唯一实例考虑使用 动态库 +  __declspec(dllexport) 的方式

Q1. 为什么 Linux 下 STL 跨库没有问题？

| 系统环境 | Linux | Windows |
|---------|-------| -------|
| 编译器 | gcc | msvc |
| 标准库 | libstd++(ABI兼容) | MSVC STL(ABI不兼容) |
| 运行库 | glibc | 多种 CRT |
| 系统层 | Linue Kernel | Windows Kernel |

1. 运行时全进程只有一个 glibc, malloc/free/pthread 等底层函数是全局唯一的
2. 各个动态库和应用程序之间共享同一个 libstdc++.so 使STL 分配器是相同的
3. ABI 相对window 更稳定

## 进程间通信



