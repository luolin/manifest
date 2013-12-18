1. 介绍
====
Coron是一个致力于开源ROM制作的项目，开源了制作百度云ROM的所有工具和部分示例机型。采用Apache License 2.0协议，为乐于分享的开发者提供最大的自由度。

开源项目的访问网址是https://github.com/baidurom
，创立纪元是二零一三年八月八日。Coron，意味着与开发者合作而生的ROM，CO-operation ROm；也意味着百度云ROM强大的云服务能力，ROm Over Cloud；同时，coron也是一个清新纯净的小岛。

开源项目旨在让更多的开发者体验百度云ROM的制作过程，感受其间简洁、细节的情怀。


2. 分支策略
===
开源项目的分支命名基于coron，对于单卡机型，后缀为Android 版本，已有单卡分支有coron-4.0, coron-4.1, coron-4.2；对于双卡机型，后缀为双卡平台与Andorid版本的结合，已有的双卡分支有coron-mtk-4.0, coron-mtk-4.2。这些分支对应到可以制作的ROM版本，
譬如，厂商原来的系统是Android 4.2的单卡版本，那么，就推荐使用coron-4.2分支来移植百度云ROM。

开源项目的Git 库主要涉及到6 个部分：

    1) manifest.git：开源项目的Repo 管理清单文件，以及教程和文档。
    2) build.git：编译脚本，包括基于Makfile 编译环境的构建脚本。
    3) tools.git：工具，包括反编译/编译，解包/打包的脚本，以及其他一些实用工具。
    4) overlay.git：资源覆盖，包括Baidu 对原生Android 资源文件的修改。
    5) reference.git : 参考代码，包括aosp, bosp的反编译代码，以及自动Patch 的补丁文件。
    6) 示例机型的Git 库：已有devices-u930.git, devices-lt26i.git, devices-onex.git 这些机型移植的修改案例。


已有的分支和Git库的关系如下所示。

                +-------+   +-------+   +----------+   +---------+   +-----------+  +----------+   +----------+
    coron-4.0   | build |   | tools |   | manifest |   | overlay |   | reference |  | devices- |   | devices- |
                +-------+   +-------+   +----------+   +---------+   +-----------+  +--lt26i---+   +---onex---+
                    ||          ||
                +-------+   +-------+   +----------+   +---------+   +-----------+  +----------+
    coron-4.1   | build |   | tools |   | manifest |   | overlay |   | reference |  | devices- |
                +-------+   +-------+   +----------+   +---------+   +-----------+  +---u930---+
                    ||                                            
                +-------+   +-------+   +----------+   +---------+   +-----------+  +----------+
    coron-4.2   | build |   | tools |   | manifest |   | overlay |   | reference |  | devices- |
                +-------+   +-------+   +----------+   +---------+   +-----------+  +--p6_t00--+
    
每一个分支都包含一定数量的Git库，譬如coron-4.1分支就包含manifest, overlay, build, tools, devices-u930这些Git库；

每一个Git库都可能处于不同的分支，譬如manifest, overlay这些Git库就处于coron-4.0, coron-4.1, coron-4.2等分支；对一个分支上的修改，不影响其他分支的Git库。

不同的分支也可以复用同一个Git库。譬如build, tools这些Git库就被coron-4.1, coron-4.2等分支复用。对一个分支上的修改，会影响到其他分支的Git库。


3. 代码下载
====

通过repo init命令的-b参数，选择需要下载的分支，譬如coron-4.0。
通过repo sync命令同步远程代码。

    repo init -u https://github.com/baidurom/manifest.git -b coron-4.0
    repo sync

如果下载时，出现以下错误，多试几次即可(一般不超过10次，防火墙导致，难以避免)

    fatal: Cannot get https://gerrit.googlesource.com/git-repo/clone.bundle
    fatal: error [Errno 101] Network is unreachable


4. 代码提交
===
代码提交有2种方式：

<b>1) 直接更新Git库</b>

对于具备开源项目管理权限的开发者，可以直接通过git push命令，提交代码改动；

    git push –u origin coron-4.0

在修改后的Git库使用上述命令。origin是远程仓库的别名，是开发者自定义的，也可以为其他别名；coron-4.0是改动的Git库所在的分支。

<b>2) 通过Code Review方式提交代码</b>

对于具备GitHub账户的开发者，可以利用GitHub提供的Pull Request方式，将代码改动以Code Review的形式，发送给开源项目的管理者。待Code Review通过后，代码改动将会合并到提交分支。

为了能够提交代码，开发者需首先注册GitHub账户，将baidurom的Git库Fork到自己的账户下；然后，对Git库进行代码修改，发送Pull Request。最后，在开源项目的管理者收到提交请求时，会对代码进行Code Review，如果符合准入标准，就会将改动代码合并到主干分支中。


5. 百度云ROM移植
===
以Sony LT26i为例，下载完代码以后，在开源项目根目录，执行以下命令：

    source build/envsetup.sh
    mkdir -p devices/lt26i
    cd devices/lt26i

这些命令将会初始化环境变量后，创建了lt26i的机型目录，后续的移植基本都在机型目录完成。

将手机ROOT以后，取到可用的boot.img和recovery.img，放到机型根目录，执行以下命令：

    make newproject
    make bringup
    make

这些命令将会创建一个新的机型工程，自动执行bringup，将百度云ROM涉及到起机的改动注入厂商的代码中，然后编译整个工程，生成一个卡刷包。

如果顺利，那么不会产生编译错误，卡刷包也能刷入手机正常使用，这时，可以执行后续命令，将起机后的其他百度云ROM的改动自动注入到厂商的代码中。

    make patchall
    make

具体机型一般有特定的问题，等待开发者去解决，以下文档可以帮助开发者解决一些实际问题：

《Developer-Guide.pdf》，《Details-to-Smali-Development.pdf》


6. 版本升级
===
对于已有的机型，可以自动化进行版本升级．按照如下步骤:

    1) 更新可用的升级补丁，升级补丁以XML的形式存放在referece/upgrade/目录中。

    2) 在机型的Makefile文件中配置两个参数: 
       ROM_VERSION 当前的所移植机型的ROM 版本; 
       UPGRADE_VERSION 需要升级到的ROM 版本．如果没有指定则默认升级到可用的最新版

    3) make upgrade。自动将补丁的改动注入的厂商代码中．

