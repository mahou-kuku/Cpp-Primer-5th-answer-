## 练习 19.1： 
### 使用 malloc 编写你自己的 operator new (size_t) 函数，使用 free 编写operator delete (void *)函数。
答：
```
void *operator new(size_t size){ 
	if (void *mem = malloc(size)) {
		return mem;
	} else {
		throw std::bad_alloc();
	}
}

void operator delete(void *mem) noexcept { free(mem); }
```
## 练习 19.2： 
### 默认情况下, allocator类使用operator new获取存储空间,然后使用 operator delete 释放它。利用上一题中的两个函数重新编译并运行你的 StrVec 程序(参见13.5节，第465页)。
答：
```
#define _SCL_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdlib>
#include <new>
#include <string>
#include <memory>

using namespace std;

void *operator new(size_t size){
	if (void *mem = malloc(size)) {
		cout << "In operator new " << endl;
		return mem;
	} else {
		throw std::bad_alloc();
	}
}

void operator delete(void *mem) noexcept {
	cout << "In operator delete " << endl; 
	free(mem);
}


// 类似vector类内存分配策略的简化实现
class StrVec {
public:
	StrVec() : elements(nullptr), first_free(nullptr), cap(nullptr) {} //allocator 成员进行默认初始化
	StrVec(const StrVec&); // 拷贝构造函数
	StrVec &operator=(const StrVec&); // 拷贝赋值运算符
	~StrVec(); // 析构函数
	void push_back(const string&); // 拷贝元素
	size_t size() const { return first_free - elements; }
	size_t capacity() const { return cap - elements; }
	string *begin() const { return elements; }
	string *end() const { return first_free; }
private:
	allocator<string> alloc; // 分配元素
	// 被添加元素的函数所使用
	void chk_n_alloc() { if (size() == capacity()) reallocate(); }
	// 工具函数，被拷贝构造函数、赋值运算符和析构函数所使用
	pair<string*, string*> alloc_n_copy(const string*, const string*);
	void free(); // 销毁元素并释放内存
	void reallocate(); // 获得更多内存并拷贝已有元素
	string *elements; // 指向数组首元素的指针
	string *first_free; // 指向数组第一个空闲元素的指针
	string *cap; // 指向数组尾后位置的指针
};

void StrVec::push_back(const string& s) {
	chk_n_alloc(); // 确保有空间容纳新元素
	// 在 first_free 指向的元素中构造 s 的副本
	alloc.construct(first_free++, s);
}

pair<string*, string*> StrVec::alloc_n_copy(const string *b, const string *e) {
	// 分配空间保存给定范围中的元素
	auto data = alloc.allocate(e - b);
	// 初始化并返回一个 pair，该 pair 由 data 和 uninitialized_copy 的返回值构成
	return{ data, uninitialized_copy(b, e , data) };
}

void StrVec::free() {
	// 不能传递给deallocate一个空指针,如果elements为0,函数什么也不做
	if (elements) {
		// 逆序销毁旧元素
		for (auto p = first_free; p != elements; /* 空 */) {
			alloc.destroy(--p);
		}
		alloc.deallocate(elements, cap - elements);
	}
}

StrVec::StrVec(const StrVec &s) {
	// 调用 alloc_n_copy分配空间以容纳与 s 中一样多的元素
	auto newdata = alloc_n_copy(s.begin(), s.end());
	elements = newdata.first;
	first_free = cap = newdata.second;
}

StrVec::~StrVec() { free(); }

StrVec &StrVec::operator=(const StrVec &rhs) {
	// 调用 alloc_n_copy分配内存,大小与rhs中元素占用空间一样多
	auto data = alloc_n_copy(rhs.begin(), rhs.end());
	free();
	elements = data.first;
	first_free = cap = data.second;
	return *this;
}

void StrVec::reallocate() {
	// 我们将分配当前大小两倍的内存空间
	auto newcapacity = size() ? 2 * size() : 1;
	// 分配新内存
	auto newdata = alloc.allocate(newcapacity);
	// 将数据从旧内存移动到新内存
	auto dest = newdata; // 指向新数组中下一个空闲位置
	auto elem = elements; // 指向旧数组中下一个元素
	for (size_t i = 0; i != size(); ++i) {
		alloc.construct(dest++, move(*elem++));
	}
	free(); // 一旦我们移动完元素就释放旧内存空间
	// 更新我们的数据结构，执行新元素
	elements = newdata;
	first_free = dest;
	cap = elements + newcapacity;
}


int main() {
	StrVec v;
	v.push_back("Hello");
	v.push_back("World");
	for (auto i = v.begin(); i != v.end(); ++i) {
		cout << *i << endl;
	}

	system("pause");
	return 0;
}
```
## 练习 19.3：
### 已知存在如下的类继承体系，其中每个类分别定义了一个公有的默认构造函数和一个虚析构函数：
```
class A { /* . . . */ };
class B : public A { /* . . . */ };
class C : public B { /* . . . */ };
class D : public B, public A { /* . . . */ };
```
### 下面的哪个dynamic_cast将失败？
```
(a) A *pa = new C;
 B *pb = dynamic_cast< B* >(pa);
(b) B *pb = new B;
 C *pc = dynamic_cast< C* >(pb);
(c) A *pa = new D;
 B *pb = dynamic_cast< B* >(pa);
```
答：
* (a) 成功转换
* (b) 将失败。
* (c) dynamic_cast语句可以成功转换，但是A *pa = new D;赋值语句会因为二义性问题而导致编译错误。
## 练习 19.4：
### 使用上一个练习定义的类改写下面的代码,将表达式*pa转换成类型C&：
```
if (C *pc = dynamic_cast< C* >(pa))
 // 使用 c 的成员
} else {
 // 使用 A 的成员
}
```
答：
```
	try {
		C& rc = dynamic_cast<C&>(*pa);
		// 使用C的成员
	} catch (const std::bad_cast &e) {
		std::cout << e.what() << std::endl;
	}
```
## 练习 19.5：
### 在什么情况下你应该使用dynamic_cast替代虚函数？
答：
* 想使用基类对象的指针或引用执行某个派生类操作并且该操作不是虚函数时。
## 练习 19.6：
### 编写一条表达式将Query_base指针动态转换为AndQuery指针(参见15.9.1节,第564页)。 分别使用AndQuery的对象以及其他类型的对象测试转换是否有效。 打印一条表示类型转换是否成功的信息,确保实际输出的结果与期望的一致。
答：
```
	Query_base* Query_basePtr = new AndQuery({ "" }, { "" }); // 实际指向AndQuery对象的Query_base类型指针
	// 尝试将基类Query_base指针转换为派生类AndQuery的指针
	AndQuery* AndQueryPtr = dynamic_cast<AndQuery*>(Query_basePtr);
	if (AndQueryPtr) {
		std::cout << "Conversion to AndQuery succeed.\n";
	} else {
		std::cout << "Conversion to AndQuery failed.\n";
	}

	Query_base* Query_basePtr_2 = new OrQuery({ "" }, { "" }); // 实际指向OrQuery对象的Query_base类型指针
	// 尝试将基类Query_base指针转换为派生类AndQuery的指针
	AndQuery* AndQueryPtr_2 = dynamic_cast<AndQuery*>(Query_basePtr_2);
	if (AndQueryPtr_2) {
		std::cout << "Conversion to AndQuery succeed.\n";
	} else {
		std::cout << "Conversion to AndQuery failed.\n";
	}
```
## 练习 19.7：
### 编写与上一个练习类似的转换,这一次将Query_base对象转换为AndQuery的引用。重复上面的测试过程,确保转换能正常工作。
答：
```
	Query_base* Query_basePtr = new AndQuery({ "" }, { "" }); // 实际指向AndQuery对象的Query_base类型指针
	try {
		AndQuery& AndQueryRef = dynamic_cast<AndQuery&>(*Query_basePtr);
		std::cout << "Conversion to AndQuery succeed.\n";
	} catch (const std::bad_cast&) {
		std::cout << "Conversion to AndQuery failed.\n";
	}

	Query_base* Query_basePtr_2 = new OrQuery({ "" }, { "" }); // 实际指向OrQuery对象的Query_base类型指针
	try {
		AndQuery& AndQueryRef = dynamic_cast<AndQuery&>(*Query_basePtr_2);
		std::cout << "Conversion to AndQuery succeed.\n";
	} catch (const std::bad_cast&) {
		std::cout << "Conversion to AndQuery failed.\n";
	}
```
## 练习 19.8：
### 编写一条typeid表达式检查两个Query_base对象是否指向同一种类型。 再检查该类型是否是 AndQuery。
答：
```
	Query_base* Query_basePtr = new AndQuery({ "" }, { "" });
	Query_base* Query_basePtr_2 = new AndQuery({ "" }, { "" });
	if (typeid(*Query_basePtr) != typeid(*Query_basePtr_2)) {
		std::cout << "Does not refer to the same type.\n";
	} else if(typeid(*Query_basePtr)== typeid(AndQuery)){
		std::cout << "The type is AndQuery.\n";
	} else {
		std::cout << "refer to the same type, but the type is not AndQuery.\n";
	}
```
## 练习 19.9：
### 编写与本节最后一个程序类似的代码,令其打印你的编译器为一些常见类型所起的名字。 如果你得到的输出结果与本书类似,尝试编写一个函数将这些字符串翻译成人们更容易读懂的形式。
答：
```
int arr[10];
	Derived d;
	Base *p = &d;
	std::cout << typeid(42).name() << ", "
		<< typeid(arr).name() << ", "
		<< typeid(Sales_data).name() << ", "
		<< typeid(std::string).name() << ", " 
		<< typeid(p).name() << ", " 
		<< typeid(*p).name() << std::endl;
```
* 输出结果：
* int, int \[10], struct Sales_data, class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >, class Base * __ptr64, class Derived
## 练习 19.10：
### 已知存在如下的类继承体系，其中每个类定义了一个默认公有的构造函数和一个虚析构函数。下面的语句将打印哪些类型名字？
```
class A { /* . . . */ };
class B : public A { /* . . . */ };
class C : public B { /* . . . */ };
(a) A *pa = new C;
 cout << typeid(pa).name() << endl;
(b) C cobj;
 A& ra = cobj; cout << typeid(&ra).name() << endl;
(c) B *px = new B;
 A& ra = *px; cout << typeid(ra).name() << endl;
```
答：
* (a) A*
* (b) A*
* (c) B
## 练习 19.11：
### 普通的数据指针与指向数据成员的指针有何区别？
答：
* 普通的数据指针直接指向具体的数据，可以直接通过解引用（*ptr）访问或修改指向的数据；而指向数据成员的指针需要与特定的类对象一起使用，通过对象.*指针或对象指针->\*指针来访问或修改数据成员。
## 练习 19.12：
### 定义一个成员指针,令其可以指向screen类的cursor成员。通过该指针获得Screen::cursor的值。
答：
```
class Screen {
public:
	typedef std::string::size_type pos;
	// 添加的成员函数，用于返回指向cursor成员的指针
	static pos Screen::* getCursorPtr() {
		return &Screen::cursor;
	}
private:
	pos cursor;
};

int main() {
	Screen::pos Screen::* cursorPtr = Screen::getCursorPtr();

	return 0;
}
```
## 练习 19.13：
### 定义一个类型,使其可以表示指向Sales_data类的bookNo成员的指针。
答：
```
std::string Sales_data::* bookNoPtr;
```
## 练习 19.14：
### 下面的代码合法吗?如果合法,代码的含义是什么?如果不合法,解释原因。
```
auto pmf = &Screen::get_cursor;
pmf = &Screen::get;
```
答：
* 这段代码是合法的。这两个成员函数get_cursor和get都有相同的函数类型，代码首先使用auto关键字自动推导pmf的类型，并将其初始化为指向Screen::get_cursor的成员函数指针，然后被重新赋值为指向get。
## 练习 19.15：
### 普通函数指针和指向成员函数的指针有何区别？
答：
* 普通函数指针是指向全局函数或静态函数的指针。
* 指向成员函数的指针是指向类的非静态成员函数的指针。
## 练习 19.16：
### 声明一个类型别名,令其作为指向sales_data的avg_price成员的指针的同义词。
答：
* using Action = double (Sales_data::*)()const;
## 练习 19.17：
### 为Screen的所有成员函数类型各定义一个类型别名。
答：
```
	// 为get_cursor函数定义类型别名
	using GetCursorAction = char (Screen::*)() const;
	// 为get函数定义类型别名
	using GetAction = char (Screen::*)() const;
	// 为get(pos, pos)函数定义类型别名
	using GetPosAction = char (Screen::*)(pos, pos) const;
```
