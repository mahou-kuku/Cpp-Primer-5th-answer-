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
