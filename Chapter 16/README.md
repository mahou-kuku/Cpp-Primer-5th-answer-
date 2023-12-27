## 练习 16.1：
### 给出实例化的定义。
答：
* C++编译器会根据模板函数的定义和提供的实参类型来实例化一个具体的函数版本。
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
