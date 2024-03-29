## 练习 16.1：
### 给出实例化的定义。
答：
* C++编译器会根据函数模板的定义和提供的实参类型来实例化一个具体的函数版本。
## 练习 16.2：
### 编写并测试你自己版本的 compare 函数。
答：
```
template <typename T>
int compare(const T &v1, const T &v2){
	if (v1 < v2) return -1;
	if (v2 < v1) return 1;
	return 0;
}
```
## 练习 16.3:
### 对两个Sales_data对象调用你的compare函数,观察编译器在实例化过程中如何处理错误。
答：
* error C2678: 二进制“<”: 没有找到接受“const Sales_data”类型的左操作数的运算符(或没有可接受的转换)
## 练习 16.4:
### 编写行为类似标准库find算法的模板。函数需要两个模板类型参数,一个表示函数的迭代器参数,另一个表示值的类型。使用你的函数在一个vector<int>和一个list<string>中查找给定值。
答：
```
#include <iostream>
#include <vector>
#include <list>
#include <string>


// find模板函数
template <typename Iterator, typename Value>
Iterator find(Iterator first, Iterator last, const Value& val) {
	while (first != last) {
		if (*first == val) {
			return first;
		}
		++first;
	}
	return last;
}

int main() {
	// 在vector<int>中查找
	std::vector<int> v = { 1, 2, 3, 4, 5 };
	auto it_v = ::find(v.begin(), v.end(), 3);
	if (it_v != v.end()) {
		std::cout << "Found in vector: " << *it_v << std::endl;
	} else {
		std::cout << "Not found in vector." << std::endl;
	}

	// 在list<string>中查找
	std::list<std::string> l = { "hello", "world", "test", "find" };
	auto it_l = ::find(l.begin(), l.end(), std::string("test"));
	if (it_l != l.end()) {
		std::cout << "Found in list: " << *it_l << std::endl;
	} else {
		std::cout << "Not found in list." << std::endl;
	}

	return 0;
}
```
## 练习 16.5:
### 为6.2.4节(第195页)中的print 函数编写模板版本，它接受一个数组的引用,能处理任意大小、任意元素类型的数组。
答：
```
#include <iostream>
#include <string>

// 模板函数，接受任意大小、任意元素类型的数组引用
template <typename T, size_t N>
void print(const T(&arr)[N]) {
	for (const auto& elem : arr) {
		std::cout << elem << std::endl;
	}
}

int main() {
	int arrInt[] = { 1, 2, 3, 4, 5 };
	print(arrInt); // 打印int数组

	std::string arrStr[] = { "hello", "world", "template" };
	print(arrStr); // 打印string数组

	return 0;
}
```
## 练习 16.6：
### 你认为接受一个数组实参的标准库函数 begin 和 end 是如何工作的？定义你自己版本的 begin 和 end。
答：
```
// 自定义 begin 函数
template <typename T, size_t N>
T* begin(T(&arr)[N]) {
	return &arr[0];
}

// 自定义 end 函数
template <typename T, size_t N>
T* end(T(&arr)[N]) {
	return &arr[N];
}
```
## 练习 16.7:
### 编写一个constexpr模板,返回给定数组的大小。
答：
```
// constexpr模板函数，用于计算数组大小
template <typename T, size_t N>
constexpr size_t arraySize(const T(&)[N]) {
	return N;
}
```
## 练习 16.8:
### 在第97页的“关键概念”中,我们注意到, C++程序员喜欢使用!=而不喜欢<。解释这个习惯的原因。
答：
* 因为模板编程广泛使用迭代器，而迭代器并不总是支持 < 操作符。如 std::list、std::forward_list、std::unordered_map 等迭代器是双向迭代器或前向迭代器，它们不支持 < 操作符。相比之下，== 和 != 操作符适用于所有类型的迭代器。
## 练习 16.9：
### 什么是函数模板？什么是类模板？
答：
* 函数模板是用于生成可以处理不同类型数据的函数的蓝图。
* 类模板是用于生成可以处理不同类型数据的类的蓝图。
## 练习 16.10:
### 当一个类模板被实例化时,会发生什么？
答：
* 当一个类模板被实例化时，编译器根据提供给模板的具体类型参数创建一个新的类类型。这就像是编译器为你写了一个新的类，这个类与原始模板在结构上相同，但所有的模板参数都被替换为了具体的类型。
## 练习 16.11:
### 下面List的定义是错误的。应如何修正它？
```
template <typename elemType> class ListItem;
template <typename elemType> class List {
public:
	List<elemType>(); 
	List<elemType>(const List<elemType> &);
	List<elemType>& operator=(const List<elemType> &);
	~List(); 
	void insert(ListItem *ptr, elemType value);
private:
	ListItem *front, *end;
};
```
答：
```
template <typename elemType> class ListItem;
template <typename elemType>
class List {
public:
	List();
	List(const List<elemType> &);
	List<elemType>& operator=(const List<elemType> &);
	~List();
	void insert(ListItem<elemType> *ptr, elemType value);
private:
	ListItem<elemType> *front, *end;
};
```
## 练习 16.12:
### 编写你自己版本的Blob和BlobPtr模板,包含书中未定义的多个const成员。
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <initializer_list>
#include <memory>

using namespace std;

template <typename T>
class BlobPtr;

template <typename T>
class Blob {
public:
	friend class BlobPtr<T>;
	friend bool operator==<T> (const Blob<T>&, const Blob<T>&);
	friend bool operator!=<T> (const Blob<T>&, const Blob<T>&);
	typedef typename std::vector<T>::size_type size_type;

