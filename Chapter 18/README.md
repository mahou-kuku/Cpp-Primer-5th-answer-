## 练习 18.1：
### 在下列 throw 语句中异常对象的类型是什么？
```
(a) range_error r("error");
throw r;
(b) exception *p = &r;
throw *p;
```
### 如果将(b)中的throw语句写成了throw p将发生什么情况？
答：
* (a) 是 range_error 。
* (b) 是 p 所指向的静态类型，也就是 exception 。
* 如果将 (b) 中的 throw 语句写成了 throw p;，那么将会抛出一个指向 exception 的指针，而不是抛出一个异常对象。
## 练习 18.2：
### 当在指定的位置发生了异常时将出现什么情况？
```
void exercise(int *b, int *e){
 vector<int> v(b, e);
 int *p = new int[v.size()];
 ifstream in("ints");
 // 此处发生异常
}
```
答：
* 由于异常导致的函数提前退出，p 指向的内存没有被 delete，从而导致内存泄漏。
## 练习 18.3：
### 要想让上面的代码在发生异常时能正常工作，有两种解决方案。请描述这两种方法并实现它们。
答：
* 使用智能指针
```
void exercise(int *b, int *e) {
	vector<int> v(b, e);
	// 使用智能指针来管理动态分配的内存，避免内存泄漏
	unique_ptr<int[]> p(new int[v.size()]);
	ifstream in("ints");
	// 此处发生异常
}
```
* 使用try-catch块来确保资源被正确管理
```
void exercise(int *b, int *e) {
	vector<int> v(b, e);
	int *p = nullptr;
	try {
		p = new int[v.size()];
		ifstream in("ints");
		// 此处发生异常

		// 正常处理完成后，确保释放分配的内存
		delete[] p;
	}
	catch (...) { // 捕获所有异常
		delete[] p; // 释放资源
		throw; // 重新抛出当前捕获的异常
	}
}
```
## 练习 18.4：
### 查看图18.1 (第693页)所示的继承体系,说明下面的try块有何错误并修改它。
```
try {
 // 使用 C++标准库
} catch(exception) {
 // ...
} catch(const runtime_error &re) {
 // ...
} catch(overflow_error eobj) { /* ... */ }
```
答：
* 问题在于异常的捕获顺序。异常应该从最具体（最派生）的类型开始捕获，逐渐到最通用（最基本）的类型。
```
try {
    // 使用 C++标准库
} catch (const overflow_error& eobj) { // 先捕获具体的异常类型
    // ...
} catch (const runtime_error& re) { // 然后捕获其基类类型的异常
    // ...
} catch (const exception&) { // 最后捕获所有标准异常的基类
    // ...
}
```
## 练习 18.5：
### 修改下面的 main函数，使其能捕获图 18.1 （第 693 页)所示的任何异常类型：
```
int main() {
 // 使用 C++标准库
}
```
### 处理代码应该首先打印异常相关的错误信息，然后调用 abort（定义在 cstdlib 头文件中）终止 main 函数。
答：
```
#include <iostream>
#include <stdexcept>
#include <typeinfo>
#include <new>
#include <cstdlib>

int main() {
	try {
		// 使用 C++标准库
	} catch (const std::bad_cast& e) {
		std::cerr << "Bad Cast: " << e.what() << std::endl;
	} catch (const std::bad_alloc& e) {
		std::cerr << "Bad Alloc: " << e.what() << std::endl;
	} catch (const std::overflow_error& e) {
		std::cerr << "Overflow Error: " << e.what() << std::endl;
	} catch (const std::underflow_error& e) {
		std::cerr << "Underflow Error: " << e.what() << std::endl;
	} catch (const std::range_error& e) {
		std::cerr << "Range Error: " << e.what() << std::endl;
	} catch (const std::runtime_error& e) {
		std::cerr << "Runtime Error: " << e.what() << std::endl;
	} catch (const std::domain_error& e) {
		std::cerr << "Domain Error: " << e.what() << std::endl;
	} catch (const std::invalid_argument& e) {
		std::cerr << "Invalid Argument: " << e.what() << std::endl;
	} catch (const std::out_of_range& e) {
		std::cerr << "Out of Range: " << e.what() << std::endl;
	} catch (const std::length_error& e) {
		std::cerr << "Length Error: " << e.what() << std::endl;
	} catch (const std::logic_error& e) {
		std::cerr << "Logic Error: " << e.what() << std::endl;
	} catch (const std::exception& e) {
		// 捕获其他所有的 std::exception 异常
		std::cerr << "Exception: " << e.what() << std::endl;
	}

	std::abort(); // 终止程序

	return 0;
}
```
## 练习 18.6：
### 已知下面的异常类型和catch语句,书写一个throw表达式使其创建的异常对象能被这些catch语句捕获:
```
(a) class exceptionType { };
catch(exceptionType *pet) { }
(b) catch(...) { }
(c) typedef int EXCPTYPE;
catch(EXCPTYPE) { }
```
答：
* (a) throw new exceptionType;
* (b) throw "an error message";
* (c) throw EXCPTYPE(42);
## 练习 18.7：
### 根据第 16 章的介绍定义你自己的 Blob 和 BlobPtr，注意将构造函数写成函数try语句块。
答：
```
template <typename T>
Blob<T>::Blob() try : data(make_shared<vector<T>>()) {
    // 构造函数体为空
} catch (const std::bad_alloc& e) {
    // 处理内存分配异常
    handle_out_of_memory(e);
}

template <typename T>
Blob<T>::Blob(initializer_list<T> il) try : data(make_shared<vector<T>>(il)) {
    // 构造函数体为空
} catch (const std::bad_alloc& e) {
    // 处理内存分配异常
    handle_out_of_memory(e);
}
```
## 练习 18.8：
### 回顾你之前编写的各个类,为它们的构造函数和析构函数添加正确的异常说明。如果你认为某个析构函数可能抛出异常,尝试修改代码使得该析构函数不会抛出异常。
答：
* 在析构函数中抛出异常非常危险，因为析构函数通常是在对象生命周期结束时自动调用的，程序可能没有相应的机制来合理处理这种情况。并且C++ 不允许同时有多个未处理的异常，如果一个析构函数在栈展开过程中抛出一个未捕获的异常，那么程序将会调用 std::terminate() 来终止运行。如果确实需要在析构函数中执行可能抛出异常的操作，应该在析构函数中捕获所有异常，确保不会从析构函数中抛出任何异常。
## 练习 18.9：
### 定义本节描述的书店程序异常类,然后为Sales_data类重新编写一个复合赋值运算符并令其抛出一个异常。
答：
```
// 为某个书店应用程序设定的异常类
class out_of_stock : public std::runtime_error {
public:
	explicit out_of_stock(const std::string &s) : std::runtime_error(s) { }
};
class isbn_mismatch : public std::logic_error {
public:
	explicit isbn_mismatch(const std::string &s) : std::logic_error(s) { }
	isbn_mismatch(const std::string &s, const std::string &lhs, const std::string &rhs) :
		std::logic_error(s), left(lhs), right(rhs) { }
	const std::string left, right;
};

// 如果参与加法的两个对象并非同一书籍，则抛出一个异常
Sales_data& Sales_data::operator+=(const Sales_data& rhs){
	if (isbn() != rhs.isbn()) {
		throw isbn_mismatch("wrong isbns", isbn(), rhs.isbn());
	}
	units_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}
```
## 练习 18.10：
### 编写程序令其对两个ISBN编号不相同的对象执行Sales_data的加法运算。为该程序编写两个不同的版本:一个处理异常,另一个不处理异常。观察并比较这两个程序的行为,用心体会当出现了一个未被捕获的异常时程序会发生什么情况。
答：
```
// 处理异常
int main() {
	Sales_data item_1("Aaa", 20, 15), item_2("Bbb", 10, 15);
	try {
		item_1 += item_2;
	} catch (const isbn_mismatch &e) {
		cerr << e.what() << ": left isbn(" << e.left <<
			") right isbn(" << e.right << ")" << endl;
	}

	return 0;
}
```
* 当在程序中遇到一个未被捕获的异常时，C++ 标准规定程序将调用 std::terminate() 函数来终止程序。
```
// 不处理异常
int main() {
	Sales_data item_1("Aaa", 20, 15), item_2("Bbb", 10, 15);
	item_1 += item_2;

	return 0;
}
```
## 练习 18.11：
### 为什么what函数不应该抛出异常？
答：
* 从C++11开始，what 函数被明确声明为 noexcept。这意味着标准要求 what 函数保证不抛出异常。
* what 函数被设计为在异常处理过程中调用，作为获取异常信息的一种可靠方式。如果 what 函数可能抛出异常，那么它就不能保证总是能够提供异常信息，从而降低了异常处理代码的可靠性。
## 练习 18.12：
### 将你为之前各章练习编写的程序放置在各自的命名空间中。也就是说,命名空间chapter15包含Query程序的代码,命名空间chapter10包含TextQuery 的代码;使用这种结构重新编译Query代码示例。
答：
* 使用作用域运算符指出所用的名字属于哪个命名空间即可。
## 练习 18.13：
### 什么时候应该使用未命名的命名空间？
答：
* 限制访问范围：当希望某些函数、变量或类型的作用范围仅限于当前源文件时，应该使用未命名的命名空间。
* 避免命名冲突：未命名的命名空间可以用来减少全局命名空间的污染，避免不同编译单元中定义的具有相同名称的实体之间的命名冲突。
* 替代static关键字：与在函数外部使用static声明全局静态变量或函数相比，使用未命名的命名空间可以更清晰地表达开发者的意图，并且适用于不能使用static关键字的实体（如类、结构体、模板）。
## 练习 18.14：
### 假设下面的operator*声明的是嵌套的命名空间mathLib::MatrixLib 的一个成员:
```
namespace mathLib {
	namespace MatrixLib {
		class matrix { /* ... */ };
		matrix operator* (const matrix &, const matrix &);
		// ... 
	}
}
```
### 请问你应该如何在全局作用域中声明该运算符？
答：
```
mathLib::MatrixLib::matrix mathLib::MatrixLib::operator*(
	const mathLib::MatrixLib::matrix &, const mathLib::MatrixLib::matrix &);
```
## 练习 18.15：### 说明using指示与using声明的区别。
答：
* using指示：会将一个命名空间中的所有名称引入到当前作用域中，这可能会导致名称冲突二义性问题。
* using声明：只引入指定的单个名称到当前作用域中，减少了发生冲突和二义性问题的可能性。
## 练习 18.16：### 假定在下面的代码中标记为“位置1”的地方是对于命名空间Exercise中所有成员的using声明,请解释代码的含义。如果这些using声明出现在“位置2”又会怎样呢？将 using声明变为 using指示，重新回答之前的问题。
```
namespace Exercise {
	int ivar = 0;
	double dvar = 0;
	const int limit = 1000;
}
int ivar = 0;
// 位置1
void manip() {
	// 位置2
	double dvar = 3.1416;
	int iobj = limit + 1;
	++ivar;
	++::ivar;
}
```
答：
* using声明在“位置1”：这会使得Exercise命名空间中的ivar、dvar和limit在全局作用域中直接可用。但因为全局作用域已经有一个名为ivar的全局变量，所以这会导致ivar在全局作用域中出现二义性错误。dvar和limit可以被正确使用。
* using声明在“位置2”：Exercise命名空间中的ivar、dvar和limit只在manip函数作用域中直接可用。在这个作用域内部，dvar会与局部变量dvar产生二义性，而ivar和limit可以被正确识别和使用。
* using指示在“位置1”：Exercise命名空间中的所有成员都被引入到全局作用域中，导致全局作用域中的ivar与Exercise中的ivar产生二义性错误。
* using指示在“位置2”，即在manip函数内部，也会导致全局作用域中的ivar与Exercise中的ivar产生二义性错误。
## 练习 18.17：### 实际编写代码检验你对上一题的回答是否正确。
答：
* 在位置1、2放置相应using语句即可。
```
// using声明
using Exercise::ivar;
using Exercise::dvar;
using Exercise::limit;
// using指示
using namespace Exercise;
```
## 练习 18.18：
### 已知有下面的swap的典型定义(参见13.3节,第457页),当mem1是一个string 时程序使用 swap的哪个版本？ 如果 mem1 是 int 呢？ 说明在这两种情况下名字查找的过程。
```
void swap(T v1, T v2)
{
	using std::swap;
	swap(v1.mem1, v2.mem1);
	// 交换类型 T的其他成员
}
```
答：
* 首先在当前作用域中寻找合适的函数,接着查找调用语句的外层作用域。对于类类型，将在类型定义的命名空间中寻找合适的swap函数。
* 由于使用了using std::swap; 将使标准库中所有版本的swap函数成为候选函数。std命名空间中有为std::string特化的swap版本，并且标准库中的swap提供了对内置类型的通用支持，当mem1是一个string或int时将调用std命名空间中相应版本的swap。
## 练习 18.19：
### 如果对swap的调用形如std::swap (v1.mem1, v2.mem1)将发生什么情况?
答：
* 当调用形式为std::swap(v1.mem1, v2.mem1)时，意味着直接请求使用标准库提供的swap函数，这绕过了名字查找的过程。
## 练习 18.20：
### 在下面的代码中,确定哪个函数与compute调用匹配。列出所有候选函数和可行函数,对于每个可行函数的实参与形参的匹配过程来说,发生了哪种类型转换？
```
namespace primerLib {
 void compute();
 void compute(const void *);
}
using primerLib::compute;
void compute(int);
void compute(double, double = 3.4);
void compute(char*, char* = 0);
void f(){
 compute(0);
}
```
答：
* compute(int);是最佳匹配。
* 候选函数:
* primerLib::compute();
* primerLib::compute(const void *);
* compute(int);
* compute(double, double = 3.4);
* compute(char*, char* = 0);
* 可行函数:
* primerLib::compute(const void *);
* compute(int);
* compute(double, double = 3.4);
* compute(char*, char* = 0);
* 类型转换:
* 对于primerLib::compute(const void \*); 实参0被视为nullptr，隐式转换为const void*类型。
* 对于compute(int); 实参0无需转换，直接匹配int类型的形参。
* 对于compute(double, double = 3.4); 实参0通过标准转换提升为double类型。
* 对于compute(char*, char* = 0); 实参0被视为nullptr，隐式转换为char*类型。
### 如果将 using 声明置于 main 函数中 compute 的调用点之前将发生什么情况？重新回答之前的那些问题。
答：
* 当using声明置于main函数内部,primerLib::compute的两个重载版本将加入到main函数的局部作用域中,compute(int);仍是最佳匹配。
* 在这种情况下，只有全局作用域中的compute函数重载会被考虑为候选函数，即：
* void compute(int);
* void compute(double, double = 3.4);
* void compute(char*, char* = 0);
## 练习 18.21：
### 解释下列声明的含义,在它们当中存在错误吗?如果有,请指出来并说明错误的原因。
```
(a) class CADVehicle : public CAD, Vehicle { ... };
(b) class DblList: public List, public List { ... };
(c) class iostream: public istream, public ostream { ... };
```
答：
* (b)存在错误，在某个给定的派生列表中，同一个基类只能出现一次。
## 练习 18.22：
### 已知存在如下所示的类的继承体系,其中每个类都定义了一个默认构造函数：
```
class A { ... };
class B : public A { ... };
class C : public B { ... };
class X { ... };
class Y { ... };
class Z : public X, public Y { ... };
class MI : public C, public Z { ... };
```
### 对于下面的定义来说,构造函数的执行顺序是怎样的？
```
MI mi;
```
答：
* 执行顺序为：A()、B()、C()、X()、Y()、Z()、MI() 。
## 练习 18.23：
### 使用练习18.22的继承体系以及下面定义的类D,同时假定每个类都定义了默认构造函数，请问下面的哪些类型转换是不被允许的？
```
class D : public X, public C { ... };
D *pd = new D;
(a) X *px = pd;
(b) A *pa = pd;
(c) B *pb = pd;
(d) C *pc = pd;
```
答：
* 都可以通过编译。
## 练习 18.24：
### 在第 714 页，我们使用一个指向 Panda 对象的 Bear 指针进行了一系列调用，假设我们使用的是一个指向 Panda 对象的 ZooAnimal 指针将发生什么情况，请对这些调用语句逐一进行说明。
答：
```
ZooAnimal *pz = new Panda("ying_yang");
pz->print(); // 正确：Panda::print()
pz->toes(); // 错误：不属于 ZooAnimal 的接口
pz->cuddle(); // 错误：不属于 ZooAnimal 的接口
pz->highlight(); // 错误：不属于 ZooAnimal 的接口
delete pz; // 正确： Panda::~Panda()
```
## 练习18.25：
### 假设我们有两个基类Base1和Base2,它们各自定义了一个名为print的虚成员和一个虚析构函数。从这两个基类中我们派生出下面的类,它们都重新定义了print 函数：
```
class D1 : public Base1 { /* ... */ };
class D2 : public Base2 { /* ... */ };
class MI : public D1, public D2 { /* ... */ };
```
### 通过下面的指针，指出在每个调用中分别使用了哪个函数：
```
Base1 *pb1 = new MI;
Base2 *pb2 = new MI;
D1 *pd1 = new MI;
D2 *pd2 = new MI;
(a) pb1->print();
(b) pd1->print();
(c) pd2->print();
(d) delete pb2;
(e) delete pd1;
(f) delete pd2;
```
答：
* 对print的调用都是在调用MI::print(),delete语句调用析构函数的执行顺序都是：~MI()、~D2()、~Base2()、~D1()、~Base1() 。

