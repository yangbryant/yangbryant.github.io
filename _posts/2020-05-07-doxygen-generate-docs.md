---
layout: post
title: 使用Doxygen创建C/C++文档
author: Srefan
date: 2020-05-07 10:09:00 +08:00
catalog: true
tags:
    - 教程
    - C
    - 规范
---

***

### 一.概述

* 写代码很重要的一部分就是输出文档, 充分利用工具可提高开发效率. 对于C/C++的开发, 使用 **Doxygen** 开源跨平台的注释文档生成工具来生成文档是个不错的选择.

### 二.流程

#### 1.安装

```shell
git clone https://github.com/doxygen/doxygen.git # 下载源码
cd doxygen
mkdir build
cd build
cmake -G "Unix Makefiles" ../ # 使用CMake编译
make
sudo make install
```

#### 2.使用

```shell
cd project-dir #进入项目目录
doxygen -g Doxyfile # 生成配置文件, 默认名为Doxyfile
doxygen # 生成文档文件
```

#### 3.配置文件模板

```
# 项目名称，将作为于所生成的程序文档首页标题
PROJECT_NAME        = “Test”
# 文档版本号，可对应于项目版本号，譬如 svn、cvs 所生成的项目版本号
PROJECT_NUMBER      = "1.0.0
# 程序文档输出目录
OUTPUT_DIRECTORY    =  /home/user1/docs
 
# 程序文档输入目录 
INPUT                = /home/user1/project/kernel
 
# 程序文档语言环境
OUTPUT_LANGUAGE      = Chinese
DOXYFILE_ENCODING  = UTF-8
# 只对头文件中的文档化信息生成程序文档 
FILE_PATTERNS        = 
 
# 递归遍历当前目录的子目录，寻找被文档化的程序源文件 
RECURSIVE            = YES 
# 如果是制作 C 程序文档，该选项必须设为 YES，否则默认生成 C++ 文档格式
OPTIMIZE_OUTPUT_FOR_C  = YES
 
#提取信息，包含类的私有数据成员和静态成员
EXTRACT_ALL            = yes
EXTRACT_PRIVATE        = yes
EXTRACT_STATIC        = yes
# 对于使用 typedef 定义的结构体、枚举、联合等数据类型，只按照 typedef 定义的类型名进行文档化
TYPEDEF_HIDES_STRUCT  = YES
# 在 C++ 程序文档中，该值可以设置为 NO，而在 C 程序文档中，由于 C 语言没有所谓的域/名字空间这样的概念，所以此处设置为 YES
HIDE_SCOPE_NAMES      = YES
# 让 doxygen 静悄悄地为你生成文档，只有出现警告或错误时，才在终端输出提示信息
QUIET  = YES
# 递归遍历示例程序目录的子目录，寻找被文档化的程序源文件
EXAMPLE_RECURSIVE      = YES
# 允许程序文档中显示本文档化的函数相互调用关系
REFERENCED_BY_RELATION = YES
REFERENCES_RELATION    = YES
REFERENCES_LINK_SOURCE = YES
# 不生成 latex 格式的程序文档
GENERATE_LATEX        = NO
# 在程序文档中允许以图例形式显示函数调用关系，前提是你已经安装了 graphviz 软件包
HAVE_DOT              = YES
CALL_GRAPH            = YES
CALLER_GRAPH          = YES
#在最后生成的文档中，把所有的源代码包含在其中
SOURCE BROWSER        = YES
$这会在HTML文档中，添加一个侧边栏，并以树状结构显示包、类、接口等的关系
GENERATE TREEVIEW      ＝ ALL
```

### 三.语法

#### 1.行注释

```
///< ...
或者
/** ... */
```

#### 2.块注释

```
/**
 *
 */
```

#### 3.文件注释

```
/**
 * @file 文件名
 * @brief 简介
 * @details 细节
 * @mainpage 工程概览
 * @author 作者
 * @version 版本号
 * @date 年-月-日
 */
```

#### 4.全局常量/变量/宏定义/结构体定义/类定义的注释

```
/// 注释
全局常量/变量/宏定义/结构体定义/类定义

例如:
/// 缓存大小
#define BUFSIZ 1024*4

或者
全局常量/变量/宏定义/结构体定义/类定义 ///< 注释

例如:
#define BUFSIZ 1024*4 ///< 缓存大小
```

#### 5.函数注释

```
/**
 * @brief 函数简介
 *
 * @param 形参 参数说明
 * @param 形参 参数说明
 * @return 返回值说明
*/

例如:
/**
 * @brief 主函数
 * @details 程序唯一入口
 *
 * @param argc 命令参数个数
 * @param argv 命令参数指针数组
 * @return @c 0 程序执行成功
 *         @c 1 程序执行失败
*/
```

| 命令 | 生成字段名 |
| :---- | :----: |
| @param | 参数 |
| @return | 返回值 |
| @p | 参数 |
| @c | 代码 |

#### 6.其他常用命令


| 命令 | 生成字段名 | 说明 |
| :---- | :----: | :---- |
| @attention | 注意 | |
| @bug | 缺陷 | 链接到所有缺陷汇总的缺陷列表 |
| @warning | 警告 | |
| @see | 参考 | |
| @code | 代码块开始 | 与@endcode成对使用 |
| @endcode | 代码块结束 | 与@code成对使用 |
| @todo | TODO | 链接到所有TODO 汇总的TODO 列表 |

#### 7.项目注释

* 项目注释块用于对项目进行描述，每个项目只出现一次，一般可以放在main.c主函数文件头部。对于其它类型的项目，置于定义项目入口函数的文件中。对于无入口函数的项目，比如静态库项目，置于较关键且不会被外部项目引用的文件中。

