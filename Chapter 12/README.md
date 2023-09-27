## 练习12.1：
### 在此代码的结尾， b1 和 b2 各包含多少个元素？
```
StrBlob b1;
{
 StrBlob b2 = {"a", "an", "the"};
 b1 = b2;
 b2.push_back("about");
}
```
答：
* b1 包含4个元素，b2 离开作用域后已经被销毁。
## 练习 12.2:
### 编写你自己的 StrBlob 类，包含 const 版本的 front 和 back。
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <initializer_list>
#include <memory>

using namespace std;

class StrBlob {
public:
	typedef vector<string>::size_type size_type;

	StrBlob() : data(make_shared<vector<string>>()) { }
	StrBlob(initializer_list<string> il) : data(make_shared<vector<string>>(il)) { }
	size_type size() const { return data->size(); }
	bool empty() const { return data->empty(); }
	// 添加和删除元素
	void push_back(const string &t) { data->push_back(t); }
	void pop_back() {
		check(0, "pop_back on empty StrBlob");
		data->pop_back();
	}
	// 元素访问
	string& front() {
		// 如果 vector 为空，check 会抛出一个异常
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back() {
		check(0, "back on empty StrBlob");
		return data->back();
	}
	string& front()const {
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back()const {
		check(0, "back on empty StrBlob");
		return data->back();
	}
private:
	shared_ptr<vector<string>> data;
	// 如果 data[i]不合法，抛出一个异常
	void check(size_type i, const string &msg) const {
		if (i >= data->size()) {
			throw out_of_range(msg);
		}
	}
};

int main() {
	StrBlob b1;
	{
		StrBlob b2 = { "a", "an", "the" };
		b1 = b2;
		b2.push_back("about");
	}
	cout << b1.back() << endl;

	return 0;
}
```
## 练习 12.3:
### StrBlob需要const版本的push_back和pop_back吗?如果需要,添加进去。 否则，解释为什么不需要。
答：
* 从语法和编译角度，可以把push_back和pop_back写成const版本，因为这些操作并不修改shared_ptr对象本身。但从设计和逻辑的角度看，这样的做法是违反直觉的。
* 虽然shared_ptr的内部指针值没有变化，但它所指向的vector的内容确实被修改了。这意味着StrBlob对象的逻辑状态已经改变。所以，从设计的角度来看，给push_back和pop_back添加const标识会导致误导。
## 练习 12.4:
### 在我们的check函数中,没有检查i是否大于0。为什么可以忽略这个检查？
答：
* std::vector<std::string>::size_type 是无符号整型，取值范围是0和正数，不可能出现负数。
## 练习 12.5：
### 我们未编写接受一个 initializer_list explicit （参见 7.5.4 节，第 264页)参数的构造函数。讨论这个设计策略的优点和缺点。
答：
* 优点：
可以直接传递初始化列表给接受StrBlob参数的函数，由于StrBlob基本上是一个对字符串vector的封装，所以允许直接使用初始化列表来构造这样一个对象是直观的。
* 缺点：
可能导致意外的类型转换，由于该构造函数不是explicit的，编译器可能会在我们不期望的情况下隐式地进行类型转换。这可能会导致一些意料之外的行为。
## 练习 12.6:
### 编写函数,返回一个动态分配的int的vector。将此vector传递给另一个函数,这个函数读取标准输入，将读入的值保存在vector元素中。再将vector 传递给另一个函数，打印读入的值。记得在恰当的时刻 delete vector。
答：
```
#include <iostream>
#include <vector>

// 创建并返回一个动态分配的vector
std::vector<int>* create_vector() {
	return new std::vector<int>();
}

// 从标准输入读取int值并存储到vector中
void read_values(std::vector<int> *vec) {
	int val;
	while (std::cin >> val) {
		vec->push_back(val);
	}
}

// 打印vector中的所有int值
void print_values(std::vector<int> *vec) {
	for (const auto &val : *vec) {
		std::cout << val << " ";
	}
	std::cout << std::endl;
}

int main() {
	auto vec_ptr = create_vector();
	read_values(vec_ptr);
	print_values(vec_ptr);

	delete vec_ptr;
	return 0;
}
```
## 练习 12.7：
### 重做上一题，这次使用 shared_ptr 而不是内置指针。
答：
```
#include <iostream>
#include <vector>
#include <memory>

// 创建并返回一个动态分配的vector
std::shared_ptr<std::vector<int>> create_vector() {
	return std::make_shared<std::vector<int>>();
}

// 从标准输入读取int值并存储到vector中
void read_values(std::shared_ptr<std::vector<int>> vec) {
	int val;
	while (std::cin >> val) {
		vec->push_back(val);
	}
}

// 打印vector中的所有int值
void print_values(const std::shared_ptr<std::vector<int>> &vec) {
	for (const auto &val : *vec) {
		std::cout << val << " ";
	}
	std::cout << std::endl;
}

int main() {
	auto vec_ptr = create_vector();
	read_values(vec_ptr);
	print_values(vec_ptr);

	// 使用shared_ptr，不需要显式地delete vector
	return 0;
}
```
## 练习 12.8:
### 下面的函数是否有错误?如果有,解释错误原因。
```
bool b() {
  int* p = new int;
  // ...
  return p;
}
```
答：
* 这个函数的问题在于它分配了内存，但却没有释放这块内存。并且由于返回的是一个布尔值，调用者也无法访问和释放这块内存。因此，这会导致内存泄漏。
## 练习 12.9:
### 解释下面代码执行的结果:
```
int *q = new int(42), *r = new int(100);
r = q;
auto q2 = make_shared<int>(42), r2 = make_shared<int>(100);
r2 = q2;
```
答：
* 使用内置指针时，r = q;导致内存泄漏。
* 使用shared_ptr时，r2 = q2;没有导致内存泄漏，因为智能指针会自动管理内存。
## 练习 12.10:
### 下面的代码调用了第413页中定义的process函数,解释此调用是否正确。如果不正确,应如何修改?
```
shared_ptr<int> p(new int(42));
process(shared_ptr<int>(p));
```
答：
* 正确，只是会额外生成一个临时的智能指针。
## 练习 12.11：
### 如果我们像下面这样调用 process，会发生什么？
```
process(shared_ptr<int>(p.get()));
```
答：
* 当process函数执行结束时，shared_ptr<int>(p.get())创建的临时shared_ptr对象和局部shared_ptr对象 ptr 都已被销毁，导致int的内存被释放，使p成为悬空的智能指针，对p的使用是未定义的行为。
* 当p在其作用域结束时被销毁，它也会试图释放int的内存，这也会导致未定义的行为，
## 练习 12.12:
### p和q的定义如下，对于接下来的对process的每个调用，如果合法，解释它做了什么，如果不合法，解释错误原因：
```
auto p = new int();
auto sp = make_shared<int>();
(a) process(sp);
(b) process(new int());
(c) process(p);
(d) process(shared_ptr<int>(p));
```
答：
* (a) 合法。向process传递了一个智能指针，智能指针会自动管理内存。
* (b) 不合法，智能指针接受指针参数的构造函数是explicit的，不能将一个内置指针隐式转换为一个智能指针。
* (c) 不合法，智能指针接受指针参数的构造函数是explicit的，不能将一个内置指针隐式转换为一个智能指针。
* (d) 合法。但p会成为一个悬空的指针。
## 练习 12.13：
### 如果执行下面的代码，会发生什么？
```
auto sp = make_shared<int>();
auto p = sp.get();
delete p;
```
答：
* sp会成为悬空的智能指针，对sp的使用是未定义的行为。sp的生命周期结束时会第二次释放内存导致未定义的行为。
## 练习 12.14:
### 编写你自己版本的用shared_ptr 管理 connection 的函数。
答：
```
struct destination; 
struct connection; 
connection connect(destination*); 
void disconnect(connection); 
void end_connection(connection *p) { disconnect(*p); }
void f(destination &d){
	connection c = connect(&d); 
	shared_ptr<connection> p(&c, end_connection);

}
```
## 练习 12.15：
### 重写第一题的程序，用 lambda（参见 10.3.2 节，第346 页）代替 end_connection 函数。
答：
```
void f(destination &d){
	connection c = connect(&d); 
	shared_ptr<connection> p(&c, [](connection *p) { disconnect(*p); });
}
```
## 练习 12.16:
### 如果你试图拷贝或赋值unique_ptr,编译器并不总是能给出易于理解的错误信息。编写包含这种错误的程序,观察编译器如何诊断这种错误。
答：
```
// unique_ptr的拷贝构造函数和拷贝赋值操作符都被删除了，所以不支持这两种操作。但是unique_ptr提供了移动构造函数和移动赋值操作符。
	int *p = new int(9);
	unique_ptr<int> up(p);
	unique_ptr<int> up2;
	unique_ptr<int> up3(up);	// 尝试引用已删除的函数
	up2 = up;	// 尝试引用已删除的函数
	up2= unique_ptr<int>(p);	// 临时unique_ptr是一个右值，编译器会以“移动”的方式构造、赋值。
```
## 练习 12.17：
### 下面的 unique_ptr 声明中，哪些是合法的，哪些可能导致后续的程序错误？解释每个错误的问题在哪里。
```
int ix = 1024, *pi = &ix, *pi2 = new int(2048);
typedef unique_ptr<int> IntP;
(a) IntP p0(ix);
(b) IntP p1(pi);
(c) IntP p2(pi2);
(d) IntP p3(&ix);
(e) IntP p4(new int(2048));
(f) IntP p5(p2.get());
```
答：
* (a) 不合法。unique_ptr需要一个指向动态分配对象的指针，而不是一个整数。
* (b) 可能导致后续的程序错误，当p1超出作用域时，它会尝试删除它所指向的对象，但pi指向的是栈上的变量ix，不是动态分配的对象，所以删除它会导致未定义的行为。
* (c) 合法。
* (d) 可能导致后续的程序错误，这与(b)中的问题相似。
* (e) 合法。
* (f) 可能导致后续的程序错误，两个unique_ptr指向同一个对象。当其中一个unique_ptr超出作用域时，它会删除所指向的对象。当另一个unique_ptr超出作用域时，它也会尝试删除同一个对象，导致二次删除，从而引发未定义的行为。
## 练习 12.18：
### shared ptr 为什么没有 release 成员？
答：
* shared_ptr 提供了一个强大的共享所有权模型，而没有 release 方法可以确保资源在所有相关的 shared_ptr 对象之间正确且安全地管理。如果需要一个可以 release 的智能指针，unique_ptr 更适合这个目的。
## 练习 12.19:
### 定义你自己版本的strBlobPtr,更新strBlob类,加入恰当的friend 声明及 begin 和 end 成员。
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <initializer_list>
#include <memory>

using namespace std;

// 对于 StrBlob 中的友元声明来说，此前置声明是必要的
class StrBlobPtr;

class StrBlob {
public:
	friend class StrBlobPtr;
	typedef vector<string>::size_type size_type;

	StrBlob() : data(make_shared<vector<string>>()) { }
	StrBlob(initializer_list<string> il) : data(make_shared<vector<string>>(il)) { }
	size_type size() const { return data->size(); }
	bool empty() const { return data->empty(); }
	// 添加和删除元素
	void push_back(const string &t) { data->push_back(t); }
	void pop_back() {
		check(0, "pop_back on empty StrBlob");
		data->pop_back();
	}
	// 元素访问
	string& front() {
		// 如果 vector 为空，check 会抛出一个异常
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back() {
		check(0, "back on empty StrBlob");
		return data->back();
	}
	string& front()const {
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back()const {
		check(0, "back on empty StrBlob");
		return data->back();
	}
	// 返回指向首元素和尾后元素的 StrBlobPtr
	StrBlobPtr begin();
	StrBlobPtr end();
private:
	shared_ptr<vector<string>> data;
	// 如果 data[i]不合法，抛出一个异常
	void check(size_type i, const string &msg) const {
		if (i >= data->size()) {
			throw out_of_range(msg);
		}
	}
};

class StrBlobPtr {
public:
	StrBlobPtr() : curr(0) { }
	StrBlobPtr(StrBlob &a, size_t sz = 0) : wptr(a.data), curr(sz) { }
	std::string& deref() const {
		auto p = check(curr, "dereference past end");
		return (*p)[curr]; //（*p)是对象所指向的 vector
	}
	//前缀递增：返回递增后的对象的引用
	StrBlobPtr& incr() {
		// 如果 curr 已经指向容器的尾后位置，就不能递增它
		check(curr, "increment past end of StrBlobPtr");
		++curr; // 推进当前位置
		return *this;
	}
private:
	// 若检查成功，check 返回一个指向 vector 的 shared_ptr
	std::shared_ptr<std::vector<std::string>> check(std::size_t i, const std::string& msg) const {
		auto ret = wptr.lock(); // vector 还存在吗?
		if (!ret) {
			throw std::runtime_error("unbound StrBlobPtr");
		}
		if (i >= ret->size()) {
			throw std::out_of_range(msg);
		}
		return ret; // 否则，返回指向 vector 的 shared_ptr
	}
	// 保存一个 weak ptr，意味着底层 vector 可能会被销毁
	std::weak_ptr<std::vector<std::string>> wptr;
	std::size_t curr; // 在vector中的当前位置
};

StrBlobPtr StrBlob::begin() { return StrBlobPtr(*this); }
StrBlobPtr StrBlob::end() { return StrBlobPtr(*this, data->size()); }
```
## 练习 12.20:
### 编写程序,逐行读入一个输入文件,将内容存入一个StrBlob中,用一个StrBlobPtr打印出StrBlob中的每个元素。
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <initializer_list>
#include <memory>
#include <fstream>

using namespace std;

// 对于 StrBlob 中的友元声明来说，此前置声明是必要的
class StrBlobPtr;

class StrBlob {
public:
	friend class StrBlobPtr;
	typedef vector<string>::size_type size_type;

	StrBlob() : data(make_shared<vector<string>>()) { }
	StrBlob(initializer_list<string> il) : data(make_shared<vector<string>>(il)) { }
	size_type size() const { return data->size(); }
	bool empty() const { return data->empty(); }
	// 添加和删除元素
	void push_back(const string &t) { data->push_back(t); }
	void pop_back() {
		check(0, "pop_back on empty StrBlob");
		data->pop_back();
	}
	// 元素访问
	string& front() {
		// 如果 vector 为空，check 会抛出一个异常
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back() {
		check(0, "back on empty StrBlob");
		return data->back();
	}
	string& front()const {
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back()const {
		check(0, "back on empty StrBlob");
		return data->back();
	}
	// 返回指向首元素和尾后元素的 StrBlobPtr
	StrBlobPtr begin();
	StrBlobPtr end();
private:
	shared_ptr<vector<string>> data;
	// 如果 data[i]不合法，抛出一个异常
	void check(size_type i, const string &msg) const {
		if (i >= data->size()) {
			throw out_of_range(msg);
		}
	}
};

class StrBlobPtr {
public:
	StrBlobPtr() : curr(0) { }
	StrBlobPtr(StrBlob &a, size_t sz = 0) : wptr(a.data), curr(sz) { }
	std::string& deref() const {
		auto p = check(curr, "dereference past end");
		return (*p)[curr]; //（*p)是对象所指向的 vector
	}
	// 前缀递增：返回递增后的对象的引用
	StrBlobPtr& incr() {
		// 如果 curr 已经指向容器的尾后位置，就不能递增它
		check(curr, "increment past end of StrBlobPtr");
		++curr; // 推进当前位置
		return *this;
	}
	// 重载迭代需要的!=运算符
	bool operator!=(const StrBlobPtr& rhs) {
		return curr != rhs.curr;
	}
private:
	// 若检查成功，check 返回一个指向 vector 的 shared_ptr
	std::shared_ptr<std::vector<std::string>> check(std::size_t i, const std::string& msg) const {
		auto ret = wptr.lock(); // vector 还存在吗?
		if (!ret) {
			throw std::runtime_error("unbound StrBlobPtr");
		}
		if (i >= ret->size()) {
			throw std::out_of_range(msg);
		}
		return ret; // 否则，返回指向 vector 的 shared_ptr
	}
	// 保存一个 weak ptr，意味着底层 vector 可能会被销毁
	std::weak_ptr<std::vector<std::string>> wptr;
	std::size_t curr; // 在vector中的当前位置
};

StrBlobPtr StrBlob::begin() { return StrBlobPtr(*this); }
StrBlobPtr StrBlob::end() { return StrBlobPtr(*this, data->size()); }

void readFileAndPrint(const string& filename) {
	ifstream file(filename);
	if (!file.is_open()) {
		cerr << "Failed to open the file: " << filename << endl;
		return;
	}

	StrBlob blob;
	string line;

	while (getline(file, line)) {
		blob.push_back(line);
	}

	for (StrBlobPtr p = blob.begin(); p != blob.end(); p.incr()) {
		cout << p.deref() << endl;
	}
}
int main() {
	readFileAndPrint("example.txt");

	return 0;
}
```
## 练习 12.21：
### 也可以这样编写 StrBlobPtr 的 deref 成员：
```
std::string& deref() const
{ return (*check(curr, "dereference past end"))[curr]; }
```
### 你认为哪个版本更好？为什么?
答：
* 这个版本的可读性更差。
## 练习 12.22：
### 为了能让StrBlobPtr使用 const StrBlob，你觉得应该如何修改?定义一个名为ConstStrBlobPtr的类,使其能够指向const StrBlob。
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <initializer_list>
#include <memory>

using namespace std;

// 对于 StrBlob 中的友元声明来说，此前置声明是必要的
class ConstStrBlobPtr;

class StrBlob {
public:
	friend class ConstStrBlobPtr;
	typedef vector<string>::size_type size_type;

	StrBlob() : data(make_shared<vector<string>>()) { }
	StrBlob(initializer_list<string> il) : data(make_shared<vector<string>>(il)) { }
	size_type size() const { return data->size(); }
	bool empty() const { return data->empty(); }
	// 添加和删除元素
	void push_back(const string &t) { data->push_back(t); }
	void pop_back() {
		check(0, "pop_back on empty StrBlob");
		data->pop_back();
	}
	// 元素访问
	string& front() {
		// 如果 vector 为空，check 会抛出一个异常
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back() {
		check(0, "back on empty StrBlob");
		return data->back();
	}
	string& front()const {
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back()const {
		check(0, "back on empty StrBlob");
		return data->back();
	}
	// 返回指向首元素和尾后元素的 ConstStrBlobPtr
	ConstStrBlobPtr cbegin()const;
	ConstStrBlobPtr cend()const;
private:
	shared_ptr<vector<string>> data;
	// 如果 data[i]不合法，抛出一个异常
	void check(size_type i, const string &msg) const {
		if (i >= data->size()) {
			throw out_of_range(msg);
		}
	}
};

class ConstStrBlobPtr {
public:
	ConstStrBlobPtr() : curr(0) { }
	ConstStrBlobPtr(const StrBlob &a, size_t sz = 0) : wptr(a.data), curr(sz) { }
	std::string& deref() const {
		auto p = check(curr, "dereference past end");
		return (*p)[curr]; //（*p)是对象所指向的 vector
	}
	// 前缀递增：返回递增后的对象的引用
	ConstStrBlobPtr& incr() {
		// 如果 curr 已经指向容器的尾后位置，就不能递增它
		check(curr, "increment past end of ConstStrBlobPtr");
		++curr; // 推进当前位置
		return *this;
	}
	// 重载迭代需要的!=运算符
	bool operator!=(const ConstStrBlobPtr& rhs) {
		return curr != rhs.curr;
	}
private:
	// 若检查成功，check 返回一个指向 vector 的 shared_ptr
	std::shared_ptr<std::vector<std::string>> check(std::size_t i, const std::string& msg) const {
		auto ret = wptr.lock(); // vector 还存在吗?
		if (!ret) {
			throw std::runtime_error("unbound ConstStrBlobPtr");
		}
		if (i >= ret->size()) {
			throw std::out_of_range(msg);
		}
		return ret; // 否则，返回指向 vector 的 shared_ptr
	}
	// 保存一个 weak ptr，意味着底层 vector 可能会被销毁
	std::weak_ptr<std::vector<std::string>> wptr;
	std::size_t curr; // 在vector中的当前位置
};

ConstStrBlobPtr StrBlob::cbegin() const { return ConstStrBlobPtr(*this); }
ConstStrBlobPtr StrBlob::cend() const { return ConstStrBlobPtr(*this, data->size()); }

int main() {
	StrBlob const a;
	auto it = a.cbegin();

	return 0;
}
```
## 练习 12.23:
### 编写一个程序,连接两个字符串字面常量,将结果保存在一个动态分配的char数组中。 重写这个程序,连接两个标准库string对象。
答：
```
// 使用字符数组：
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstring>

int main() {
	const char* str1 = "Hello, ";
	const char* str2 = "World!";
	char* result = new char[strlen(str1) + strlen(str2) + 1];  // +1 为 '\0' 结束符

	strcpy(result, str1);
	strcat(result, str2);

	std::cout << result << std::endl;

	delete[] result;

	return 0;
}

// 使用标准库的string对象：
#include <iostream>
#include <string>

int main() {
    std::string s1 = "Hello, ";
    std::string s2 = "World!";
    std::string result = s1 + s2;

    std::cout << result << std::endl;

    return 0;
}
```
## 练习 12.24：
### 编写一个程序，从标准输入读取一个字符串，存入一个动态分配的字符数组中。描述你的程序如何处理变长输入。测试你的程序,输入一个超出你分配的数组长度的字符串。
答：
```
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstring>

int main() {
	const size_t BUFFER_SIZE = 10; // 每次从输入读取的字符数
	char buffer[BUFFER_SIZE];

	char* input = nullptr;
	size_t totalSize = 0;

	while (std::cin.read(buffer, BUFFER_SIZE - 1)) {
		size_t bytesRead = std::cin.gcount();
		buffer[bytesRead] = '\0'; // 添加终止字符

		char* newInput = new char[totalSize + bytesRead + 1];
		std::strcpy(newInput, input ? input : ""); // 复制旧的内容
		std::strcat(newInput, buffer); // 连接新读取的内容

		delete[] input;
		input = newInput;
		totalSize += bytesRead;
	}

	if (std::cin.eof()) {
		size_t bytesRead = std::cin.gcount();
		buffer[bytesRead] = '\0';

		char* newInput = new char[totalSize + bytesRead + 1];
		std::strcpy(newInput, input ? input : "");
		std::strcat(newInput, buffer);

		delete[] input;
		input = newInput;
	}

	std::cout << "You entered: " << input << std::endl;

	delete[] input;
	return 0;
}
```
## 练习 12.25：
### 给定下面的 new 表达式，你应该如何释放 pa?
```
int *pa = new int[10];
```
答：
* delete[] pa;
## 练习 12.26:
### 用allocator重写第427页中的程序。
答：
```
#include <iostream>
#include <string>
#include <memory>

using namespace std;

int main() {
	const size_t allocSize = 9;
	allocator<string> alloc;
	string* p = alloc.allocate(allocSize);
	string s;
	string* q = p;
	while (q != p + allocSize && cin >> s) {
		alloc.construct(q++, s);
	}
	const size_t inputCount = q - p;

	while (q != p) {
		alloc.destroy(--q);
	}
	alloc.deallocate(p, allocSize);

	return 0;
}
```
## 练习 12.27：
### TextQuery 和 QueryResult 类只使用了我们已经介绍过的语言和标准库特性。不要提前看后续章节内容，只用已经学到的知识对这两个类编写你自己的版本。
答：
```
#include <iostream>
#include <fstream>
#include <sstream>
#include <vector>
#include <map>
#include <set>
#include <string>
#include <memory>

using namespace std;

class QueryResult; // 前向声明
class TextQuery {
public:
	using line_no = vector<string>::size_type;
	TextQuery(ifstream&);
	QueryResult query(const string&) const;
private:
	shared_ptr<vector<string>> file; // 输入文件
	// 每个单词到它所在的行号的集合的映射
	map<string, shared_ptr<set<line_no>>> wm;
};

class QueryResult {
	friend std::ostream& print(std::ostream&, const QueryResult&);
public:
	QueryResult(string s, shared_ptr<set<TextQuery::line_no>> p,
		shared_ptr<vector<string>> f) : sought(s), lines(p), file(f) { }
private:
	string sought; // 查询单词
	shared_ptr<set<TextQuery::line_no>> lines; // 出现的行号
	shared_ptr<vector<string>> file; // 输入文件
};

// 读取输入文件并建立单词到行号的映射
TextQuery::TextQuery(ifstream &is) : file(new vector<string>){
	string text;
	while (getline(is, text)) { // 对文件中每一行
		file->push_back(text); // 保存此行文本
		int n = file->size() - 1; // 当前行号
		istringstream line(text); // 将行文本分解为单词
		string word;
		while (line >> word) { // 对行中每个单词
			// 如果单词不在 wm中，以之为下标在wm中添加一项
			auto &lines = wm[word]; // lines 是一个 shared_ptr
			if (!lines) // 在我们第一次遇到这个单词时，此指针为空
				lines.reset(new set<line_no>); // 分配一个新的 set
			lines->insert(n); // 将此行号插入 set 中
		}
	}
}

QueryResult TextQuery::query(const string &sought) const{
	// 如果未找到 sought，我们将返回一个指向此 set 的指针
	static shared_ptr<set<line_no>> nodata(new set<line_no>);
	// 使用 find 而不是下标运算符来查找单词，避免将单词添加到 wm 中！
	auto loc = wm.find(sought);
	if (loc == wm.end()) {
		return QueryResult(sought, nodata, file); // 未找到
	} else {
		return QueryResult(sought, loc->second, file);
	}
}

ostream &print(ostream & os, const QueryResult &qr){
	// 如果找到了单词,打印出现次数和所有出现的位置
	os << qr.sought << " occurs " << qr.lines->size() << " " << "times" << endl;
	// 打印单词出现的每一行
	for (auto num : *qr.lines) { // 对 set 中每个单词
		// 避免行号从 0 开始给用户带来的困惑
		os << "\t(line " << num + 1 << ") " << *(qr.file->begin() + num) << endl;
	}
	return os;
}
```
## 练习 12.28:
### 编写程序实现文本查询,不要定义类来管理数据。你的程序应该接受一个文件，并与用户交互来查询单词。使用 vector、 map 和 set 容器来保存来自文件的数据并生成查询结果。
答：
```
#include <iostream>
#include <fstream>
#include <sstream>
#include <vector>
#include <map>
#include <set>
#include <string>

using namespace std;

void queryWord(const vector<string>& lines, const map<string, set<int>>& wordMap) {
	cout << "Enter the word you want to query: ";
	string word;
	cin >> word;

	auto it = wordMap.find(word);
	if (it != wordMap.end()) {
		cout << word << " occurs " << it->second.size() << " times." << endl;
		for (auto lineNo : it->second) {
			cout << "\t(line " << lineNo + 1 << ") " << lines[lineNo] << endl;
		}
	} else {
		cout << word << " does not occur." << endl;
	}
}

int main() {
	ifstream file;
	string filename;
	cout << "Enter the filename: ";
	cin >> filename;

	file.open(filename);
	if (!file) {
		cerr << "Failed to open the file." << endl;
		return -1;
	}

	vector<string> lines;
	map<string, set<int>> wordMap;
	string line;
	int lineNo = 0;

	while (getline(file, line)) {
		lines.push_back(line);
		istringstream lineStream(line);
		string word;

		set<string> uniqueWordsInLine; // 每行只计算一个单词一次
		while (lineStream >> word) {
			uniqueWordsInLine.insert(word);
		}

		for (const auto& uniqueWord : uniqueWordsInLine) {
			wordMap[uniqueWord].insert(lineNo);
		}

		++lineNo;
	}
	file.close();

	// 与用户交互
	char ch;
	do {
		queryWord(lines, wordMap);
		cout << "Do you want to query another word? (y/n) ";
		cin >> ch;
	} while (ch != 'n');

	return 0;
}
```
## 练习 12.29：
### 我们曾经用 do while 循环来编写管理用户交互的循环（参见 5.4.4 节，第169页)。用do while重写本节程序,解释你倾向于哪个版本,为什么。
答：
```
void runQueries(ifstream &infile)
{
	TextQuery tq(infile);
	string s;
	do {
		cout << "enter word to look for, or q to quit: ";
		if (!(cin >> s)) break;
		if (s == "q") break;
		print(cout, tq.query(s)) << endl;
	} while (true);
}
```
* 我倾向于原始的while版本，因为它更加明确和直观。
## 练习 12.30:
### 定义你自己版本的TextQuery和QueryResult类,并执行12.3.1节(第 431页)中的runQueries函数。
答：
```
void runQueries(ifstream &infile){
	// infile是一个ifstream,指向我们要处理的文件
	TextQuery tq(infile); // 保存文件并建立查询 map
	// 与用户交互：提示用户输入要查询的单词，完成查询并打印结果
	while (true) {
		cout << "enter word to look for, or q to quit: ";
		string s;
		// 若遇到文件尾或用户输入了'q' 时循环终止
		if (!(cin >> s) || s == "q") break;
		// 指向查询并打印结果
		print(cout, tq.query(s)) << endl;
	}
}

int main() {
	ifstream ifs("example.txt");
	runQueries(ifs);

	return 0;
}
```
## 练习 12.31：
### 如果用 vector 代替 set 保存行号，会有什么差别？哪种方法更好？为什么？
答：
* 如果单词在一行内多次出现，vector会存储多个相同的行号，违反了设计要求的“行号无重复”。set 更好，不需要额外的去重复设计。
## 练习 12.32:
### 重写TextQuery和QueryResult类,用StrBlob代替vector<string> 保存输入文件。
答：
```
class QueryResult; // 前向声明
class TextQuery {
public:
	using line_no = vector<string>::size_type;
	TextQuery(ifstream&);
	QueryResult query(const string&) const;
private:
	StrBlob file; // 输入文件
									 // 每个单词到它所在的行号的集合的映射
	map<string, shared_ptr<set<line_no>>> wm;
};

class QueryResult {
	friend std::ostream& print(ostream&, const QueryResult&);
public:
	QueryResult(string s, shared_ptr<set<TextQuery::line_no>> p,
		StrBlob f) : sought(s), lines(p), file(f) { }
private:
	string sought; // 查询单词
	shared_ptr<set<TextQuery::line_no>> lines; // 出现的行号
	StrBlob file; // 输入文件
};

// 读取输入文件并建立单词到行号的映射
TextQuery::TextQuery(ifstream &is)  {
	string text;
	while (getline(is, text)) { // 对文件中每一行
		file.push_back(text); // 保存此行文本
		int n = file.size() - 1; // 当前行号
		istringstream line(text); // 将行文本分解为单词
		string word;
		while (line >> word) { // 对行中每个单词
							   // 如果单词不在 wm中，以之为下标在wm中添加一项
			auto &lines = wm[word]; // lines 是一个 shared_ptr
			if (!lines) // 在我们第一次遇到这个单词时，此指针为空
				lines.reset(new set<line_no>); // 分配一个新的 set
			lines->insert(n); // 将此行号插入 set 中
		}
	}
}

QueryResult TextQuery::query(const string &sought) const {
	// 如果未找到 sought，我们将返回一个指向此 set 的指针
	static shared_ptr<set<line_no>> nodata(new set<line_no>);
	// 使用 find 而不是下标运算符来查找单词，避免将单词添加到 wm 中！
	auto loc = wm.find(sought);
	if (loc == wm.end()) {
		return QueryResult(sought, nodata, file); // 未找到
	} else {
		return QueryResult(sought, loc->second, file);
	}
}


ostream &print(ostream & os, const QueryResult &qr) {
	// 如果找到了单词,打印出现次数和所有出现的位置
	os << qr.sought << " occurs " << qr.lines->size() << " " << "times" << endl;
	// 打印单词出现的每一行
	for (auto num : *qr.lines) { // 对 set 中每个单词
		auto p = qr.file.cbegin();
								 // 避免行号从 0 开始给用户带来的困惑
		os << "\t(line " << num + 1 << ") " << p.deref() << endl;
	}
	return os;
}
```
## 练习 12.33:
### 在第15章中我们将扩展查询系统,在QueryResult类中将会需要一些额外的成员。添加名为begin和end的成员,返回一个迭代器,指向一个给定查询返回的行号的set中的位置。再添加一个名为get_file的成员,返回一个shared_ptr, 指向QueryResult对象中的文件。
答：
```
class QueryResult {
	friend std::ostream& print(std::ostream&, const QueryResult&);
public:
	QueryResult(string s, shared_ptr<set<TextQuery::line_no>> p,
		shared_ptr<vector<string>> f) : sought(s), lines(p), file(f) { }
	set<TextQuery::line_no>::iterator begin() const { return lines->begin(); }
	set<TextQuery::line_no>::iterator end() const { return lines->end(); }
	shared_ptr<vector<string>> get_file() const { return file; }
private:
	string sought; // 查询单词
	shared_ptr<set<TextQuery::line_no>> lines; // 出现的行号
	shared_ptr<vector<string>> file; // 输入文件
};
```
