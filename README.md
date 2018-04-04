# SYLabelFontFit
UILabel的文字大小随着机型自动适配，不分纯代码和XIB/SB，使用分类加runtime实现，很简单.

```
//在UIFont的分类中实现
+ (BOOL)exchangeMethod:(SEL)systemSel with:(SEL)newSel
{
    Class class = object_getClass(self);
    Method originalMethod = class_getInstanceMethod(class, systemSel);
    Method newMethod = class_getInstanceMethod(class, newSel);
    if (!originalMethod || !newMethod) return NO;
    method_exchangeImplementations(originalMethod, newMethod);
    return YES;
}
```

```
//在UILabel的分类中实现
+ (BOOL)exchangeMethod:(SEL)systemSel with:(SEL)newSel
{
    Method originalMethod = class_getInstanceMethod(self, systemSel);
    Method newMethod = class_getInstanceMethod(self, newSel);
    if (!originalMethod || !newMethod) return NO;
    
    class_addMethod(self,
                    systemSel,
                    class_getMethodImplementation(self, systemSel),
                    method_getTypeEncoding(originalMethod));
    class_addMethod(self,
                    newSel,
                    class_getMethodImplementation(self, newSel),
                    method_getTypeEncoding(newMethod));
    
    method_exchangeImplementations(class_getInstanceMethod(self, systemSel),
                                   class_getInstanceMethod(self, newSel));
    return YES;
}
```
