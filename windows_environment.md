
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




