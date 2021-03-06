---
title: OC 中给类扩充方法
date: 2015-05-02 09:01:51
categories:
- OC
tags:
- OC
---
## 写在前面的话

在之前写的[OC 中的方法“重写”小结（一）](http://kai-lee.com/2015/04/29/%E5%B0%8F%E7%BB%93OC%E4%B8%AD%E7%9A%84%E6%96%B9%E6%B3%95%E9%87%8D%E5%86%99/)与[OC 中的方法“重写”小结（二）](http://kai-lee.com/2015/04/30/%E5%B0%8F%E7%BB%93OC%E4%B8%AD%E7%9A%84%E6%96%B9%E6%B3%95%E9%87%8D%E5%86%992/)文章中介绍了重写某些方法的知识点，而且了解到在实际的开发中，为了方便，经常会自定义某些方法，也就是说会重写某些方法。文中提到了，在 OC 的**继承**中，**子类**可以**重写**其**父类**的方法，还有我们可以重写**构造方法**重写**dealloc** 方法和 **description** 方法等。那么今天我将总结一下通过**继承**和**分类**在不改变类模型的前提下，给类扩充一些方法。希望对读者有所帮助。
<!--more-->

## 类

开始之前有必要了解一下什么是类。其实类也是一个对象，是 Class 类型的对象，简称**类对象**。

    typedef struct objc_class *Class;

类名就代表着类对象，每个类只有一个类对象。

在程序启动的时候会加载所有的类和分类，并调用所有类和分类的 `+load` 方法；限价在父类，再加载子类（即先调用父类的 `+load` ，再调用子类的 `+load`方法）；先加载原始类，再加载分类;不管程序运行过程有没有用到这个类，都会调用 `+load` 加载，直到第一次使用某个类时（比如创建对象等）就会调用一次初始化方法（`+initialize`），一个类只会调用一次初始化方法，同时仍旧是父类在前，子类在后。

## 继承

### 如何通过继承扩充方法

现在可以来说一说通过继承来扩充类方法了。我们知道只要 A 类继承了 B 类，那么 A 是子类， B 是父类。 A 将继承 B 的所有属性和方法。这里我们只谈到了 A 继承 B 那么可不可以 A 再继承 C 或者其他的类呢？答案是： NO 。OC 中的继承是**单继承**。

那么，现在我们创建一些个类和对象（其中有继承关系），系统就会给类和对象开辟内存空间，其中这里我们关注的**类**存放的是方法列表，同时每个子类有个 **superclass 指针**指向它的父类。子类方法和属性的访问过程是这样的：调用某个对象的方法（或者类的方法）时，优先通过 **isa 指针**去自己的类中找方法，如果自己的类中没有该方法，就顺着superclass指针到父类中找。出于这种访问机制，我们有理由通过继承关系，利用子类方法来扩充父类的方法。

### 继承扩充类方法的利弊

- 不改变原来的模型的基础上，拓展方法
- 建立了类与类之间的关系
- 抽取了公共代码
- 但是增加了耦合性，也可以说上边的优点同时成了他的缺点，真是把双刃剑！


## 分类

### 基本使用

    // 分类的声明
    @interface 类名 (分类名称)
    // 方法声明
    @end
    
    // 分类的实现
    @implementation 类名 (分类名称)
    // 方法实现
    @end

### 分类给 NSString 类扩充方法


    #import <Foundation/Foundation.h>
    
    @interface NSString (Arab)
    // 类方法声明，计算某个字符串中阿拉伯数字的个数
    + (int)countNumOfString:(NSString *)str;
    //  对象方法声明，计算某个字符串中阿拉伯数字的个数
    - (int)countNum;
    
    @end
    
    @implementation NSString (Arab)
    // 类方法实现，计算某个字符串中阿拉伯数字的个数
    + (int)countNumOfString:(NSString *)str
    {
        
        return [str countNum];
        
    }
    //  对象方法实现，计算某个字符串中阿拉伯数字的个数
    - (int)countNum
    {
        int count = 0;
        int len = [self length];
        int i = 0;
    
        for (; i < len; i++)
        {
            unichar c = [self characterAtIndex:i];
            if (c>=48 && c<=57)
            // if (c>='0' && c<='9')
            {
                count++;
            }
            
        }
    
        return count;
        
    }
    @end
    
    int main()
    {
        int co1 = [NSString countNumOfString:@"nihao2015"];
    
        NSLog(@"The number's amount is %d", co1);
    
        int co2 = [@"jack'sbirthdayis20150101" countNum];
    
        NSLog(@"The number's amount is %d", co2);
    
        return 0;
    }


### 利弊分析与使用注意

- 分类只能增加方法，不能增加成员变量。如果想添加变量，可以考虑通过继承创建子类
- 分类的实现可以访问原来类中声明的成员变量
- 当你调用一个方法，它先会去分类里面找，分类里面找不到再找原类，原类找不到再找父类
- 分类可以重新实现原来类中的方法，但是会覆盖掉原来的方法，会导致原来的方法没法再使用，一般不建议覆盖掉原类的方法
- 分类下的方法优先级高
- 后编译的分类方法会覆盖掉先编译的类方法
- 编译只编译源文件 `.m` , `.h` 不参加编译哦！
- 分类利于分模块开发，一个庞大的类可以由多个人来编写，更利于团队合作

## 写在最后的话

希望我的博文可以给看到文章最后的您带来些许的帮助，有的地方会是浅尝辄止，有的地方可能出现了错误，所以对于文中的不足，还请加以指正，谢谢！同时，我还会不断总结学习中遇到的知识点，敬请期待！

## 参考文献

- [面向对象程序设计](http://zh.wikipedia.org/wiki/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1)
- [ OC学习那些事：Category](http://blog.csdn.net/p106786860/article/details/10170081)