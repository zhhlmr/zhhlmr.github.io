---
title: iOS - 一些现代化的Objective-C 写法
categories: [Programming, iOS]
tags: 
 - iOS
---

<!-- ![cover](https://images.unsplash.com/photo-1484557052118-f32bd25b45b5?ixlib=rb-1.2.1&auto=format&fit=crop&w=1950&q=80) -->

<!-- more -->

## 构造方法中，返回值应使用instancetype，而不是id

首先，我们来看看以下代码：

```objc
@interface MyObject : NSObject
+ (instancetype)factoryMethodA;
+ (id)factoryMethodB;
@end
 
@implementation MyObject
//使用instancetype
+ (instancetype)factoryMethodA { return [[[self class] alloc] init]; }
//使用id
+ (id)factoryMethodB { return [[[self class] alloc] init]; }
@end
 
void doSomething() {
    NSUInteger x, y;
    x = [[MyObject factoryMethodA] count]; // Return type of +factoryMethodA is taken to be "MyObject *"
    y = [[MyObject factoryMethodB] count]; // Return type of +factoryMethodB is "id"
}
```

###### id与instancetype区别：

- id :因返回值有可能是任何类型的对象，此时编译器不会提示该对象没有count方法。
- instancetype : 返回MyObject类型的信息，**此时翻译器会检查该类中有没有count方法**。这样可以提高安全性。


--- 

## 多使用@property


###### 使用@property 有以下几个好处:

- 自动生成Getter/Setter方法
- 可以更好的理解对变量的声明使用方式和位置。
- 可以添加关键词（keyword）来约束/限制变量的行为/生成方式。


###### 使用@property 需遵从的规则:

- 使用readwrite 关键词修饰的property，会产生Getter/Setter方法。Setter方法有一个传入的参数并且没有返回值，Getter方法没有传参并仅返回一个值。（通常也是默认的readwrite）
- 使用readonly 关键词修饰的property，只有一个Getter方法生成。这个Getter方法同样也是没有传参并仅返回一个值。
- **任何情况下，一个Getter方法应该是 幂等的（idempotent）**，意思就是说，如果这个Getter方法调用了两次，两次的返回值应该是一模一样的。不过呢，也是允许每次调用这个Getter方法时，这个值是重新计算的。（这不是废话吗....）


> 注意：  
> 当你要决定怎样使用@property的时候，以下是不可以使用的情况:

> 1. init方法 （init method）
> 2. copy / mutableCopy 方法 （copy method, mutableCopy method）
> 3. 类的构造方法 （A class factory method）
> 4. 会进行多个操作并返回BOOL的方法 （A method that initiates an action and returns a BOOL result）
> 5. 当使用Getter方法时，会在内部改变的（A method that explicitly changes internal state as a side effect of a getter）


 ---
 
## 多使用NS_ENUM或NS_OPTIONS定义枚举值


###### 使用NS_ENUM和NS_OPTIONS有以下几个好处:

- 在Xcode里可以自动生成输入内容。
- 可以明确定义枚举的类型(NSInteger/NSUInteger)以及枚举值的大小范围
- 可以更好的兼容旧版本的编译器，并让新的编译器更好的编译

**示例：**

- 如UITableViewCellStyle

```objc
typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
        UITableViewCellStyleDefault,
        UITableViewCellStyleValue1,
        UITableViewCellStyleValue2,
        UITableViewCellStyleSubtitle
};
```

- 如UIViewAutoresizing:

```objc
typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing) {
        UIViewAutoresizingNone                 = 0,//值为0
        UIViewAutoresizingFlexibleLeftMargin   = 1 << 0,//值为2的0次方
        UIViewAutoresizingFlexibleWidth        = 1 << 1,//值为2的1次方
        UIViewAutoresizingFlexibleRightMargin  = 1 << 2,//值为2的2次方
        UIViewAutoresizingFlexibleTopMargin    = 1 << 3,//值为2的3次方
        UIViewAutoresizingFlexibleHeight       = 1 << 4,
        UIViewAutoresizingFlexibleBottomMargin = 1 << 5
};
```

> 注意 ： 

> 1.NS_OPTIONS 用于位移（bitmask）的枚举值，其类型应总是NSUInteger 。
> 
> （位移枚举使用的场景：存在需要多个枚举值的时候）
> 
> 2.NS_ENUM 用于通用的枚举值，其类型应总是NSInteger
> 
> （通用的枚举值是不可以多个枚举值共存的，只能使用一项）
> 
> 3.枚举值一般是4个字节的int值，在64位的情况下是8个字节


 ---
 
 
## 在init时使用	NS_DESIGNATED_INITIALIZER


Designated Initializer (指定初始化器),可以如Swift 一般指定构造器和便捷构造器，并且方便编译器检查代码正确性。

###### 使用中的原则

- 一个类的正确初始化方法应该是从子类到父类的顺序，依次调用类的Designated Initializer。从父类初始化一个子类对象也是遵从这一原则。
- 如果在子类中，指定了一个新的构造器，这个构造器必须调用父类的Designated Initializer，并且需要重写这个Designated Initializer，并将其指向子类新的构造器。
- 如果有多个Secondary initializers(次要初始化器)，它们之间可以任意调用，但最后必须指向Designated Initializer。在Secondary initializers内不能直接调用父类的初始化器

示例：

```objc

#import "TestInitView.h"


@implementation TestInitView

//super override
- (id)initWithFrame:(CGRect)frame
{
    return [self initWithFrame:frame andName:@""];
}

//Designated Initializer
- (instancetype)initWithFrame:(CGRect)frame andName:(NSString *)name{
    if (self = [super initWithFrame:frame]){
        self.name=name;
    }
    return self;
}

//Instance secondary initializer
- (instancetype)initWithName:(NSString *)name{
    return [self initWithFrame:CGRectZero andName:name];
}

//Instance secondary initializer
- (instancetype)initWithName2:(NSString *)name{
    return [self initWithName:name];
}

//Class secondary initializer
+ (instancetype)testInitViewWithName:(NSString *)name{
    TestInitView *testView=[[TestInitView alloc] initWithFrame:CGRectZero andName:name];
    if (testView){
        //do something here
    }
    return testView;
}

@end

```


---


## Nullability

在Swift中，可以使用语法糖（！和？）来表示一个对象是否Optional,如view！和view？。
而在Objective-C中是没有这样的区分的。这样的情况下，当Swift和Objective-C混编的时候，编译器就无法判断对象是否Optional。

为了解决这一问题，Xcode 6.3引进了新的特性 ： Nullability Annotations。

> Nullability 在编译器层面提供了空值的类型检查，在类型不符时给出 warning，方便开发者第一时间发现潜在问题



其中，有两个新的类型注释：

- __nullable (对象可否为NULL或nil)
- __nonnull（对象不可为空）


当用于修饰一个变量的时候，可以这样用：

```objc
@property (nonatomic, strong, nonnull) Sark *sark;
@property (nonatomic, copy, readonly, nullable) NSArray *friends;
```

当用于修饰一个方法时，可以这样用：

```objc
+ (nullable NSString *)friendWithName:(nonnull NSString *)name;

```

当用于修饰一个Block时，可以这样用:

```objc
- (void)startWithCompletionBlock:(nullable void (^)(NSError * __nullable error))block;

```

除此之外，还有一个null_resettable。
举例说明：

```objc
@property (null_resettable, nonatomic, strong) UIView *view;
```

加了null_resettable后，可以setView nil。然而调用getter的时候会触发-(void)loadView从而返回一个非空的view。


###### 默认加nonnull修饰符的宏 （ Audited Regions ）

示例:

```objc
NS_ASSUME_NONNULL_BEGIN
@interface Sark : NSObject
@property (nonatomic, copy, nullable) NSString *workingCompany;
@property (nonatomic, copy) NSArray *friends;
- (nullable NSString *)gayFriend;
@end
NS_ASSUME_NONNULL_END

```

上述代码中，包在宏里的对象默认加nonnull修饰符，这样子，只需要把nullable的指出来就可以了。




## 轻量级泛型(Lightweight Generics)

这里的轻量，是因为这个语法是纯编译器的支持，和Nullability 一样，没有在runtime层面上升级，也就是说这个特性是向下兼容的。

示例:

```objc
NSArray<NSString *> *strings = @[@"sun", @"yuan"];
NSDictionary<NSString *, NSNumber *> *mapping = @{@"a": @1, @"b": @2};

```

这样可以指定容器中的类型后，有两个好处：

- 自动提示代码时可以自动找到该类型可选的方法或函数。
- 当代码中加入错误的类型时，编译器会有提示。


###### 引申 - 自定义泛型类

示例：

```objc
@interface Stack<ObjectType> : NSObject
- (void)pushObject:(ObjectType)object;
- (ObjectType)popObject;
@property (nonatomic, readonly) NSArray<ObjectType> *allObjects;
@end

```

上述代码中，ObjectType是传入类型的placeholder， 只能在interface上定义。这样的类型在interface到end区间内有效，可以用于作为参数传入传出，也可以作为NSArray的泛型类型。

在此基础上，也可以增加类型限制，例如:

```objc
// 只接受 NSNumber * 的泛型
@interface Stack<ObjectType: NSNumber *> : NSObject
// 只接受满足 NSCopying 协议的泛型
@interface Stack<ObjectType: id<NSCopying>> : NSObject

```

###### 引申 - 协变性和逆变性

以下代码中， 虽然都是Stack，但其实是三个不同的Type:

```objc
Stack *stack; // Stack *
Stack<NSString *> *stringStack; // Stack<NSString *>
Stack<NSMutableString *> *mutableStringStack; // Stack<NSMutableString *>

```

而当其中的任意一个Type的对象要进行类型转化时	，不指定泛型类型的对象可以和任何类型转化，但**指定了泛型类型之后，两个类型间不可强转。** 这时候，就需要用到以下两个修饰符:

- __covariant : 协变性，子类型可以强转到父类型（里氏替换原则）
- __contravariant - 逆变性，父类型可以强转到子类型

使用方法：

```objc
//协变
@interface Stack<__covariant ObjectType> : NSObject
//逆变
@interface Stack<__contravariant ObjectType> : NSObject
```



---


## __kindof 修饰符

```objc
//原来
- (id)dequeueReusableCellWithIdentifier:(NSString *)identifier;
//在UITableView中的使用
- (__kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier;

//变量中的使用
@property (nonatomic, readonly, copy) NSArray<__kindof UIView *> *subviews;

```

增加了__kindof 修饰符后，编译器会知道该方法返回的是什么类型的对象，这样就不用在取值时强转类型了。

```objc
//未用__kindof
UIButton *button = (UIButton*) view.subviews.lastObject;

//使用了__kindof
//编译器不会提示

UIButton *button = view.subviews.lastObject;
```

---

### 参考  

1. [Next
Adopting Modern Objective-C](https://developer.apple.com/library/content/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html)


2. [正确编写Designated Initializer的几个原则](http://www.starfelix.com/blog/2014/04/13/zheng-que-bian-xie-designated-initializerde-ji-ge-yuan-ze/)

3. [2015 Objective-C 新特性](http://blog.sunnyxx.com/2015/06/12/objc-new-features-in-2015/)
4. [Nullability and Objective-C](https://developer.apple.com/swift/blog/?id=25)


---