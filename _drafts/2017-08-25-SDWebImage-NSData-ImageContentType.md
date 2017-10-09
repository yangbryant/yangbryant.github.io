---
layout: post
title: SDWebImage 源码解读 之 NSData+ImageContentType
date: 2017-08-25 15:51:00 +08:00
tags: 源码解读
---

***

### NSData (ImageContentType)

```objective-c
/**
 *  Return image format
 *
 *  @param data the input image data
 *
 *  @return the image format as `SDImageFormat` (enum)
 */
+ (SDImageFormat)sd_imageFormatForImageData:(nullable NSData *)data;
```

* 根据二进制数据获取图片的 `contentType` .

* 文件头: 位于文件开头, 承担一定任务的数据. 换言之, **当文件都使用二进制流作为传输时, 需要制定一套规范, 用来区分该文件到底是什么类型**.

* 图片相关的文件头:
1. JPEG (jpg), 文件头: `0xFFD8FFE1`
2. PNG (png), 文件头: `0x89504E47`
3. GIF (gif), 文件头: `0x47494638`
4. TIFF (tif;tiff), 文件头: `0x49492A00`we
5. TIFF (tif;tiff), 文件头: `0x4D4D002A`
6. RAR Archive (rar), 文件头: `0x52617221`
7. WebP, 文件头: `0x52494646 2A730100 57454250`

> WebP这种格式很特别. 是由12个字节组成的文件头, 把这些字节通过ASCII编码后会得到下边这样一张表格:

![WebP_Image][Img_1]

```objective-c
+ (SDImageFormat)sd_imageFormatForImageData:(nullable NSData *)data {
    if (!data) {
        return SDImageFormatUndefined;
    }
    
    uint8_t c;
    [data getBytes:&c length:1];
    switch (c) {
        case 0xFF:
            return SDImageFormatJPEG;
        case 0x89:
            return SDImageFormatPNG;
        case 0x47:
            return SDImageFormatGIF;
        case 0x49:
        case 0x4D:
            return SDImageFormatTIFF;
        case 0x52:
            // R as RIFF for WEBP
            if (data.length < 12) {
                return SDImageFormatUndefined;
            }
            
            NSString *testString = [[NSString alloc] initWithData:[data subdataWithRange:NSMakeRange(0, 12)] encoding:NSASCIIStringEncoding];
            if ([testString hasPrefix:@"RIFF"] && [testString hasSuffix:@"WEBP"]) {
                return SDImageFormatWebP;
            }
    }
    return SDImageFormatUndefined;
}
```

### NSData (ImageContentTypeDeprecated)

* 有一个东西值得学习.

```objective-c
+ (NSString *)contentTypeForImageData:(NSData *)data __deprecated_msg("Use `sd_contentTypeForImageData:`");
```

* 这个 `__deprecated_msg` 可以告诉开发者该方法不建议使用. 当我们在写框架或者类的时候, 如果功能相同, 但是想使用新的方法名的时候, 使用 `__deprecated_msg` 给予其他开发者一个提示, 这远远比我们直接删除旧的更专业.


### 参考链接

此文摘抄于 [老马的春天的简书][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://www.jianshu.com/p/fd984fd8bd5d

[Img_1]: /assets/images/SDWebImage/NSData_webp.jpg