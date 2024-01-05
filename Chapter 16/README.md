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