* 项目注释块以“/** @mainpage”开头，以“*/”结束。包含项目描述、及功能描述、用法描述、注意事项4个描述章节。

* 项目描述章节描述项目名称、作者、代码库目录、项目详细描述4项内容，建议采用HTML的表格语法进行对齐描述。

* 功能描述章节列举该项目的主要功能。

* 用法描述章节列举该项目的主要使用方法，主要针对动态库、静态库等会被其它项目使用的项目。对于其它类型的项目，该章节可省略。

* 注意事项章节描述该项目的注意事项、依赖项目等相关信息。

```
/**@mainpage  智能井盖固件程序
* <table>
* <tr><th>Project  <td>ble_app_smc 
* <tr><th>Author   <td>wanghuan 
* <tr><th>Source   <td>E:\keil_workspace\NORDIC\nRF52832_htwh_sdk15.0\examples\ble_peripheral\ble_app_smc_freertos-doxygen
* </table>
* @section   项目详细描述
* 通过智能井盖管理系统的部署，管理人员通过手机APP与管理平台就能对辖区内井盖的安装、开闭、状态进行管理，出现异常情况及时通知维护人员进行检修，保障排水正常，保障市民安全。
*
* @section   功能描述  
* -# 本工程基于蓝牙新品nRF52832开发
* -# 本工程基于蓝牙协议栈开发，协议栈版本 SDK-15.0
* -# 智能井盖采用NB-IoT模组为ME3616
* 
* @section   用法描述 
* -# 智能井盖检测器安装指导
* -# 智能井盖检测器使用前需配置使能
* 
* @section   固件更新 
* <table>
* <tr><th>Date        <th>H_Version  <th>S_Version  <th>Author    <th>Description  </tr>
* <tr><td>2018/08/17  <td>1.0    <td>S02010041808171   <td>wanghuan  <td>创建初始版本 </tr>
* <tr><td>2019/06/24  <td>1.3    <td>S02010041906241   <td>wanghuan  <td>
* -# 电信平台增加上报需应答，应答超时时间默认40s；\n
*       代码宏：ME3616_NOTIFY_NEED_RPLY_EN
* -# 新增PSM进入超时处理，默认超时处理模组关机，超时时间默认200s；\n
*       代码宏：ME3616_PSM_TIMEOUT_HANDLE_EN
* -# 信号强度获取接口函数修改，增加可靠性，详见 me3616_getSignal()；
* -# 调试指令新增周期上报测试指令，710A-0D
* </tr>
* </table>
**********************************************************************************
*/
```

#### 8.更多参考这里

[Doxygen语法][Link_3]

### 四.VS Code自动生成Doxygen格式注释

#### 1.安装 `Generate Doxygen Comments`插件

#### 2.配置

```json
# setting.json
{
    "doxdocgen.file.copyrightTag": [
        "@copyright Copyright © 2014-{year} Yantai IRay Technology Co., Ltd."
    ],
    "doxdocgen.file.customTag": [
        "*********************************************************************************",
        "@par ChangeLog:",
        "<table>",
        "<tr><th>Date       <th>Version <th>Author  <th>Description",
        "<tr><td>{date} <td>1.0     <td>liyang     <td>内容",
        "</table>",
        "*********************************************************************************",
    ],
    "doxdocgen.file.fileOrder": [
        "file",		// @file
        "brief",	// @brief 简介
        "author",	// 作者
        "version",	// 版本
        "date",		// 日期
        "copyright",// 版权
        "empty",
        "custom"	// 自定义
    ],
    "doxdocgen.file.fileTemplate": "@file {name}",
    "doxdocgen.file.versionTag": "@version 1.0",
    "doxdocgen.generic.authorEmail": "yli@iraytek.com",
    "doxdocgen.generic.authorName": "liyang",
    "doxdocgen.generic.authorTag": "@author {author} ({email})",
    // 日期格式与模板
    "doxdocgen.generic.dateFormat": "YYYY-MM-DD",
    "doxdocgen.generic.dateTemplate": "@date {date}",
	
    // 根据自动生成的注释模板（目前主要体现在函数注释上）
    "doxdocgen.generic.order": [
        "brief",
        "tparam",
        "param",
        "return"
    ],
    "doxdocgen.generic.paramTemplate": "@param{indent:8}{param}{indent:25}desc",
    "doxdocgen.generic.returnTemplate": "@return {type} @c ",
    "doxdocgen.generic.splitCasingSmartText": true,
}
```
#### 3.使用

> 在文件头部输入 “/**” 后回车

```c
/**
 * @file uart.h
 * @brief 
 * @author liyang (yli@iraytek.com)
 * @version 1.0
 * @date 2020-05-07
 * @copyright Copyright © 2014-2020 Yantai IRay Technology Co., Ltd.
 * 
 * @par ChangeLog:
 * <table>
 * <tr><th>Date       <th>Version <th>Author  <th>Description
 * <tr><td>2020-05-07 <td>1.0     <td>liyang     <td>内容
 * </table>
 */
```

> 在函数上面 “/**” 后回车

```c
/**
 * @brief 
 * @param  _pClient         desc
 * @return int @c 
 */
 int Inf_DestroySerialClient(SerialClient *_pClient);
```

### 五.参考链接

此文参考于 [jdzhangxin的简书][Link_1]和[osc_ckub9v3l的OSCHINA空间][Link_2],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://www.jianshu.com/p/0d3fa90ebddf/
[Link_2]: https://my.oschina.net/u/4347039/blog/3346168
[Link_3]: https://www.cnblogs.com/silencehuan/p/11169084.html