# iOS代码规范及技巧（二）

> 作者：华文杰

1. 在对象内部尽量直接访问实例变量

   + 在初始方法及dealloc方法中，应该直接通过实例变量来读写数据
   + 在对象内部读书数据时，应该直接通过实例变量来读取，而写入数据时，则应该通过属性来写
   + 有时使用惰性初始化技术配置某份数据，这种情况下，需要通过属性来读取数据

2. 理解“对象等同性”这一概念

   + NSObject 协议中有两个用于判断等同性的关键方法：

     ```objective-c
     - (BOOL)isEqual:(id)Object;
     - (NSUInteger)hash;
     ```

     NSObject 类对这两个方法的默认实现时：当且仅当其“指针值”完全相等时，这两个对象才会相等。

     重写的话是hash值相等。 

   + 相同的对象必须具有相同的哈希码，但是两个哈希吗相同的对象未必相同。

   + 不要盲目的逐个检测每条属性，而是应该依照具体需求来定制检测方案。

   + 编写hash方法时，应该使用计算速度快而且哈希码碰撞几率低的算法。 

3. 以“族类模式”隐藏实现细节。

   “族类”是一种很有用的模式，

   ```objective-c
   typedef NS_ENUM(NSUInteger, EOCEmployeeType){
     EOCEmployeeTypeDeveloper,
     EOCEmployeeTypeDesigner,
     EOCEmployeeTypeFinance,
   }
   @interface EOCEmployee: NSObject 
   @property (copy) NSString *name;
   @property NSUInteger salary;

   //Helper for creating Employee objects
   + (EOCEmployee *)employeeWithType:(EOCEmployeeType)type;

   //Make Employees do their respective day's work
   - (void)doADaysWork;
   @end

   @implementation EOCEmployee
   + (EOCEmployee *)employeeWithType:(EOCEmployeeType)type{
     	switch(type){
         case EOCEmployeeTypeDeveloper:
         		return [EOCEmployeeDeveloper new];
         		break;
         case EOCEmployeeDesigner:
         		return [EOCEmployeedDesigner new];
         		break;
         caes EOCEmployeeFinance:
         		return [EOCEmployeeFinance new];
               break;
     	} 
   }

   - (void)doADaysWork{
    	//Subclasses implement this.
   }
   ```

   + 族类模式可以把实现细节隐藏在一套简单的公共接口后面
   + 系统框架中经常使用族类
   + 从类族的公共抽象基类中继承子类时要当心，若有开发文档，则应首先阅读

4. 在既有类中使用关联对象存放自定义数据（runtime知识点）

   + ```objective-c
     void objc_setAssociatedObject(id object, void*key, id value, objc_AssociationPolicy)
     ```

   + ```objective-c
     id objc_getAssoicatedObject(id Object,void *key);
     ```

   + ```objective-c
     void objc_removeAssociatedObejcts(id Obejct); 
     ```

     要点

     + 可以通过“关联对象”机制来把两个对象连起来
     + 定义关联对象时可指定内存管理语义，用以模仿定义属性时所采用的“拥有关系”和“非拥有关系”
     + 只有在其他做法不可行时才应选用关联对象，因为这种做法通常会引入难以查找的bug

5. 理解objc_msgSend的作用

   + id returnValue = objc_msgSend(someObject,@selector(messageName:),parameter);