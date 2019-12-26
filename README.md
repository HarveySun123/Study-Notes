# Study-Notes
C++学习笔记
大多来自小甲鱼的鱼C论坛
1、多态实现原理；重载、覆盖、隐藏的区别
C++类的多态通过抽象方法实现，函数多态通过重载实现。而对于区分重载、覆盖和隐藏：
1、重载是指同一类中函数重载，函数名相同参数不同；
2、覆盖指子类对基类中函数的覆盖，子与父类中函数名相同，参数相同（若参数不同则为隐藏），基类中声明时必须加virtual关键字；
3、隐藏指子类对父类的函数的隐藏，若子类与基类函数名相同，但参数不同；或者函数名相同，参数相同，但并未声明为虚方法。基类函数隐藏本质是失误，即在基类和子类中误用了不同的参数，或者在基类中漏掉了virtual关键字。基类函数隐藏会导致不可预知的错误发生。
以下进行举例说明：
#include <iostream>
using namespace std;

class Base
{
public:
    void human(int age)
    {
        cout<<"Class Base::human(int age) "<<endl;
    }
    void man(int age)
    {
        cout<<"Class Base::man(int age) "<<endl;
    }
    //重载
    void man(int age, float weight)
    {
        cout<<"Class Base::man(int age, float weight) "<<endl;
    }
    virtual void woman(int age);
}

class Derived
{
public:
    void human(float age)
    {
        cout<<"Class Derived::human(float age)"<<endl;
    }
    void women(int age)
    {
        cout<<"class Derived::woman(int age)"<<endl;
    }   
}

int main
{
    
    Derived d;
    Base* pb = &d;//将Derived对象地址赋给Base对象时，编译器自动进行类型转换，
    编译器此时认为保存的就是Base对象地址
    b->human(23.5);//基类human被隐藏
    b->woman(21);//基类woman被覆盖
    b->man(23);
    b->man(23,60.0);//重载函数正常使用
     
    return 0;
}

最简单的是重载，同一个类中为了省去对新声明函数重新命名的麻烦，可以使用同一函数名，只要参数表不同即可。但是注意返回值类型也必须一致，否则会变成重新定义，是不允许的。
对于虚方法的覆盖，程序一般运行时，先找到找到类，如果它有基类，再找它的基类，最后运行的是基类中的函数，这时，它在基类中找到的是virtual标识的函数，它就会再回到子类中找同名函数。

2、0xC0000005: 读取位置 0x00000000 时发生访问冲突
来自：https://blog.csdn.net/heathyhuhu/article/details/18039359
遇见这种问题一般都是空指针，即：指针里没有赋值~ 
如果你对null 进行操作就会产生空指针异常

Object obj = new Object();
你要知道 obj是一个Object指针变量,指向Object类的一个实例
我们说obj是一个对象 实质是它指向一个对象的首地址 
如果这个指针变量obj 没有指向任何空间 你调用它的方法和属性就会出错

例如 Object obj = new Object();
     obj.equals("123");这个是没错的

但换下面语句 Object obj = null;
     obj.equals("123");就会出现空指针异常
未处理的异常: 0xC0000005: 读取位置 0x00000000 时发生访问冲突

     在使用的过程中，出现了标题中的错误，首先在网上搜了一些方法，费了好大的劲，终于解决了，
关于0xC0000005问题：
0xC0000005: Access Violation错误调试- -
1》数据越界或是定义的指针未释放.
2》空的指针的可能性最大。使用指针前最好能显式的赋值！ 
应该是指针的问题
3》内存访问错误，检查指针，是否为空，是否越界等
可能性 3 种 
1: 
char *p; 
p = new char[number]; 
delete [] p; 
.... 
// always using p.... 
p = xxx; // access violation 

2: 
char *p; 
memcpy(p, xxx, number); // access violation 

3: 
char *p; 
p = new char[number]; 
delete [] p; 
......... 
delete [] p; // access violation

	