	Blob() : data(make_shared<vector<T>>()) { }
	Blob(initializer_list<T> il) : data(make_shared<vector<T>>(il)) { }

	size_type size() const { return data->size(); }
	bool empty() const { return data->empty(); }

	// 添加和删除元素
	void push_back(const T& t) { data->push_back(t); }
	void pop_back() {
		check(0, "pop_back on empty Blob");
		data->pop_back();
	}

	// 元素访问
	T& back() {
		check(0, "back on empty Blob");
		return data->back();
	}
	const T& back() const {
		check(0, "back on empty Blob");
		return data->back();
	}

	T& operator[](size_t n) {
		check(n, "subscript out of range");
		return data->at(n);
	}
	const T& operator[](size_t n) const {
		check(n, "subscript out of range");
		return data->at(n);
	}

private:
	shared_ptr<vector<T>> data;
	void check(size_type i, const string &msg) const {
		if (i >= data->size()) {
			throw out_of_range(msg);
		}
	}
};

template <typename T>
bool operator==(const Blob<T>& lhs, const Blob<T>& rhs) {
	return lhs.data == rhs.data;
}
template <typename T>
bool operator!=(const Blob<T>& lhs, const Blob<T>& rhs) {
	return !(lhs == rhs);
}


template <typename T>
class BlobPtr {
public:
	BlobPtr() : curr(0) { }
	BlobPtr(Blob<T> &a, size_t sz = 0) : wptr(a.data), curr(sz) { }

	T& operator*() const {
		auto p = check(curr, "dereference past end");
		return (*p)[curr];
	}

	// 递增和递减
	BlobPtr& operator++() {
		check(curr, "increment past end of BlobPtr");
		++curr;
		return *this;
	}

	BlobPtr& operator--() {
		--curr;
		check(curr, "decrement past begin of BlobPtr");
		return *this;
	}

	BlobPtr operator++(int) {
		BlobPtr ret = *this;
		++*this;
		return ret;
	}

	BlobPtr operator--(int) {
		BlobPtr ret = *this;
		--*this;
		return ret;
	}

private:
	std::shared_ptr<std::vector<T>> check(std::size_t i, const std::string& msg) const {
		auto ret = wptr.lock();
		if (!ret) {
			throw std::runtime_error("unbound BlobPtr");
		}
		if (i >= ret->size()) {
			throw std::out_of_range(msg);
		}
		return ret;
	}

	std::weak_ptr<std::vector<T>> wptr;
	std::size_t curr;
};
```
## 练习 16.13:
### 解释你为BlobPtr的相等和关系运算符选择哪种类型的友好关系？
答：
* 一对一友好关系
## 练习 16.14：
### 编写 Screen 类模板，用非类型参数定义 Screen 的高和宽。
答：
```
#include <iostream>
#include <string>

template <std::string::size_type H, std::string::size_type W>
class Screen {
public:
	typedef std::string::size_type pos;
	Screen() = default;
	Screen(char c) : contents(H * W, c) {}
	char get() const { return contents[cursor]; }
	char get(pos row, pos column) const { return contents[row * W + column]; }
	Screen& move(pos row, pos column) {
		cursor = row * W + column;
		return *this;
	}

private:
	pos cursor = 0;
	std::string contents;
};
```
## 练习 16.15:
### 为你的Screen模板实现输入和输出运算符。Screen类需要哪些友元(如果需要的话)来令输入和输出运算符正确工作？解释每个友元声明（如果有的话)为什么是必要的。
答：
* 需要将输入和输出运算符声明为 Screen 模板类的友元。这种做法是必要的，因为输入和输出操作通常需要直接访问类的私有成员。
```
template <std::string::size_type H, std::string::size_type W>
class Screen {
    // ... 其他成员 ...

    friend std::ostream& operator<<(std::ostream& os, const Screen<H, W>& screen) {
        // 直接访问 Screen 的私有成员 contents
        os << screen.contents;
        return os;
    }
    friend std::istream& operator>>(std::istream& is, Screen<H, W>& screen) {
        std::string input;
        is >> input;
        // 确保输入的长度不超过屏幕的容量
        input.resize(H * W, ' ');  // 如果输入太短，用空格填充
        screen.contents = input;
        return is;
    }

    // ... 其他成员 ...
};
```
## 练习 16.16：
### 将 StrVec 类（参见 13.5 节，第 465 页）重写为模板，命名为 Vec.
答：
```
#define _SCL_SECURE_NO_WARNINGS
#include <string>
#include <memory>

using namespace std;

template <typename T>
class Vec {
public:
	Vec() : elements(nullptr), first_free(nullptr), cap(nullptr) {}
	Vec(const Vec&); // 拷贝构造函数
	Vec &operator=(const Vec&); // 拷贝赋值运算符
	~Vec(); // 析构函数
	void push_back(const T&); // 拷贝元素
	size_t size() const { return first_free - elements; }
	size_t capacity() const { return cap - elements; }
	T *begin() const { return elements; }
	T *end() const { return first_free; }
	void reserve(size_t n);
	void resize(size_t n, const T& t = T());

private:
	std::allocator<T> alloc; // 分配元素
	void chk_n_alloc() { if (size() == capacity()) reallocate(); }
	std::pair<T*, T*> alloc_n_copy(const T*, const T*);
	void free(); // 销毁元素并释放内存
	void reallocate(); // 获得更多内存并拷贝已有元素
	T *elements; // 指向数组首元素的指针
	T *first_free; // 指向数组第一个空闲元素的指针
	T *cap; // 指向数组尾后位置的指针
};

