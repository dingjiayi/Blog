## C++11 =default 和 =delete
### 1. = default
#### 1.1 引入背景
C++ 的类有四类特殊的成员函数，分别为：默认构造函数，析构函数，拷贝函数以及拷贝赋值函数。如果程序没有显式地为一个类定义某个特殊成员函数，而又需要用到该特殊成员函数时，编译器会隐式地为这个类生成一个默认的特殊成员函数。
例如：
```c++
class X {
private:
	int a;
};
X x;   //可以编译通过，编译器隐式生成默认构造函数
```

但是如果X  显式的自定义了非默认构造函数，却没有定义默认构造函数，下面的代码会出现编译错误

```c++
class X{
public:
	X(int i){
        a = i;
	}
private:
    int a;
};

X x; // 错误，默认构造函数 X::X()不存在	
```

为了解决上面的问题，我们需要自定义默认构造函数，如下

```c++
class X{
public:
    X(){};  // 手动定义默认构造函数
	X(int i){
        a = i;
	}
private:
    int a;
};

X x; //正确，默认构造函数 X::X()不存在	
```

但是手动编写存在两个问题：1. 程序员工作量变大 2. 没有编译器自动生成的默认特殊构造函数效率高。

不能看到上面的例子中，工作量不大，就认为上面的第一个问题不存在。请看下面的例子：

```c++
class A
{
public:
    A(int ii, char cc):i(ii),c(cc){}
    A(const A& a);
private:
    int i;
    char c;
};
```

上述代码如果要手动写出拷贝构造函数，如下

```
A(const A& a):i(a.i),c(a.c){}
```

为了解决上述的两个问题，C++ 11标准引入了一个新特性：defaulted 函数。

#### 1.2 使用

##### 1.2.1 使用规则

​	(1) defaulted 函数特性仅用于类的特殊成员函数，且该特殊成员函数没有默认参数。例如：

```c++
class X {
public:
	int f() = default: //错误， f()非特殊成员函数
    X(int) = default; //错误， 非特殊成员函数
    X(int i = 1) = default; // 错误， 含有默认参数
}
//能够支持default的，都是编译器能够在该情形下明确要做什么（构造函数一般是逐bit拷贝）
```



​	(2) 既可以在类体里定义(inline),也可以在类体外(out-of-line)定义。例如

```c++
class X {
public:
	X() = default; // inline
    X(const X&); // 
    X& operator = (const X&);
    ~X() = default; // inline
}

X::X(const X&) = default; // out of line
X& X::operator = (const X&) = default; // out of line
```

##### 1.2.2 析构函数中的使用

​	先看一个例子：

```c++
class X { 
private: 
 int x; 
}; 
 
class Y: public X { 
private: 
 int y; 
}; 
 
int main(){ 
 X* x = new Y; 
 delete x; 
}
```

上面的程序有内存泄漏的问题，因为这种情况下，会调用基类X的析构函数，并非Y的析构函数。而且此时X 的析构函数是编译器隐式自动生成的。

为了解决上面的问题，我们可以手动将X 的析构函数定义为虚函数，如下：

```c++
class X { 
public: 
 virtual ~X(){};     // 手动定义虚析构函数
private: 
 int x; 
}; 
 
class Y: public X { 
private: 
 int y; 
}; 
 
int main(){ 
 X* x = new Y; 
 delete x; 
 }
```

同样，为了 1. 减轻程序员的编程工作量 2.函数执行效率，我们使用default特性，如下：

```c++
class X { 
public: 
 virtual ~X()= defaulted; // 编译器自动生成 defaulted 函数定义体
private: 
 int x; 
}; 
 
class Y: public X { 
private: 
 int y; 
}; 
 
int main(){ 
 X* x = new Y; 
 delete x;
```

## 2. = delete

