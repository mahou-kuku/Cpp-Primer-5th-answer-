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
		std::cout << "The factorial of " << num << " is equal to：" << result << std::endl;
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