template <typename T>
void Vec<T>::push_back(const T& t) {
	chk_n_alloc();
	alloc.construct(first_free++, t);
}

template <typename T>
std::pair<T*, T*> Vec<T>::alloc_n_copy(const T *b, const T *e) {
	auto data = alloc.allocate(e - b);
	return{ data, std::uninitialized_copy(b, e, data) };
}

template <typename T>
void Vec<T>::free() {
	if (elements) {
		for (auto p = first_free; p != elements; )
			alloc.destroy(--p);
		alloc.deallocate(elements, cap - elements);
	}
}

template <typename T>
Vec<T>::Vec(const Vec &s) {
	auto newdata = alloc_n_copy(s.begin(), s.end());
	elements = newdata.first;
	first_free = cap = newdata.second;
}

template <typename T>
Vec<T>::~Vec() { free(); }

template <typename T>
Vec<T> &Vec<T>::operator=(const Vec &rhs) {
	auto data = alloc_n_copy(rhs.begin(), rhs.end());
	free();
	elements = data.first;
	first_free = cap = data.second;
	return *this;
}

template <typename T>
void Vec<T>::reallocate() {
	auto newcapacity = size() ? 2 * size() : 1;
	auto newdata = alloc.allocate(newcapacity);
	auto dest = newdata;
	auto elem = elements;
	for (size_t i = 0; i != size(); ++i)
		alloc.construct(dest++, std::move(*elem++));
	free();
	elements = newdata;
	first_free = dest;
	cap = elements + newcapacity;
}

template <typename T>
void Vec<T>::reserve(size_t n) {
	if (n <= capacity()) return;
	auto newdata = alloc.allocate(n);
	auto dest = newdata;
	auto elem = elements;
	for (size_t i = 0; i != size(); ++i)
		alloc.construct(dest++, std::move(*elem++));
	free();
	elements = newdata;
	first_free = dest;
	cap = elements + n;
}

template <typename T>
void Vec<T>::resize(size_t n, const T& t) {
	if (n < size()) {
		while (first_free != elements + n)
			alloc.destroy(--first_free);
	} else if (n > size()) {
		while (size() != n)
			push_back(t);
	}
}
```
## 练习 16.17：
### 声明为 typename 的类型参数和声明为 class 的类型参数有什么不同(如果有的话）？ 什么时候必须使用 typename?
答：
* 在模板参数列表中，这两个关键字的含义相同，可以互换使用。当我们希望通知编译器一个名字表示类型时，必须使用关键字 typename，而不能使用 class。
## 练习 16.18:
### 解释下面每个函数模板声明并指出它们是否非法。 更正你发现的每个错误。
```
(a) template <typename T, U, typename V> void f1(T, U, V);
(b) template <typename T> T f2(int &T);
(c) inline template <typename T> T foo(T, unsigned int*);
(d) template <typename T> f4(T, T);
(e) typedef char Ctype;
 	template <typename Ctype> Ctype f5(Ctype a);
