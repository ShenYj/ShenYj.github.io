
# 修复`xcode 11`下`OCLint`分析失败

## `homebrew`当前可用最高版本为0.13版本

- `$ oclint --version`

  ```bash
  LLVM (http://llvm.org/):
    LLVM version 5.0.0svn-r313528
    Optimized build.
    Default target: x86_64-apple-darwin19.4.0
    Host CPU: skylake

    OCLint (http://oclint.org/):
    OCLint version 0.13.
    Built Sep 18 2017 (08:58:40).
    ```

- 在这之后, 其实还有`0.13.1`、`0.14`和`0.15`版本, 之所以要升级`OCLint`版本:
[issue547](https://github.com/oclint/oclint/issues/547) ,
[upgrade llvm 9.0 to resolve xcode11 build's issue](https://github.com/oclint/oclint/pull/548)
`0.14`和`0.15`目前只能通过编译安装

## 编译安装过程简介

### 一 通过`homebrew`安装`cmake`和`Ninja`两个编译工具

`$ brew install cmake ninja`

### 二 编译安装`OCLint`

1. [Clone OCLint SourceCode](https://github.com/oclint/oclint/releases)
2. `cd` 到`oclint-scripts`路径下
3. 执行`./make`, 成功之后会出现build文件夹, `oclint-release`就是编译成功的oclint工具
4. 设置`oclint`工具的环境变量:

    - 添加环境

        ```bash
        OCLint_PATH=换成你存放的实际路径/oclint/build/oclint-release
        export PATH=$OCLint_PATH/bin:$PATH
        ```

    - 更新环境

        执行`source ~/.zshrc`  
        这里我用的是`zsh`如果你使用系统的终端则执行 `source .bash_profile`

    - 把`bin`中的全部文件复制到`/usr/local/bin/`和`/usr/local/lib`中

        ```bash
        cp 换成你存放的实际路径/oclint-0.15/build/oclint-release/bin/oclint* /usr/local/bin/
        sudo cp -rp 换成你存放的实际路径/oclint-0.15/build/oclint-release/lib/* /usr/local/lib/
        cp -rp 换成你存放的实际路径/oclint-0.15/build/oclint-release/include/* /usr/local/include/
        ```

5. 验证`OCLint`安装成功, 执行`$ oclint --version`

    ```bash
    OCLint (http://oclint.org/):
    OCLint version 0.15.
    Built Sep  2 2020 (12:16:58).
    ```

## OCLint提供的参考资料

- [OCLint Installation *](http://docs.oclint.org/en/stable/intro/installation.html)
- [Building OCLint](http://docs.oclint.org/en/stable/intro/build.html)
- [OCLint Manual](http://docs.oclint.org/en/stable/manual/oclint.html)
- [oclint-json-compilation-database Manual](http://docs.oclint.org/en/stable/manual/oclint-json-compilation-database.html)

## 失败分析

在今年4月份时首次遇到这个问题, 当时也尝试过本地编译`0.15`版本, 但当时以失败告终后暂时将问题搁置, 也在幻想着
`Ryuichi Laboratories`没准后面就直接发布新版本到`homebrew`了

再次回过来处理这个问题发现情况依旧, 对我产生迷惑的一点是重写的一个老项目是基于`xcode 11`新建的工程, 而原来的老项目是`16年`创建的, 在`OCLint 0.13.0`下, 一个没有报错, 一个没成功过, 也让我无法确认是否是`LLVM`版本过低导致. 所以这一次仍然花了些时间在`OCLint 0.13.0`版本上进行调试.

最终无奈, 再次考虑升级到`0.15`版本, 翻看了`OCLint`的`issues`和提交记录, `2019-10-22`日更新下修复了`xcode 11`的问题

`OCLint`官方的介绍资料还是很详细的, 因为时间有点久了, 自己并不是很确认第一次升级`0.15`的时候是否严格按照官方资料来复制文件到对应目录的, 所以当时没有解决这个问题

第一次应该是参考了这篇文章:[参考链接](https://juejin.im/post/6844903853775650830), 这里的后两步的`cp`我换成了`ln`, 其实我看了下`OCLint 0.13`通过`homebrew`安装后, 也应该是链接过去的, 我们的构建机`OCLint`在`usr/local/lib/`路径下可以看到`oclint`和`clang`文件夹, 通过右键显示原身,会跳转到实际路径是`/usr/local/Cellar/oclint/0.13`, 所以很有可能是这三个步骤中的`文件夹名称`, `/`和`*`我某一步的时候缺少了或是错了

## 0.15.0 资源文件分享

如果你本地没有`CMake`和`Ninja`两个编译环境, 并且安装失败或者是`CMake`编译阶段频繁失败, 可以使用我提供的编译好的`0.15.0`版本

我已经`Fork`了`OCLint`仓库, [仓库地址](https://github.com/ShenYj/oclint) 并新建了一个`feature/0.15.0`的分支, 默认`build`被设置了忽略, 为了更明显的辨认, 我将文件夹名称改为`oclint-0.15.0-build`, 名称可以任意修改, 关键是环境路径一定要设置正确.