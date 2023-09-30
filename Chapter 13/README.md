## 练习 13.1：
### 拷贝构造函数是什么？什么时候使用它?
答：
* 当一个对象用另一个同类型的对象初始化时。
* 当一个函数的参数是类的对象，并通过值传递。
* 当函数按值返回一个类的对象。
* 其他需要复制同类型对象的情况。
## 练习 13.2：
### 解释为什么下面的声明是非法的：
```
Sales_data::Sales_data(Sales_data rhs);
```
答：
* 因为按值传递参数会触发复制操作，所以如果复制构造函数自己还需要按值传递其类型的对象，就会导致无限递归，从而造成程序崩溃。
## 练习 13.3:
### 当我们拷贝一个StrBlob时,会发生什么?拷贝一个StrBlobPtr呢?
答：
* 会有一个StrBlob和StrBlobPtr被拷贝！
* StrBlob的数据成员含有一个shared_ptr指针，StrBlobPtr的数据成员含有的则是一个weak_ptr指针，拷贝后shared_ptr的计数器会递增。
* StrBlob使用的是编译器合成的拷贝构造函数，该版本拷贝构造函数只会进行浅复制，所以StrBlob构造函数内动态分配的vector会被2个StrBlob对象共享。
## 练习 13.4:
### 假定Point是一个类类型,它有一个public的拷贝构造函数,指出下面程序片段中哪些地方使用了拷贝构造函数:
```

```
答：
```
Point global;
Point foo_bar(Point arg) // 按值传参时，通常会使用拷贝构造函数来初始化形参。
{
 Point local = arg, *heap = new Point(global); // 这里使用拷贝构造函数从arg初始化local。
 *heap = local;
 Point pa[ 4 ] = { local, *heap }; // local和*heap都用于初始化数组pa中的元素，这里使用了拷贝构造函数两次。
 return *heap; // 按值返回函数时，通常会使用拷贝构造函数来复制返回值
}
```
## 练习 13.5:
### 给定下面的类框架,编写一个拷贝构造函数,拷贝所有成员。你的构造函数应该动态分配一个新的string (参见12.1.2节,第407页),并将对象拷贝到ps指向的位置，而不是 ps 本身的位置。
```
class HasPtr {
public:
 HasPtr(const std::string &s = std::string()):
    ps(new std::string(s)), i(0) { }
private:
 std::string *ps;
 int i;
};
```
答：
```
class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }

	// 拷贝构造函数
	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)), i(hp.i) { }

private:
	std::string *ps;
	int i;
};
```
## 练习 13.6：
### 拷贝赋值运算符是什么？什么时候使用它？合成拷贝赋值运算符完成什么工作？什么时候会生成合成拷贝赋值运算符？
答：
* 拷贝赋值运算符是类的一个特殊的成员函数，用于处理类对象之间的赋值操作。其名为 operator=，通常接受一个与当前类类型相同的参数。
* 当我们要将一个对象的值赋给另一个已经存在的同类型对象时，就会使用拷贝赋值运算符。
* 合成拷贝赋值运算符会对其左侧对象的每个非static成员进行赋值操作。
* 当类未定义自己的拷贝赋值运算符时，编译器会为其生成一个合成拷贝赋值运算符。但需要注意的是，如果类定义了一个移动构造函数或移动赋值运算符，编译器就不会为其合成拷贝赋值运算符。
## 练习 13.7:
### 当我们将一个StrBlob赋值给另一个StrBlob时,会发生什么?赋值StrBlobPtr 呢?
答：
* StrBlob和StrBlobPtr都是采用的合成拷贝赋值运算符，只会进行浅拷贝，即对类的每个成员进行逐成员的赋值。对于指针成员，这意味着指针值本身（而不是它所指向的内容）会被复制。
## 练习 13.8：
### 为 13.1.1 节（第 443 页）练习 13.5 中的 HasPtr 类编写赋值运算符。类似拷贝构造函数,你的赋值运算符应该将对象拷贝到ps指向的位置。
答：
```
class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }
	// 拷贝构造函数
	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)), i(hp.i) { }
	// 拷贝赋值运算符
	HasPtr& operator=(const HasPtr& rhs) {
		if (this != &rhs) {  // 检查自我赋值
			delete ps;	// 删除当前字符串
			ps = new std::string(*rhs.ps);	// 从rhs分配新字符串
			i = rhs.i;
		}
		return *this;
	}
private:
	std::string *ps;
	int i;
};
```
## 练习13.9:
### 析构函数是什么?合成析构函数完成什么工作?什么时候会生成合成析构函数？
答：
* 析构函数是一个特殊的成员函数，它在一个对象的生命周期结束时自动被调用。析构函数的名称与类名相同，但前面加上了一个波浪符~，并且它不接受任何参数，也没有返回类型。
* 合成析构函数是编译器自动为类生成的析构函数。合成析构函数确保类的所有非静态成员对象都会被正确地销毁。但合成析构函数不会释放对象可能拥有的动态分配的内存，这种内存管理应由程序员手动处理。
* 当一个类没有显式定义析构函数时，编译器会自动为这个类生成一个合成析构函数。
## 练习 13.10:
### 当一个StrBlob 对象销毁时会发生什么？一个 StrBlobPtr 对象销毁时呢？
答：
* 当类对象被销毁时析构函数会被调用，析构函数体首先执行，随后它的所有成员和基类的析构函数按逆序（与构造顺序相反）被调用。最后，对象所占的内存会被释放。
## 练习 13.11:
### 为前面练习中的HasPtr类添加一个析构函数。
答：
```
class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }
	// 拷贝构造函数
	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)), i(hp.i) { }
	// 拷贝赋值运算符
	HasPtr& operator=(const HasPtr& rhs) {
		if (this != &rhs) {  // 检查自我赋值
			delete ps;	// 删除当前字符串
			ps = new std::string(*rhs.ps);	// 从rhs分配新字符串
			i = rhs.i;
		}
		return *this;
	}
	// 析构函数
	~HasPtr() {
		delete ps;	// 释放动态分配的内存
	}
private:
	std::string *ps;
	int i;
};
```
## 练习 13.12：
### 在下面的代码片段中会发生几次析构函数调用？
```
bool fcn(const Sales_data *trans, Sales_data accum)
{
 Sales_data item1(*trans), item2(accum);
 return item1.isbn() != item2.isbn();
}
```
答：
* 三次。accum、item1 和 item2 会因离开作用域的销毁而调用析构函数。
## 练习 13.13：
### 理解拷贝控制成员和构造函数的一个好方法是定义一个简单的类，为该类定义这些成员，每个成员都打印出自己的名字：
```
struct X {
X() {std::cout << "X()" << std::endl;}
X(const X&) {std::cout << "X(const X&)" <<std::endl;}
};
```
### 给 X 添加拷贝赋值运算符和析构函数，并编写一个程序以不同方式使用 X 的对象：将它们作为非引用和引用参数传递；动态分配它们；将它们存放于容器中；诸如此类。观察程序的输出，直到你确认理解了什么时候会使用拷贝控制成员，以及为什么会使用它们。当你观察程序输出时，记住编译器可以略过对拷贝构造函数的调用。
答：
```
#include <iostream>
#include <vector>

using namespace std;

struct X {
	X() { std::cout << "X()" << std::endl; }
	X(const X&) { std::cout << "X(const X&)" << std::endl; }
	X& operator=(const X&) {
		std::cout << "X& operator=(const X&)" << std::endl;
		return *this;
	}
	~X() { std::cout << "~X()" << std::endl; }
};

void f1(X x) { }
void f2(X &x) { }
void f3(const X &x) { }

int main() {
	std::cout << "Directly create an object:" << std::endl;
	X x1;

	std::cout << "\nCopy initialize an object:" << std::endl;
	X x2 = x1;

	std::cout << "\nAssign an object:" << std::endl;
	X x3;
	x3 = x1;

	std::cout << "\nPass an object by value:" << std::endl;
	f1(x1);

	std::cout << "\nPass an object by reference:" << std::endl;
	f2(x1);

	std::cout << "\nPass an object by const reference:" << std::endl;
	f3(x1);

	std::cout << "\nDynamically allocate objects:" << std::endl;
	X* px = new X;
	delete px;

	std::cout << "\nStore objects in a container:" << std::endl;
	std::vector<X> vec;
	vec.push_back(x1);

	std::cout << "\nEnd of main()" << std::endl;
	return 0;
}
```
## 练习 13.14：
### 假定 numbered 是一个类，它有一个默认构造函数，能为每个对象生成一个唯一的序号,保存在名为mysn的数据成员中。 假定numbered使用合成的拷贝控制成员，并给定如下函数：
```
void f (numbered s) { cout << s.mysn << endl; }
```
### 则下面代码输出什么内容？
```
numbered a, b = a, c = b;
f(a); f(b); f(c);
```
答：
* 三条输出的内容相同。
## 练习 13.15:
### 假定numbered定义了一个拷贝构造函数,能生成一个新的序号。 这会改变上一题中调用的输出结果吗？ 如果会改变，为什么？ 新的输出结果是什么？
答：
* 会改变。因为新定义的拷贝构造函数保证了拷贝发生时也能生成一个新的序号。新的输出结果是函数f的局部numbered对象的序号。
## 练习 13.16：
### 如果 f 中的参数是 const numbered&，将会怎样？这会改变输出结果吗？如果会改变，为什么？新的输出结果是什么？
答：
* 将会按引用传参。会改变输出结果。因为按引用传参不会创建新的numbered对象。新的输出结果是调用f()前 a、b、c的序号。
## 练习 13.17：
### 分别编写前三题中所描述的 numbered 和 f，验证你是否正确预测了输出结果。
答：
```
#include <iostream>

using namespace std;

class numbered {
public:
	// 默认构造函数
	numbered() : mysn(unique++) {}
	// 拷贝构造函数
	// numbered(const numbered& n) : mysn(unique++) {}
	// 返回对象的序号
	int get_mysn() const { return mysn; }
private:
	int mysn;               // 保存对象的序号
	static int unique;      // 静态数据成员，用于生成唯一序号
};

// 初始化静态数据成员
int numbered::unique = 9;

void f(numbered s) { cout << s.get_mysn() << endl; }
//void f(const numbered &s) { cout << s.get_mysn() << endl; }

int main() {
	numbered a, b = a, c = b;
	f(a); f(b); f(c);

	return 0;
}
```
## 练习 13.18:
### 定义一个Employee类,它包含雇员的姓名和唯一的雇员证号。 为这个类定义默认构造函数，以及接受一个表示雇员姓名的 string的构造函数。 每个构造函数应该通过递增一个static数据成员来生成一个唯一的证号。
答：
```
#include <iostream>
#include <string>

class Employee {
public:
	// 默认构造函数
	Employee() : name(""), id(unique_id++) {}

	// 接受一个表示雇员姓名的 string 的构造函数
	Employee(const std::string& s) : name(s), id(unique_id++) {}

	// 获取姓名和ID的成员函数
	std::string getName() const { return name; }
	int getId() const { return id; }

private:
	std::string name;
	int id;

	// 静态数据成员，用于生成唯一的证号
	static int unique_id;
};

// 初始化静态数据成员
int Employee::unique_id = 0;

int main() {
	Employee e1, e2("John");
	std::cout << e1.getName() << ": " << e1.getId() << std::endl; // : 0
	std::cout << e2.getName() << ": " << e2.getId() << std::endl; // John: 1

	return 0;
}
```
## 练习 13.19：
### 你的Employee类需要定义它自己的拷贝控制成员吗？如果需要，为什么？如果不需要,为什么？实现你认为Employee需要的拷贝控制成员。
答：
* 为了保证为每个雇员分配一个唯一的证号，应该禁止拷贝构造和拷贝赋值。
```
class Employee {
public:
    // ... [其他成员如前]

    // 删除拷贝构造函数和拷贝赋值运算符
    Employee(const Employee&) = delete;
    Employee& operator=(const Employee&) = delete;
};
```
## 练习 13.20：
### 解释当我们拷贝、赋值或销毁 TextQuery 和 QueryResult 类(参见 12.3 节,第430页)对象时会发生什么。
答：
* TextQuery 和 QueryResult 类使用的是合成版本拷贝控制成员，当拷贝、赋值时会进行浅拷贝。当销毁时合成析构函数不会释放动态分配的内存，不过这两个类管理动态资源的指针都是智能指针，智能指针的析构函数会负责释放动态分配的内存。
## 练习 13.21：
### 你认为 TextQuery 和 QueryResult 类需要定义它们自己版本的拷贝控制成员吗?如果需要,为什么?如果不需要,为什么?实现你认为这两个类需要的拷贝,控制操作。
答：
* 不需要，使用合成版本的拷贝控制成员并不会产生问题。
## 练习 13.22：
### 假定我们希望 HasPtr 的行为像一个值。即，对于对象所指向的 string 成员，每个对象都有一份自己的拷贝。我们将在下一节介绍拷贝控制成员的定义。但是，你已经学习了定义这些成员所需的所有知识。在继续学习下一节之前，为 HasPtr 编写拷贝构造函数和拷贝赋值运算符。
答：
```
class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }
	// 拷贝构造函数
	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)), i(hp.i) { }
	// 拷贝赋值运算符
	HasPtr& operator=(const HasPtr& rhs) {
		if (this != &rhs) {  // 检查自我赋值
			delete ps;	// 删除当前字符串
			ps = new std::string(*rhs.ps);	// 从rhs分配新字符串
			i = rhs.i;
		}
		return *this;
	}
	// 析构函数
	~HasPtr() {
		delete ps;	// 释放动态分配的内存
	}
private:
	std::string *ps;
	int i;
};
```