```
答：
* (a) 非法：类型参数 U 没有被明确指定为 typename 或 class。更正：template <typename T, typename U, typename V> void f1(T, U, V);
* (b) 非法：函数参数名不应该使用类型模板参数的名字。更正：template <typename T> T f2(int &val);
* (c) 非法：inline 关键字应该放在模板声明之后。更正：template <typename T> inline T foo(T, unsigned int*);
* (d) 非法：缺少函数返回类型。更正：template <typename T> T f4(T, T);
* (e) 合法：但可能会导致混淆或误解。编译器会将模板中的 Ctype 视为一个独立的模板类型参数，与外部的 typedef 定义的 Ctype 无关。
## 练习 16.19:
### 编写函数,接受一个容器的引用,打印容器中的元素。使用容器的size_type和 size 成员来控制打印元素的循环。
答：
```
// 对于list，这种索引访问是不适用的。
template <typename Container>
void printContainer(const Container& c) {
	// 使用容器的 size_type 来控制循环
	typename Container::size_type i = 0;
	auto size = c.size();
	for (; i < size; ++i) {
		std::cout << c[i] << " ";
	}
}
```
### 练习 16.20:
### 重写上一题的函数,使用begin和end返回的迭代器来控制循环。
答：
```
template <typename Container>
void printContainer(const Container& c) {
	for (auto it = c.begin(); it != c.end(); ++it) {
		std::cout << *it << " ";
	}
}
```
## 练习 16.21：
### 编写你自己的 DebugDelete 版本。
答：
```
// 函数对象类，对给定指针执行 delete
class DebugDelete {
public:
	DebugDelete(std::ostream &s = std::cerr) : os(s) { }
	// 与任何函数模板相同,T的类型由编译器推断
	template <typename T> void operator()(T *p) const {
		os << "deleting unique_ptr" << std::endl; delete p;
	}
private:
	std::ostream &os;
};
```
## 练习 16.22:
### 修改 12.3 节（第 430 页）中你的 TextQuery 程序，令 shared_ptr 成员使用DebugDelete作为它们的删除器（参见 12.1.4 节，第415 页)。
答：
* 初始化shared_ptr时提供DebugDelete对象即可，shared_ptr的删除器类型采用了成员模板设计，编译器可以根据构造函数的实参类型推导出成员模板参数的实参。
```
TextQuery::TextQuery(ifstream &is) : file(new vector<string>, DebugDelete()) {/* . . . */}
```
## 练习 16.23:
### 预测在你的查询主程序中何时会执行调用运算符。如果你的预测和实际不符，确认你理解了原因。
答：
* TextQuery对象的shared_ptr数据成员的引用计数为0时会调用DebugDelete对象的调用运算符。
## 练习 16.24:
### 为你的Blob模板添加一个构造函数,它接受两个迭代器。
答：
```
template <typename T> // 类的类型参数
template <typename It> // 构造函数的类型参数
Blob<T>::Blob(It b, It e) : data(std::make_shared<std::vector<T>>(b, e)) {}
```
## 练习 16.25：
### 解释下面这些声明的含义：
```
extern template class vector<string>;
template class vector<Sales_data>;
```
答：
* extern template class vector<string>;是一个显式实例化声明，用于避免在当前编译单元重复实例化同一模板类，编译器不会再为 vector<string> 生成模板代码，会使用在程序其他编译单元已经生成的实例化代码。
* template class vector<Sales_data>;是一个显式实例化定义，用于在某个编译单元为特定类型（这里是 Sales_data）生成 vector 模板类的所有相关代码。
* 两种声明通常配合使用，以优化程序的编译时间和最终大小。
## 练习 16.26:
### 假设NoDefault是一个没有默认构造函数的类,我们可以显式实例化 vector<NoDefault>吗？ 如果不可以，解释为什么。
答：
* 不可以。当显式地实例化一个模板类，编译器会尝试生成该模板的所有实现代码。如果 vector 的实现需要默认构造函数，就会出现编译错误。而 vector 需要其元素类型具备默认构造函数，在某些操作中，如调整大小（resize）、预留空间（reserve）等，vector 需要能够默认构造其元素。
## 练习 16.27:
### 对下面每条带标签的语句,解释发生了什么样的实例化(如果有的话)。如果一个模板被实例化，解释为什么；如果未实例化，解释为什么没有。
```
template <typename T> class Stack { };
void f1(Stack<char>); // (a)
class Exercise {
 Stack<double> &rsd; // (b)
 Stack<int> si; // (c)
};
int main() {
 Stack<char> *sc; // (d)
 f1(*sc); // (e)
 int iObj = sizeof(Stack< string >); // (f)
}
```
答：
* (a) 没有发生实例化。在函数声明中，模板类不会被实例化。实例化只发生在模板类或函数被实际使用时。
* (b) 没有发生实例化。由于只是声明，没有实际创建 Stack<double> 对象，所以不会触发实例化。
* (c) Stack<int> 被实例化了。因为 si 是一个 Stack<int> 类型的对象。Stack<int> 模板被实例化。
* (d) 没有发生实例化。声明指针本身不会导致模板的实例化，因为没有创建 Stack<char> 类型的实际对象。
* (e) Stack<char> 被实例化了。编译器需要知道 *sc 的类型，以确定这个函数调用是否合法，为了进行这种类型检查，编译器需要知道 Stack<char> 的完整定义，这就触发了模板的实例化。
* (f) Stack< string >被实例化了。为了计算大小，编译器必须根据类模板定义产生该类型。
## 练习 16.28:
### 编写你自己版本的shared_ptr和unique_ptr。
答：
```
#include <functional> // 用于 std::function

template <typename T>
class SharedPtr {
private:
	T* ptr;
	int* count;
	std::function<void(T*)>* deleter;

	void release() {
		if (ptr && --(*count) == 0) {
			(*deleter)(ptr);
			delete count;
			delete deleter;
		}
	}

public:
	// 构造函数，接受一个可调用对象作为删除器
	explicit SharedPtr(T* p = nullptr, std::function<void(T*)> d = [](T* ptr) { delete ptr; })
		: ptr(p), count(new int(1)), deleter(new std::function<void(T*)>(d)) {}

	// 拷贝构造函数
	SharedPtr(const SharedPtr& other)
		: ptr(other.ptr), count(other.count), deleter(other.deleter) {
		(*count)++;
	}

	// 拷贝赋值运算符
	SharedPtr& operator=(const SharedPtr& other) {
		if (this != &other) {
			release();
			ptr = other.ptr;
			count = other.count;
			deleter = other.deleter;
			(*count)++;
		}
		return *this;
	}

	// 解引用
	T& operator*() const { return *ptr; }

	// 成员访问
	T* operator->() const { return ptr; }

	// 获取原始指针
	T* get() const { return ptr; }

	// 析构函数
	~SharedPtr() {
		release();
	}
};


template <typename T, typename Deleter = std::default_delete<T>>
class UniquePtr {
private:
	T* ptr;
	Deleter deleter;

public:
	explicit UniquePtr(T* p = nullptr, const Deleter& d = Deleter())
		: ptr(p), deleter(d) {}

	// 禁止拷贝和赋值
	UniquePtr(const UniquePtr&) = delete;
	UniquePtr& operator=(const UniquePtr&) = delete;

	// 移动构造函数
	UniquePtr(UniquePtr&& other): ptr(other.ptr), deleter(std::move(other.deleter)) {
		other.ptr = nullptr;
	}

	// 移动赋值运算符
	UniquePtr& operator=(UniquePtr&& other) {
		if (this != &other) {
			deleter(ptr);
			ptr = other.ptr;
			deleter = std::move(other.deleter);
			other.ptr = nullptr;
		}
		return *this;
	}

