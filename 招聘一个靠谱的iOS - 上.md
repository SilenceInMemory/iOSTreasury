## 1. 风格纠错

![image-20230822202506499](/Users/bytedance/Library/Application Support/typora-user-images/image-20230822202506499.png)

```objective-c
typedef NS_ENUM(NSInteger, ZJCGender) {
	ZJCGenderUndefined,
    ZJCGenderMale,
    ZJCGenderFemale
}

@interface ZJCUserModel : NSObject

@property (nonatomic, readonly, copy) NSString *name;
@property (nonatomic, readonly, assign) NSUInteger age;
@property (nonatomic, readonly, assign) ZJCGender gender;

- (instancetype)initWithName:(NSString *)name age:(NSUInteger)age gender:(ZJCGender)gender;
- (instancetype)initWithName:(NSString *)name age:(NSUInteger)age;

@end
```



## 2. weak vs assign

`weak` 只能修饰对象；弱引用，“非拥有关系”；所指向对象被销毁时，系统会自动置nil；常用于delegate、循环引用场景

`assign`既可以修饰基本数据类型，也可修饰对象；基本数据类型存储在栈上，由系统分配和释放；对象存储在堆上，对象释放后，指针的地址依然存在且未被置为nil，造成野指针，继续用该指针访问和操作那块已经被释放的内存会导致EXC_BAD_ACCESS

runtime维护了一个weak表，用于存储某个对象的所有weak指针，key是某个对象的地址，value是指向该对象的所有weak 指针的地址所组成的数组



## 3. copy

- NSString NSArray NSDictionary由于有对应的可变类型而使用
  - 若不使用copy进行拷贝，可能存在赋值给NSString的是一个NSMutableString实例，从而导致NSString可能在不知情的情况下值被更改
  - copy修饰NSMutableArray可能会造成崩溃，copy后本质为NSArray，当使用数组的增删改等操作时会崩溃
- block  https://juejin.cn/post/6844903893176958983
  -  `__NSGlobalBlock__`没有访问局部变量(非static)，存储在data区
  -  `__NSStackBlock__`访问了局部变量(非static)，不会进行强持有，存储在栈
  -  `__NSMallocBlock__` 由`__NSStackBlock__` copy而来，会根据`weak strong`进行强/弱持有 ，存储在堆
  - ARC下编译器会自动将栈上的block进行copy
  - 在block内修改变量的方式：1. 直接修改全局变量；2. 直接修改static变量，因为static变量在block中是一个指针；3. 上述两种手段我们都无法及时释放变量，`__block`修饰，`__block`会将变量包装成一个对象，通过一个指针访问到变量所在内存





