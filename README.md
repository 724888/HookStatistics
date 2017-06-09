### HookStatistics

HookStatistics includes two way by using method swizzling：

1 use StatisticsManager to hook methods of different classes 

2 usually  because of referring to business logic，so we could use class category to log user actions

### Cocoapods：

excute command： pod install

### Example

First,swizzling method should happens in +(void)load
#### 1. swizzling private class method(or public class method) 

```objc
[self swizzleClassSelector:NSSelectorFromString(@"privateClassMethod") andSelector:NSSelectorFromString(@"hk_privateClassMethod")];
```
#### 2. swizzling private instance method(or public instance method) 

```objc
[self swizzleInstanceSelector:NSSelectorFromString(@"privateInstanceMethod") andSelector:NSSelectorFromString(@"hk_privateInstanceMethod")];
```
#### 3. swizzling action method，such as UIButton

```objc
[self swizzleInstanceSelector:NSSelectorFromString(@"clickTestNormal") andSelector:NSSelectorFromString(@"hk_clickTestNormal")];
```
#### 4. swizzling protocol

```objc
[self swizzleInstanceSelector:NSSelectorFromString(@"someDelegateTriggerWithArg:") andSelector:NSSelectorFromString(@"hk_someDelegateTriggerWithArg:")];
```
#### At last，there is some specical for swizzling block

```objc
static IMP originBlockMethodImp = NULL;
...
+(void)load{
   originBlockMethodImp = [self instanceMethodIMPForSelector:NSSelectorFromString(@"clickTestBlockCompletion:")];
   [self swizzleInstanceSelector:NSSelectorFromString(@"clickTestBlockCompletion:")
       andSelector:NSSelectorFromString(@"hk_clickTestBlockCompletion:")];
}
...
-(void)hk_clickTestBlockCompletion:(ClickCompletion)block{
    
    //replacement
    ClickCompletion hookBlock = ^(NSString *text){
        
        block(text);
        
        //insert color to statistic database;
    };
    
    //method-imp
    ((void (*)(id, SEL, void *))originBlockMethodImp)(self, NSSelectorFromString(@"clickTestBlockCompletion:"),(__bridge void *)hookBlock);
}
 
