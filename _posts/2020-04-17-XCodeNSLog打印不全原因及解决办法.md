---
layout: post
title: "XCode NSLog打印不全原因及解决办法"
date: 2020-04-17 
description: "NSLog打印不全解决办法"
tag: Xcode
---   
　　
　　在测试过程中,发现xcode在打印超长数据时候数据打印不完全,查找了资料,总结如下.

##原因:
        NSLog打印长的json显示不全，但是printf可以打印完全;

##解决方案一:

```        
        #define NSLog(format, ...) printf("TIME:%s FILE:%s(%d行) FUNCTION:%s \n %s\n\n",__TIME__, [[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String], __LINE__, __PRETTY_FUNCTION__, [[NSString stringWithFormat:(format), ##__VA_ARGS__] UTF8String])

```

##解决方案二:
    
    可以定义debug模式：Build Settings -> Preprocessor Macros -> Debug : DEBUG=1 ，根据DEBUG来显示log
```
    
    //定义了 Build Setting > Preprocessor Macros > Debug : DEBUG=1
    #ifdef DEBUG
    #define DeLog(format, ...) printf("\n%s JJLog %s(line%d) %s\n%s\n\n", __TIME__, [[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String], __LINE__, __PRETTY_FUNCTION__, [[NSString stringWithFormat:(format), ##__VA_ARGS__] UTF8String])
    #else
    #define DeLog(format, ...)
    #endif

```
    
    根据优化方式Optimization Leve来显示log，在工程的Prefix.pch中添加,编译运行,当选择release模式时,程序不会有打印,当选择debug时程序会正常打印；

``` 
        #ifndef __OPTIMIZE__  //优化方式 Optimization Leve
        #define NSLog(...) NSLog(__VA_ARGS__)
        #else
        #define NSLog(...){}
        #endif
    
```

新增debug调试宏:

``` 
        #ifdef DEBUG
        #define NSLog(format, ...) NSLog((@"%s " "%s(%d)\n" format "\n-----------------------------------------------------"), __PRETTY_FUNCTION__, [[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String], __LINE__, ##__VA_ARGS__);
        #else
        #define NSLog(format, ...)
        #endif
```

OC是C语言的超集，保留有c语言的一些特性。可以利用编译器预定义的宏来输出一些信息，以达到调试程序的目的。这些宏包括：

（1）__FUNCTION__   ：//获取当前方法名；

（2）__func__   ：//获取当前方法名；

（3）__PRETTY_FUNCTION__   ：//获取当前方法名；

（4）__LINE__   ：//获取当前所在行；

（5）__FILE__   ：//获取该文件的绝对路径；

（6）__DATE__   ：//获取当前日期；

（7）__TIME__   ：//获取当前时分秒；

（8）__TIMESTAMP__   ：//获取当前时间戳；
