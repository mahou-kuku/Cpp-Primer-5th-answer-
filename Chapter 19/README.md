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
