---
layout: post
title: QML与C++的国际化
author: Srefan
date: 2020-03-30 11:46:00 +08:00
catalog: true
tags:
    - qt
    - 国际化 
---

***

### 一.概述

* 工作中的Qt项目需要支持多语言, 过程中遇到了很多麻烦, 记录一下.

### 二.流程

* 在项目文件 `sample.pro` 中添加如下代码.

```
TRANSLATIONS += zh_CN.ts
```

* 到终端中执行如下命令,生成 `zh_CN.ts` 文件.

```shell
lupdate sample.pro
```

* 继续在终端中执行如下命令, 将项目中有字符的 **C++源码** 和 **qml文件** 的字符取出, 添加到 **ts** 文件中.

```shell
lupdate main.cpp main.qml -ts zh_CN.ts
```

* 使用 `Qt Linguist` 打开 `zh_CN.ts` 文件, 填写翻译内容, 保存文件.

* 回到终端执行如下命令, 生成release文件 `zh_CN.qm`.

```shell
lrelease zh_CN.ts
```

* 将生成的 `zh_CN.qm` 文件添加到项目的 `main.qrc` 文件中.

* 在 `main.cpp` 中添加加载代码.

```
#include <QGuiApplication>
#include <QQmlApplicationEngine>
#include <QtCore>

int main(int argc, char *argv[])
{
    QCoreApplication::setAttribute(Qt::AA_EnableHighDpiScaling);
    QGuiApplication app(argc, argv);

    // 添加如下代码
    QTranslator translator;
    translator.load(QString(":/zh_CN"));
    app.installTranslator(&translator);
    // 添加如上代码

    QQmlApplicationEngine engine;
    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));
    if (engine.rootObjects().isEmpty())
        return -1;

    return app.exec();
}
```

* 运行, 看是否生效.

### 三.参考链接

此文参考于 **Qt Quick核心编程-安晓辉**,十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: https://letsencrypt.org/
