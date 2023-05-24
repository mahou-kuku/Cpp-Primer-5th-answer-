## 练习 6.1：
### 实参和形参的区别是什么?
答：  
* 实参是指在函数调用时小括号中传递的具体值或对象，用来初始化形参。形参则是在函数声明或定义的小括号中声明的局部对象。
## 练习 6.2：
### 请指出下列函数哪个有错误，为什么？应该如何修改这些错误呢？
```
(a)
int f() {
	string s;
	//...
	return s;
}
(b)
f2(int i){/* ...*/}
(c)
int calc(int v1,int v1)/* ...*/}
(d)
double square(double x) return x * x;
```
答：  
```
//(a)这个函数试图返回一个string类型的值，但其声明的返回类型却是int。
string f() {
	string s;
	//...
	return s;
}
//(b)这个函数没有声明返回类型，必须要声明返回类型，比如int。
int f2(int i) {/* ...*/ }
//(c)这个函数的形参列表中有两个同名的形参v1，这是不允许的。
int calc(int v1, int v2) { /* ...*/ }
//(d)这个函数的主体缺少了函数体的花括号 {}。
double square(double x) { return x * x; }
```
## 练习 6.3:
### 编写你自己的fact函数,上机检查是否正确。
答：  
```
unsigned long long factorial(int num) {
	unsigned long long ret = 1;
	while (num > 1) {
		ret *= num--;
	}
	return ret;
}
```
## 练习 6.4：
### 编写一个与用户交互的函数，要求用户输入一个数字，计算生成该数字的阶乘。在main 函数中调用该函数。
答：  
```
#include <iostream>

//计算阶乘函数
void factorial() {
	int num;
	std::cout << "Please enter a number to calculat factorial:";
	std::cin >> num;
	if (num < 0) {
		std::cout << "cannot calculate the factorial of a negative number!";
	} else {
		unsigned long long result = num ? 1 : 0;
		while (num > 1) {
			result *= num--;
		}
		std::cout << "The factorial of the integer is equal to：" << result << std::endl;
	}
}

int main() {
	factorial();

	return 0;
}
```
### 练习 6.5:
### 编写一个函数输出其实参的绝对值。
答：  
```
#include <iostream>
#include <cstdlib>	//用于调用 std::abs 函数

//输出绝对值函数
double absoluteValue(double num) {
	return std::abs(num);
}

int main() {
	double num;
	std::cout << "Please enter an integer: ";
	std::cin >> num;
	std::cout << "The absolut value of the integer is: " << absoluteValue(num) << std::endl;

	return 0;
}
```
## 练习 6.6：
### 说明形参、局部变量以及局部静态变量的区别。编写一个函数，同时用到这三种形式。
答：  
* 形参：形参在函数被调用时以实参的值初始化。形参的作用域是它所在的函数，而生命周期是该函数的执行过程。
* 局部变量：局部变量是在函数体中定义的变量，它的作用域和生命周期也是它所在的函数，函数执行结束时销毁。
* 局部静态变量：局部静态变量的作用域仍限于定义它们的函数，但它们的生命周期却持续到程序结束。在函数被多次调用的情况下，局部静态变量可以保持之前调用时的值。
```
#include <iostream>

void myFunction(int param) {	//param是形参
	int localVar = 10;	//localVar是局部变量
	static int staticLocalVar = 10;	//staticLocalVar是局部静态变量

	staticLocalVar += param;	//修改局部静态变量
	localVar += param;	//修改局部变量

	std::cout << "Parameter value: " << param << std::endl;
	std::cout << "Local variable value: " << localVar << std::endl;
	std::cout << "Static local variable value: " << staticLocalVar << std::endl;
}

int main() {
	myFunction(5);
	myFunction(5);

	return 0;
}
```
## 练习 6.7：
### 编写一个函数，当它第一次被调用时返回0，以后每次被调用返回值加1。
答：  
```
size_t count_calls() {
	static size_t ctr = 0;
	return ctr++;
}
```
## 练习 6.8:
### 编写一个名为 Chapter6.h 的头文件，令其包含 6.1 节练习(第 184 页)中的函数声明。
答：  
```
#ifndef CHAPTER6_H
#define CHAPTER6_H

unsigned long long factorial(int num);
void factorial();
double absoluteValue(double num);

#endif
```
## 练习 6.9：
### 编写你自己的 fact.cc 和 factMain.cc，这两个文件都应该包含上一小节·的练习中编写的Chapter6.h头文件。通过这些文件,理解你的编译器是如何支持分离式编译的。
答：  
* fact.cpp
```
#include <iostream>
#include <cstdlib>
#include "Chapter6.h"

unsigned long long factorial(int num) {
	unsigned long long ret = 1;
	while (num > 1) {
		ret *= num--;
	}
	return ret;
}

void factorial() {
	int num;
	std::cout << "Please enter a number to calculat factorial:";
	std::cin >> num;
	if (num < 0) {
		std::cout << "cannot calculate the factorial of a negative number!";
	} else {
		unsigned long long result = num ? 1 : 0;
		while (num > 1) {
			result *= num--;
		}
		std::cout << "The factorial of the integer is equal to：" << result << std::endl;
	}
}

double absoluteValue(double num) {
	return std::abs(num);
}
```
* factMain.cpp
```
#include <iostream>
#include "Chapter6.h"


int main() {
	std::cout << "Factorial is: " << factorial(5) << std::endl;
	factorial();
	std::cout << "The absolut value of -9.9 is: " << absoluteValue(-9.9) << std::endl;

	return 0;
}
```
## 练习 6.10:
### 编写一个函数，使用指针形参交换两个整数的值。在代码中调用该函数并输出交换后的结果，以此验证函数的正确性。
答：  
```
#include <iostream>

//交换两个整数的值
void swap(int* a, int* b) {
	int temp = *a;
	*a = *b;
	*b = temp;
}

int main() {
	int x = 5, y = 10;
	std::cout << "Before swapping: " << std::endl;
	std::cout << "x = " << x << ", y = " << y << std::endl;

	//调用函数，传入x和y的地址
	swap(&x, &y);

	std::cout << "After swapping: " << std::endl;
	std::cout << "x = " << x << ", y = " << y << std::endl;

	return 0;
}
```
## 练习 6.11：
### 编写并验证你自己的reset函数，使其作用于引用类型的参数。
答：  
```
#include <iostream>

void reset(int& i) {
	i = 0;
}

int main() {
	int x = 5;
	std::cout << "Before reset: x = " << x << std::endl;

	reset(x);
	std::cout << "After reset: x = " << x << std::endl;

	return 0;
}
```
## 练习 6.12：
### 改写 6.2.1 节中练习 6.10（第 188 页）的程序，使用引用而非指针交换两个整数的值。你觉得哪种方法更易于使用呢？为什么？
答：  
```
#include <iostream>

void swap(int& a, int& b) {
	int temp = a;
	a = b;
	b = temp;
}

int main() {
	int x = 5, y = 10;
	std::cout << "Before swapping: x = " << x << ", y = " << y << std::endl;

	//调用函数，传入x和y的地址
	swap(x, y);

	std::cout << "After swapping: x = " << x << ", y = " << y << std::endl;

	return 0;
}
```
* 使用引用形参更简单一些，因为不需要用&来传递变量，也不需要用\*来访问变量。
## 练习 6.13：
### 假设 T 是某种类型的名字，说明以下两个函数声明的区别：一个是 void f(T)，另一个是 void f(&T)。
答：  
* void f(T)：函数f接受一个T类型的参数，使用的是值传递方式。当调用函数f时，会创建实参的一个副本，函数f内部对这个参数的任何修改都不会影响到传入的实参。
* void f(T&)：函数f接受一个T类型的引用作为参数，使用的是引用传递方式。当调用函数f时，实际上是传递了实参的引用，函数f内部对这个参数的修改会直接作用到原来的实参上。需要注意的是&T表达式并不是标准的C++语法，如果形参是指针或引用类型，应该写为T\*或T&。
## 练习 6.14：
### 举一个形参应该是引用类型的例子，再举一个形参不能是引用类型的例子。
答：  
```
//需要修改传递到函数的实参的值时，需要使用引用类型的形参。
void swap(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}
//不希望或者不需要修改传递到函数的实参的值时，不应该使用引用类型的形参。
void process_elements(std::vector<int>::iterator begin, std::vector<int>::iterator end) {
	for (; begin != end; ++begin) {
		// 对 *begin 执行某种操作
	}
}
```
## 练习 6.15： 
### 说明 find_char 函数中的三个形参为什么是现在的类型，特别说明为什么s是常量引用而occurs是普通引用？为什么s和occurs是引用类型而c不是？如果令s是普通引用会发生什么情况？如果令occurs是常量引用会发生什么情况？
答：  
* s 是一个常量引用。由于字符串可能会非常大，直接传值会导致不必要的开销，传递引用可以避免这个问题。同时，由于不打算修改字符串，所以使用常量引用是一个好的选择，它能保证字符串的内容不会被意外修改。
* occurs 是普通引用，因为需要修改它的值来反映字符出现的次数。如果 occurs 是常量引用，那么就不能修改它的值，这与需求相矛盾。
* c 是传值参数，因为它是一个 char 类型的变量，占用的内存空间很小，传值和传引用在性能上几乎没有差别。同时，也不需要在函数内部修改 c，所以无需使用引用。
* 如果把 s 改为普通引用，那么在函数内部，就有可能会意外地修改字符串的内容，这可能会导致调用者在调用函数后发现字符串的值发生了变化，这通常不是一个期望的结果。
* 如果把 occurs 改为常量引用，这会导致编译错误，因为需要在函数内部修改 occurs 的值以反映字符出现的次数。