## 练习 18.26：
```
struct Base1 {
 void print(int) const; // public by default
protected:
 int ival;
 double dval;
 char cval;
private:
 int *id;
};
struct Base2 {
 void print(double) const; // public by default
protected:
 double fval;
private:
 double dval;
};
struct Derived : public Base1 {
 void print(std::string) const; // public by default
protected:
 std::string sval;
 double dval;
};
struct MI : public Derived, public Base2 {
 void print(std::vector<double>); // public by default
protected:
 int *ival;
 std::vector<double> dvec;
};
```
### 已知如上所示的继承体系，下面对 print 的调用为什么是错误的？适当修改 MI，令其对 print 的调用可以编译通过并正确执行。
```
MI mi;
mi.print(42);
```
答：
* 因为MI没有一个void print(int);成员函数，给MI添加void print(int);即可。
```
struct MI : public Derived, public Base2 {
	void print(std::vector<double>); // public by default
	void print(int);
protected:
	int *ival;
	std::vector<double> dvec;
};
```
## 练习 18.27：
### 已知如上所示的继承体系，同时假定为 MI 添加了一个名为 foo 的函数：
```
int ival;
double dval;
void MI::foo(double cval)
{
 int dval;
 // 练习中的问题发生在此处
}

(a)列出在MI::foo中可见的所有名字。
(b)是否存在某个可见的名字是继承自多个基类的?
(c)将Base1的dval成员与Derived的dval成员求和后赋给dval的局部实例。
(d)将MI::dvec的最后一个元素的值赋给Base2::fval。
(e)将从Base1继承的cval赋给从Derived继承的sval的第一个字符。
```
答：
* (a) MI类中定义的名字：ival、dval、dvec、print。 Derived类中定义的名字：sval、dval、print。 Base1类中定义的名字：ival、dval、cval、print。 Base2类中定义的名字：fval、print。
* (b) 并没有直接“可见”的继承自多个基类的名字，继承自多个基类的名字有print和dval，但它们在MI类中被相同的名字隐藏了，访问它们需要显式的作用域限定。
* (c) dval = Derived::dval + Base1::dval;
* (d) fval = dvec.back();
* (e) sval\[0] = Base1::cval;
## 练习 18.28：
### 已知存在如下的继承体系,在VMI类的内部哪些继承而来的成员无须前缀限定符就能直接访问?哪些必须有限定符才能访问?说明你的原因。
```
struct Base {
	void bar(int); // 默认情况下是公有的
protected:
	int ival;
};
struct Derived1 : virtual public Base {
	void bar(char); // 默认情况下是公有的
	void foo(char);
protected:
	char cval;
};
struct Derived2 : virtual public Base {
	void foo(int); // 默认情况下是公有的
protected:
	int ival;
	char cval;
};
class VMI : public Derived1, public Derived2 { };
```
答：
* 无须前缀限定符的继承成员：
* void Derived1::bar(char) 从Derived1直接继承而来，隐藏了Base的bar 。
* Derived2::ival 从Derived2直接继承而来，隐藏了Base的ival 。
* 需要限定符的继承成员：
* void Base::bar(int) 被Derived1的bar隐藏。
* Base::ival 被Derived2的ival隐藏.
* void Derived1::foo(char) 和Derived2的同名成员产生了二义性。
* Derived1::cval 和Derived2的同名成员产生了二义性。
* void Derived2::foo(int) 和Derived1的同名成员产生了二义性。
* Derived2 cval 和Derived1的同名成员产生了二义性。
## 练习 18.29：
### 已知有如下所示的类继承关系：
```
class Class { ... };
class Base : public Class { ... };
class D1 : virtual public Base { ... };
class D2 : virtual public Base { ... };
class MI : public D1, public D2 { ... };
class Final : public MI, public Class { ... };
```
### (a)当作用于一个Final对象时,构造函数和析构函数的执行次序分别是什么?
答：
* 构造函数的执行顺序：
* Class()、Base()、D1()、D2()、MI()、Class()、Final() 。
* 析构函数的执行顺序：
* Final()、Class()、 MI()、D2()、D1()、Base()、Class()。
### (b)在一个Final对象中有几个Base部分?几个Class部分？
答：
* 1个Base和两个Class。
### (c)下面的哪些赋值运算将造成编译错误？
```
Base *pb; Class *pc; MI *pmi; D2 *pd2;
(a) pb = new Class;
(b) pc = new Final;
(c) pmi = pb;
(d) pd2 = pmi;
```
答：
* (a) 编译错误。尝试将基类指针赋值给派生类指针需要向下转换，在没有显式类型转换的情况下将造成编译错误。
* (b) 编译错误。基类Class不明确。编译器无法确定使用哪一个Class子对象进行赋值。
* (c) 编译错误。尝试将基类指针赋值给派生类指针需要向下转换，在没有显式类型转换的情况下将造成编译错误。
* (d) 通过编译。
## 练习 18.30：
### 在 Base 中定义一个默认构造函数、一个拷贝构造函数和一个接受 int 形参的构造函数。 在每个派生类中分别定义这三种构造函数,每个构造函数应该使用它的实参初始化其 Base 部分。
答：
```
#include <iostream>

class Class {
public:
	Class() { std::cout << "Class default constructor\n"; }
};

class Base : public Class {
public:
	Base() { std::cout << "Base default constructor\n"; }
	Base(const Base&) { std::cout << "Base copy constructor\n"; }
	Base(int) { std::cout << "Base int constructor\n"; }
};

class D1 : virtual public Base {
public:
	D1() : Base() { std::cout << "D1 default constructor\n"; }
	D1(const D1& obj) : Base(obj) { std::cout << "D1 copy constructor\n"; }
	D1(int i) : Base(i) { std::cout << "D1 int constructor\n"; }
};

class D2 : virtual public Base {
public:
	D2() : Base() { std::cout << "D2 default constructor\n"; }
	D2(const D2& obj) : Base(obj) { std::cout << "D2 copy constructor\n"; }
	D2(int i) : Base(i) { std::cout << "D2 int constructor\n"; }
};

class MI : public D1, public D2 {
public:
	MI() : D1(), D2() { std::cout << "MI default constructor\n"; }
	MI(const MI& obj) : D1(obj), D2(obj), Base(obj) { std::cout << "MI copy constructor\n"; }
	MI(int i) : D1(i), D2(i), Base(i) { std::cout << "MI int constructor\n"; }
};

class Final : public MI, public Class {
public:
	Final() : MI() { std::cout << "Final default constructor\n"; }
	Final(const Final& obj) : MI(obj), Base(obj) { std::cout << "Final copy constructor\n"; }
	Final(int i) : MI(i), Base(i) { std::cout << "Final int constructor\n"; }
};

int main() {
	Final f(9);
	std::cout << "===========" << std::endl;
	Final f2 = f;

	system("pause");
	return 0;
}
```