	// 解引用
	T& operator*() const { return *ptr; }

	// 成员访问
	T* operator->() const { return ptr; }

	// 获取原始指针
	T* get() const { return ptr; }

	// 释放所有权
	T* release() {
		T* temp = ptr;
		ptr = nullptr;
		return temp;
	}

	// 替换管理的对象
	void reset(T* p = nullptr) {
		deleter(ptr);
		ptr = p;
	}

	// 析构函数
	~UniquePtr() {
		deleter(ptr);
	}
};
```
## 练习 16.29:
### 修改你的Blob类,用你自己的shared_ptr代替标准库中的版本。
答：
```
#include <functional> // 用于 std::function
#include <iostream>
#include <string>
#include <vector>
#include <initializer_list>

using namespace std;

class StrBlob {
public:
	typedef vector<string>::size_type size_type;

	StrBlob() : data(new vector<string>()) { }
	StrBlob(initializer_list<string> il) : data(new vector<string>(il)) { }
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
	SharedPtr<vector<string>> data;
	// 如果 data[i]不合法，抛出一个异常
	void check(size_type i, const string &msg) const {
		if (i >= data->size()) {
			throw out_of_range(msg);
		}
	}
};
```
## 练习 16.30:
### 重新运行你的一些程序,验证你的shared_ptr类和修改后的Blob类。 (注意:实现weak_ptr类型超出了本书范围,因此你不能将BlobPtr类与你修改后 的 Blob 一起使用。
答：
```
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
## 练习 16.31:
### 如果我们将 DebugDelete 与 unique_ptr 一起使用，解释编译器将删除器处理为内联形式的可能方式。
答：
* 是否内联取决于编译器的决定。编译器会基于生成最高效代码的目标来做出这一决定，同时考虑到函数的大小、调用频率、上下文和其他优化目标。
## 练习 16.32:
### 在模板实参推断过程中发生了什么？
答：
* 这个过程基于函数调用中提供的实参来推断函数模板定义中的模板参数类型。这使得程序员不必显式指定模板实参，简化了模板的使用。
## 练习 16.33:
### 指出在模板实参推断过程中允许对函数实参进行的两种类型转换。
答：
* 顶层const或引用的添加或删除：编译器会忽略实参类型的是否具有顶层const或是否是引用。
* 数组或函数到指针的转换：如果实参是数组或函数类型，它们会被转换为相应的指针类型。
## 练习 16.34:
### 对下面的代码解释每个调用是否合法。如果合法，T 的类型是什么？如果不合法，为什么？
```
template <class T> int compare(const T&, const T&);
(a) compare("hi", "world");
(b) compare("bye", "dad");
```
答：
* (a) compare("hi", "world"); 不合法。"hi" 和 "world" 的类型分别是 const char\[3] 和 const char\[6] ，在模板实参推断中，T 需要对两个参数都适用。
* (b) compare("bye", "dad"); 合法。"bye" 和 "dad" 的类型都是 const char\[4] ，两个实参具有相同的类型，模板实参推断是成功的。
## 练习 16.35:
### 下面调用中哪些是错误的（如果有的话）？如果调用合法，T 的类型是什么？如果调用不合法，问题何在？
```
template <typename T> T calc(T, int);
template <typename T> T fcn(T, T);
double d; float f; char c;
(a) calc(c, 'c');
(b) calc(d, f);
(c) fcn(c, 'c');
(d) fcn(d, f);
```
答：
* (a) calc(c, 'c'); 合法。T 被推断为 char。
* (b) calc(d, f); 合法。T 被推断为 double。
* (c) fcn(c, 'c'); 合法。T 被推断为 char。
* (d) fcn(d, f); 不合法。d 是 double 类型，f 是 float 类型，无法推断出一个单一的 T 类型满足两个参数。
## 练习 16.36:
### 进行下面的调用会发生什么：
```
template <typename T> f1(T, T);
template <typename T1, typename T2> f2(T1, T2);
int i = 0, j = 42, *p1 = &i, *p2 = &j;
const int *cp1 = &i, *cp2 = &j;
(a) f1(p1, p2);
(b) f2(p1, p2);
(c) f1(cp1, cp2);
(d) f2(cp1, cp2);
(e) f1(p1, cp1);
(f) f2(p1, cp1);
```
答：
* (a) f1(p1, p2); 合法。T 被推断为 int*。
* (b) f2(p1, p2); 合法。T1 和 T2 都被推断为 int*
* (c) f1(cp1, cp2); 合法。T 被推断为 const int*。
* (d) f2(cp1, cp2); 合法。T1 和 T2 都被推断为 const int*。
* (e) f1(p1, cp1); 不合法。p1 是 int* 类型，而 cp1 是 const int* 类型。由于 f1 要求两个参数类型完全相同，所以模板实参推断失败。
* (f) f2(p1, cp1); 合法。f2 允许两个不同类型的参数，因此 T1 被推断为 int*，T2 被推断为 const int*。
## 练习 16.37:
### 标准库max函数有两个参数,它返回实参中的较大者。 此函数有一个模板类型参数。 你能在调用max时传递给它一个int和一个double吗?如果可以,如何做？ 如果不可以，为什么？
答：
* 可以显式指定 std::max 的模板参数，来使两个参数的类型一致。通常会选择更宽泛的类型（在这里是 double）来避免精度损失。
```
	int i = 5;
	double d = 6.7;
	auto result = std::max<double>(i, d); // 显式指定模板参数为double
```
## 练习 16.38:
### 当我们调用make_share (参见 12.1.1 节,第401 页)时,必须提供一个显式模板实参。解释为什么需要显式模板实参以及它是如何使用的。
答：
* std::make_shared 是一个函数模板，有一个主要的模板参数，这个参数指定了要返回的 shared_ptr 所管理的对象的类型。对于模板参数只用于返回类型的函数模板，通常需要显式指定模板参数。
* std::make_shared 需要知道要创建的 std::shared_ptr 的确切类型，以便分配正确类型的内存，并调用正确的构造函数。
## 练习 16.39:
### 对16.1.1节(第578页)中的原始版本的compare函数,使用一个显式模板实参，使得可以向函数传递两个字符串字面常量。
答：
```
int result = compare<std::string>("hello", "world");
```
## 练习 16.40:
### 下面的函数是否合法?如果不合法,为什么?如果合法,对可以传递的实参类型有什么限制（如果有的话)？返回类型是什么？
```
template <typename It>
auto fcn3(It beg, It end) -> decltype(*beg + 0)
{
 // 处理序列
 return *beg; // 返回序列中一个元素的拷贝
}
```
答：
* 合法。迭代器 It 必须支持解引用操作,解引用迭代器 It 后得到的元素类型必须支持与 int 类型的加法操作。
* 至于函数的返回类型，由尾置返回类型 decltype(*beg + 0) 推断，即解引用迭代器后得到的元素类型与 int 相加的结果类型。
## 练习 16.41：
### 编写一个新的 sum 版本，它的返回类型保证足够大，足以容纳加法结果。
答：
```
template <typename T1, typename T2>
auto sum(const T1& a, const T2& b) -> decltype(a + b) {
    return a + b;
}
```
## 练习 16.42：
### 对下面每个调用，确定 T 和 val 的类型：
```
template <typename T> void g(T&& val);
int i = 0; const int ci = i;
(a) g(i);
(b) g(ci);
(c) g(i * ci);
```
答：
* (a) g(i);T 被推导为 int&， val 的类型是 int&。
* (b) g(ci);T 被推导为 const int&， val 的类型是 const int&。
* (c) g(i * ci);T 被推导为 int，因为 i * ci 是一个右值表达式。 val 的类型是 int&&。
## 练习 16.43：
### 使用上一题定义的函数，如果我们调用 g(i = ci), g 的模板参数将是什么？
答：
* 赋值操作返回左值，T 被推导为 int& 。
## 练习 16.44:
### 使用与第一题中相同的三个调用,如果g的函数参数声明为T(而不是T&&),确定T的类型。如果g的函数参数是const T&呢?
答：
* T 都被推导为 int。
## 练习 16.45:
### 给定下面的模板,如果我们对一个像42这样的字面常量调用g,解释会发生什么？ 如果我们对一个 int 类型的变量调用g 呢？
```
template <typename T> void g(T&& val) { vector<T> v; }
```
答：
* 调用 g 传递字面常量 42： 字面常量 42 是一个右值 int。 T 被推导为 int。
* 调用 g 传递 int 类型的变量： 变量 i 是一个左值 int。T 被推导为 int&，在函数体内，尝试创建一个 vector<int&> 的实例 v。这是不合法的，因为 vector 的元素类型不能是引用。
## 练习 16.46:
### 解释下面的循环,它来自13.5节(第469页)中的strVec::reallocate:
```
for (size_t i = 0; i != size(); ++i)
 alloc.construct(dest++, std::move(*elem++));
```
答：
* for循环遍历每个已有元素，std::move生成对原数组元素进行绑定的未命名右值引用（一个右值），将该右值传递给construct允许对原数组的左值元素进行移动操作。
## 练习 16.47：
### 编写你自己版本的翻转函数,通过调用接受左值和右值引用参数的函数来测试它。
答：
```
#include <iostream>

using namespace std;

template <typename F, typename T1, typename T2>
void flip(F f, T1 &&t1, T2 &&t2){
	f(std::forward<T2>(t2), std::forward<T1>(t1));
}

void g(int &&i, int& j){
	cout << i << " " << j << endl;
}

int main() {
	int i = 9;
	flip(g, i, 42);

	return 0;
}
```
## 练习 16.48：
### 编写你自己版本的 debug_rep 函数。
答：
```
template <typename T> string debug_rep(const T &t){
	ostringstream ret;
	ret << t;
	return ret.str();
}

template <typename T> string debug_rep(T *p){
	ostringstream ret;
	ret << "pointer: " << p;
	if (p)
		ret << " " << debug_rep(*p);
	else
		ret << " null pointer";
	return ret.str();
}
```
## 练习 16.49：
### 解释下面每个调用会发生什么：
```
template <typename T> void f(T);
template <typename T> void f(const T*);
template <typename T> void g(T);
template <typename T> void g(T*);
int i = 42, *p = &i;
const int ci = 0, *p2 = &ci;
g(42); g(p); g(ci); g(p2);
f(42); f(p); f(ci); f(p2);
```
答：
* g(42); 调用 g(T)，因为42是一个整数字面量，匹配非指针模板。
* g(p); 调用 g(T*)，因为p是一个指向int的指针。 而g(T*)是针对指针类型的最特例化版本。
* g(ci); 调用 g(T)，因为ci是一个const int，匹配非指针模板。
* g(p2); 调用 g(T*)，因为p2是一个指向const int的指针。
* f(42); 调用 f(T)，同样因为42是一个整数字面量。
* f(p); 调用 f(T)，这里T被推导为int*，这里不会调用f(const T*)因为p不是指向const的。
* f(ci); 调用 f(T)，因为ci是一个const int，匹配到T。
* f(p2); 调用 f(const T*)，因为p2是一个指向const int的指针，f(const T*)是针对const指针的最特例化版本。
## 练习 16.50：
### 定义上一个练习中的函数，令它们打印一条身份信息。运行该练习中的代码。如果函数调用的行为与你预期不符,确定你理解了原因。
答：
```
#include <iostream>

template <typename T> void f(T) {
    std::cout << "f(T) called\n";
}

template <typename T> void f(const T*) {
    std::cout << "f(const T*) called\n";
}

template <typename T> void g(T) {
    std::cout << "g(T) called\n";
}

template <typename T> void g(T*) {
    std::cout << "g(T*) called\n";
}

int main() {
    int i = 42, *p = &i;
    const int ci = 0, *p2 = &ci;
    g(42); g(p); g(ci); g(p2);
    f(42); f(p); f(ci); f(p2);
    return 0;
}
```
## 练习 16.51:
### 调用本节中的每个foo,确定sizeof.. (Args)和sizeof.. (rest)分别返回什么。
答：
* 分别返回模板类型参数和函数参数的个数。
## 练习 16.52：
### 编写一个程序验证上一题的答案。
答：
```
#include <iostream>
#include <string>

using namespace std;

template <typename T, typename... Args>
void foo(const T &t, const Args& ... rest) {
	cout << sizeof...(Args) << endl; 
	cout << sizeof...(rest) << endl;
}

int main() {
	int i = 0; double d = 3.14; string s = "how now brown cow";
	foo(i, s, 42, d); 
	foo(s, 42, "hi"); 
	foo(d, s); 
	foo("hi"); 

	return 0;
}
```
## 练习 16.53：
### 编写你自己版本的 print 函数，并打印一个、两个及五个实参来测试它，要打印的每个实参都应有不同的类型。
答：
```
#include <iostream>
#include <string>

using namespace std;

template<typename T>
ostream &print(ostream &os, const T &t){
	return os << t;
}

template <typename T, typename... Args>
ostream &print(ostream &os, const T &t, const Args&... rest) {
	os << t << ", ";
	return print(os, rest...);
}

int main() {
	int i = 0; double d = 3.14; string s = "how now brown cow";
	print(cout, 9);
	cout << endl;
	print(cout, "AAA", 99);
	cout << endl;
	print(cout,i,d,s,"BBB",42);

	return 0;
}
```
## 练习 16.54：
### 如果我们对一个没有<<运算符的类型调用print,会发生什么？
答：
* 会产生编译错误。
## 练习 16.55：
### 如果我们的可变参数版本print的定义之后声明非可变参数版本,解释可变参数的版本会如何执行。
答：
* 可变参数版本会无限递归。
## 练习 16.56：
### 编写并测试可变参数版本的 errorMsg。
答：
```
template <typename T>
ostream &print(ostream &os, const T &t) {
	return os << t;
}

template <typename T, typename... Args>
ostream &print(ostream &os, const T &t, const Args&... rest) {
	os << t << ", ";
	return print(os, rest...);
}

template <typename T> 
string debug_rep(const T &t){
	ostringstream ret; 
	ret << t; 
	return ret.str(); 
}

template <typename... Args>
ostream &errorMsg(ostream &os, const Args&... rest) {
	return print(os, debug_rep(rest)...);
}
```
## 练习 16.57：
### 比较你的可变参数版本的errorMsg和6.2.6节(第198页)中的 error_msg 函数。两种方法的优点和缺点各是什么？
答：
* 可变参数版本的errorMsg可以接受不同类型的实参，但是需要通过递归来展开参数包。
## 练习 16.58：
### 为你的strVec类及你为16.1.2节(第591页)练习中编写的Vec类添加 emplace_back 函数。
答：
```
template <class... Args>
inline void StrVec::emplace_back(Args&&... args){
	chk_n_alloc(); // 如果需要的话重新分配 StrVec 内存空间
	alloc.construct(first_free++,
		std::forward<Args>(args)...);
}
```
## 练习 16.59：
### 假定 s 是一个 string，解释调用 svec.emplace_back (s)会发生什么。
答：
* emplace_back利用了模板和完美转发，可以将任意数量和类型的参数转发给construct。
## 练习 16.60：
### 解释make_shared (参见12.1.1 节，第401页)是如何工作的。
答：
* make_shared内部使用了转发参数包的特性，可以将任意数量和类型的参数完美转发到被管理对象类型的构造函数中。并返回一个指向新创建对象的shared_ptr。
## 练习 16.61：
### 定义你自己版本的make_shared。
答：
```
template<typename T, typename... Args>
std::shared_ptr<T> make_shared(Args&&... args) {
	// 使用new表达式创建一个T类型的对象，利用std::forward<Args>(args)...来完美转发参数
	return std::shared_ptr<T>(new T(std::forward<Args>(args)...));
}
```
## 练习 16.62：
### 定义你自己版本的 hash<Sales_data>，并定义一个 Sales_data 对象 的unordered-multiset。将多条交易记录保存到容器中,并打印其内容。
答：
```
#include <iostream>
#include <unordered_set>
#include <string>

using namespace std;

struct Sales_data {
	friend std::hash<Sales_data>;
	friend bool operator==(const Sales_data& lhs, const Sales_data& rhs);
	friend std::ostream &operator<<(std::ostream& os, const Sales_data& item);

	Sales_data() = default;
	Sales_data(const std::string &s) :bookNo(s) {}
	Sales_data(const std::string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {}
	
	std::string isbn() const {
		return bookNo;
	}
	Sales_data& combine(const Sales_data& rhs) {
		units_sold += rhs.units_sold;
		revenue += rhs.revenue;
		return *this;
	}
private:
	double avg_price() const {
		if (units_sold) {
			return revenue / units_sold;
		} else {
			return 0;
		}
	}

	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};
bool operator==(const Sales_data &lhs, const Sales_data &rhs) {
	return lhs.isbn() == rhs.isbn() &&
		lhs.units_sold == rhs.units_sold &&
		lhs.revenue == rhs.revenue;
}
ostream &operator<<(ostream &os, const Sales_data &item)
{
	os << item.isbn() << " " << item.units_sold << " "
		<< item.revenue << " " << item.avg_price();
	return os;
}

namespace std {
	template <> 
	struct hash<Sales_data> {
		typedef size_t result_type;
		typedef Sales_data argument_type;
		size_t operator()(const Sales_data& s) const;
	};
	size_t hash<Sales_data>::operator()(const Sales_data& s) const{
		return hash<string>()(s.bookNo) ^
			hash<unsigned>()(s.units_sold) ^
			hash<double>()(s.revenue);
	}
}

int main() {
	unordered_multiset<Sales_data> umset;
	umset.emplace("Aaa", 10, 0.9);
	umset.emplace("Bbb", 20, 0.8);

	for (const auto &item : umset) {
		cout << item << endl;
	}

	return 0;
}
```
## 练习 16.63：
### 定义一个函数模板,统计一个给定值在一个vector中出现的次数。 测试你的函数,分别传递给它一个double的vector,一个int的vector以及一个string的vector。
答：
```
#include <vector>
#include <iostream>
#include <string>

// 函数模板定义
template<typename T>
size_t count_occurrences(const std::vector<T>& vec, const T& value) {
	size_t count = 0;
	for (const auto& item : vec) {
		if (item == value) {
			++count;
		}
	}
	return count;
}

// 测试函数
int main() {
	// 测试double类型的vector
	std::vector<double> vecDouble = { 1.1, 2.2, 3.3, 1.1, 4.4 };
	double valDouble = 1.1;
	std::cout << "Double vector: " << count_occurrences(vecDouble, valDouble) << " occurrences of " << valDouble << std::endl;

	// 测试int类型的vector
	std::vector<int> vecInt = { 1, 2, 3, 4, 5, 1, 1 };
	int valInt = 1;
	std::cout << "Int vector: " << count_occurrences(vecInt, valInt) << " occurrences of " << valInt << std::endl;

	// 测试string类型的vector
	std::vector<std::string> vecString = { "apple", "banana", "cherry", "apple", "date", "apple" };
	std::string valString = "apple";
	std::cout << "String vector: " << count_occurrences(vecString, valString) << " occurrences of " << valString << std::endl;

	return 0;
}
```
## 练习 16.64：
### 为上一题中的模板编写特例化版本来处理 vector<const char*>。 编写程序使用这个特例化版本。
答：
```
#include <vector>
#include <iostream>
#include <cstring>

// 原始模板定义
template<typename T>
size_t count_occurrences(const std::vector<T>& vec, const T& value) {
	size_t count = 0;
	for (const auto& item : vec) {
		if (item == value) {
			++count;
		}
	}
	return count;
}

// 针对 const char* 的特例化版本
template<>
size_t count_occurrences<const char*>(const std::vector<const char*>& vec, const char* const& value) {
	size_t count = 0;
	for (const auto& item : vec) {
		if (strcmp(item, value) == 0) {
			++count;
		}
	}
	return count;
}

// 测试程序
int main() {
	// 使用特例化版本处理vector<const char*>
	std::vector<const char*> vecCharPtr = { "apple", "banana", "cherry", "apple", "date", "apple" };
	const char* valCharPtr = "apple";
	std::cout << "const char* vector: " << count_occurrences(vecCharPtr, valCharPtr) <<
		" occurrences of " << valCharPtr << std::endl;

	system("pause");
	return 0;
}
```
## 练习 16.65：
### 在16.3节(第617页)中我们定义了两个重载的debug_rep版本,一个接受const char\*参数,另一个接受char*参数。 将这两个函数重写为特例化版本。
答：
```
template <typename T>
string debug_rep(T* p) {
	ostringstream ret;
	ret << p;
	return ret.str();
}
template <>
string debug_rep(char *p) {
	return string(p);
}
template <>
string debug_rep(const char *p) {
	return string(p);
}
```
## 练习 16.66：
### 重载 debug_rep 函数与特例化它相比，有何优点和缺点？
答：
* 优点：重载函数在类型匹配上更为直接和清晰
* 缺点：如果需要处理多种指针类型并且对于大多数类型的处理逻辑相同，那么使用重载就需要为每一种类型写一个几乎相同的函数，这会导致代码重复。
## 练习 16.67：
### 定义特例化版本会影响 debug_rep 的函数匹配吗？如果不影响，为什么？
答：
* 特例化本身不会阻碍函数匹配过程。
