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
* 如果把 s 改为普通引用，那么在函数内部，就有可能会意外地修改字符串的内容，这可能会导致调用者在调用函数后发现字符串的值发生了变化，这通常不是一个预期的结果。
* 如果把 occurs 改为常量引用，这会导致编译错误，因为需要在函数内部修改 occurs 的值以反映字符出现的次数。
## 练习 6.16:
### 下面的这个函数虽然合法,但是不算特别有用。指出它的局限性并设法改善。
```
bool is_empty(string& s) { return s.empty(); }
```
答：  
* 因为这个函数不需要修改字符串，而非const引用不能绑定到const对象、字面值或者用来存储类型转换后的值的临时对象，所以改为const引用形参可以改善这个函数的局限性。
```
bool is_empty(const string& s) { return s.empty(); }
```
## 练习 6.17：
### 编写一个函数，判断 string 对象中是否含有大写字母。编写另一个函数，把 string 对象全都改成小写形式。在这两个函数中你使用的形参类型相同吗？为什么？
答：  
* 不相同，判断 string 对象中是否含有大写字母的函数应该使用常量引用形参，这能保证字符串的内容不会被意外修改。并且能接受更多种类的实参类型。
* 把 string 对象改成小写形式的函数需要使用非常量引用形参，因为需要在函数内部修改 string 对象的值。
## 练习 6.18：
### 为下面的函数编写函数声明，从给定的名字中推测函数具备的功能。
```
(a) 名为 compare 的函数，返回布尔值，两个参数都是 matrix 类的引用。
(b) 名为 change_val 的函数,返回vector<int>的迭代器,有两个参数:一个是int,另一个是vector<int>的迭代器。
```
答：  
```
(a)
bool compare(matrix&, matrix&);
(b)
vector<int>::iterator change_val(int, vector<int>iterator);
```
## 练习 6.19：
### 假定有如下声明，判断哪个调用合法、哪个调用不合法。对于不合法的函数调用，说明原因。
```
double calc(double);
int count(const string&, char);
int sum(vector<int>::iterator, vector<int>::iterator, int);
(a) calc(23.4, 55.1);	(b) count("avcda", 'a');
(c) calc(66);		(d) sum(vec.begin(),vec.end(), 3.8);
```
答：  
```
(a)不合法，calc 函数不接受 2 个参数。
(b)合法(c)合法(d)合法
```
## 练习 6.20:
### 引用形参什么时候应该是常量引用?如果形参应该是常量引用,而我们将其设为了普通引用，会发生什么情况？
答：  
* 当函数内部不需要修改实参的值时，应该考虑使用常量引用形参。使用普通引用可能会意外修改实参的值，并且限制函数可以接受的实参类型。
## 练习 6.21：
### 编写一个函数，令其接受两个参数：一个是 int 型的数，另一个是 int 指针。函数比较int的值和指针所指的值,返回较大的那个。在该函数中指针的类型应该是什么？
答：  
```
int compare(const int num, const int* ptr) {
	return (num > *ptr) ? num : *ptr;
}
```
* 指针的类型应该是const int* 。
## 练习 6.22:
### 编写一个函数,令其交换两个int指针。
答：  
```
void exchangePtr(int*& ptr_1, int*& ptr_2) {
	int* temp = ptr_1;
	ptr_1 = ptr_2;
	ptr_2 = temp;
}
```
## 练习 6.23：
### 参考本节介绍的几个 print 函数，根据理解编写你自己的版本。依次调用每个函数使其输入下面定义的i 和j：
```
int i = 0, j[2] = { 0,1 };
```
答：  
```
#include <iostream>

void print(const int& pi) {
	std::cout << pi << std::endl;
}

void print(const char* cp) {
	if (cp) {
		while (*cp) {
			std::cout << *cp++;
		}
		std::cout << std::endl;
	}
}

void print(const int* beg, const int* end) {
	while (beg != end) {
		std::cout << *beg++ << std::endl;
	}

}

void print(const int ia[], size_t size) {
	for (size_t i = 0; i != size; ++i) {
		std::cout << ia[i] << std::endl;
	}
}

void print(const int(&arr)[2]) {
	for (auto elem : arr) {
		std::cout << elem << std::endl;
	}
}

int main() {
	int i = 0, j[2] = { 0,1 };
	const char* c = "Hello world!";

	print(i);
	print(c);
	print(std::begin(j), std::end(j));
	print(j, std::end(j) - std::begin(j));
	print(j);

	return 0;
}
```
## 练习 6.24:
### 描述下面这个函数的行为。如果代码中存在问题，请指出并改正。
```
void print(const int ia[10])
{
	for (size_t i = 0; i != 10; ++i)
		cout << ia[i] << endl;
}
```
答：  
* 这个函数只能保证接受的实参是一个int\*类型，\[10]指针指向的地址处并不一定真的有10个有效的int元素。可以将ia声明为引用或指针来确保只接受含有10个int元素的数组实参。
```
void print(const int (&ia)[10])
{
	for (size_t i = 0; i != 10; ++i)
		cout << ia[i] << endl;
}
```
## 练习 6.25：
### 编写一个 main 函数，令其接受两个实参。把实参的内容连接成一个 string对象并输出出来。
答：  
```
#include <iostream>
#include <string>

int main(int argc, char* argv[]) {
	std::string str;

	while (*argv) {
		str += *argv++;
		str += ' ';
	}

	std::cout << str << std::endl;
	return 0;
}
```
## 练习 6.26：
### 编写一个程序，使其接受本节所示的选项；输出传递给 main 函数的实参的内容。
答：  
```
#include <iostream>

int main(int argc, char* argv[]) {

	while (*argv) {
		std::cout << *argv++ << std::endl;
	}

	return 0;
}
```
## 练习 6.27：
### 编写一个函数，它的参数是 initializer_list\<int>类型的对象，函数的功能是计算列表中所有元素的和。
答：  
```
int sum(std::initializer_list<int>il) {
	int total = 0;
	for (auto i : il) {
		total += i;
	}
	return total;
}
```
## 练习 6.28：
### 在 error_msg 函数的第二个版本中包含 ErrCode 类型的参数，其中循环内的 elem 是什么类型？
答：  
* elem 的类型是const string& 。
## 练习 6.29:
### 在范围for循环中使用initializer_list对象时,应该将循环控制变量声明成引用类型吗？为什么？
* 可以根据需要来选择是否将循环控制变量声明为引用类型。但是initializer_list中的元素始终是const的，尝试修改这些元素的值会导致编译错误，所以要将循环控制变量声明成引用的话，应该声明为const引用。
## 练习 6.30：
### 编译第 200 页的 str_subrange 函数，看看你的编译器是如何处理函数中的错误的。
答：  
* error C2561: “str_subrang”: 函数必须返回值		note: 参见“str_subrang”的声明
## 练习 6.31:
### 什么情况下返回的引用无效?什么情况下返回常量的引用无效?
答：  
* 无论是不是常量引用，当返回局部对象的引用时，都将绑定到被销毁的无效内存。
## 练习 6.32：
### 下面的函数合法吗？如果合法，说明其功能；如果不合法，修改其中的错误并解释原因。
```
int& get(int* arry, int index) { return arry[index]; }
int main() {
	int ia[10];
	for (int i = 0; i != 10; ++i)
		get(ia, i) = i;
}
```
答：  
* 合法，该函数返回一个int对象的引用，这是一个左值可以被赋值。
## 练习 6.33：
### 编写一个递归函数，输出 vector 对象的内容。
答：  
```
#include <iostream>
#include <vector>

void printVectorRecursively(const std::vector<int>& v, const size_t index = 0) {
	if (index >= v.size()) {
		return;		//基本情况：已打印完所有元素
	}
	std::cout << v[index] << " ";
	printVectorRecursively(v, index + 1);	//递归情况：打印当前元素，然后递归处理其余元素
}

int main() {
	std::vector<int> vec = { 1,2,3,4,5 };
	printVectorRecursively(vec);

	return 0;
}
```
## 练习 6.34:
### 如果factorial函数的停止条件如下所示,将发生什么情况?
```
if (val != 0)
```
答：  
* 程序将多递归一次。另外新的判断条件不能防止val是个负数时的无限递归问题。
## 练习 6.35:
### 在调用factorial 函数时，为什么我们传入的值是 val-1 而非 val--?
答：  
* 后置递增返回的是对象递增前的副本，所以调用函数时传递的值将永远不会减少而导致无限递归
## 练习 6.36：
### 编写一个函数的声明，使其返回数组的引用并且该数组包含 10 个 string对象。不要使用尾置返回类型、decltype 或者类型别名。
答：  
```
string (&strRef())[10]
```
## 练习 6.37：
### 为上一题的函数再写三个声明，一个使用类型别名，另一个使用尾置返回类型，最后一个使用 decltype 关键字。你觉得哪种形式最好？为什么？
答：  
```
//使用类型别名
using StrArrRef = string(&)[10];
StrArrRef strRef();
//使用尾置返回类型
auto strRef()->string(&)[10];
//使用 decltype 关键字
string (arr)[10];
decltype(arr)& strRef();
```
* 我更倾向于使用尾置返回类型。因为这种方式的表达力更强，也不需要依赖其他已知变量或额外的类型定义。
## 练习 6.38：
### 修改 arrPtr 函数，使其返回数组的引用。
答：  
```
int odd[] = { 1,3,5,7,9 };
int even[] = { 0,2,4,6,8 };
decltype(odd)& arrRef(int i){
	return (i % 2) ? odd : even;
}
```
## 练习 6.39:
### 说明在下面的每组声明中第二条声明语句是何含义。如果有非法的声明,请指出来。
```
(a)
int calc(int, int);
int calc(const int, const int);
(b)
int get();
double get();
(c)
int *reset(int *);
double *reset(double *);
```
答：
* 这些声明语句是在尝试进行函数重载。(a) 非法。顶层const不影响传入函数的对象。(b) 非法。函数重载不能仅仅依靠返回类型的不同。 (c) 合法。
## 练习 6.40:
### 下面的哪个声明是错误的?为什么?
```
(a) int ff(int a, int b = 0, int = 0);
(b) char *init(int ht = 24, int wd, char bckgrnd);
```
答：
```
(a) 正确。
(b) 错误。一旦某个形参被赋予了默认值，它后面所有的形参都必须有默认值。
```
## 练习 6.41：
### 下面的哪个调用是非法的？为什么？哪个调用虽然合法但显然与程序员的初衷不符？为什么？
```
char *init(int ht, int wd = 80, char bckgrnd = ' ');
(a) init();	(b) init(24, 10);	(c) init(14, '*');
```
答：
* (a) 非法。在函数 init 的声明中 ht 没有默认值，所以在调用函数时，必须提供 ht 的值。
* (b) 合法。
* (c) 合法但显然与程序员的初衷不符。在这个调用中，'\*' 是一个字符常量，但由于缺少了 wd 的值，它被当作了第二个参数 wd 。
## 练习 6.42:
### 给make_plural函数(参见6.3.2节,第201页)的第二个形参赋予默认实参's',利用新版本的函数输出单词success和failure的单数和复数形式。
答：
```
#include <iostream>
#include <string>

std::string mack_plural(size_t ctr, const std::string &word, const std::string &ending = "s") {
	return (ctr > 1) ? word + ending : word;
}

int main() {
	std::cout << "Singular: " << mack_plural(1, "success", "es") << " " << mack_plural(1, "failure") << std::endl;
	std::cout << "Plural: " << mack_plural(2, "success", "es") << " " << mack_plural(2, "failure") << std::endl;

	return 0;
}
```
## 练习 6.43：
### 你会把下面的哪个声明和定义放在头文件中？哪个放在源文件中？为什么？
```
(a) inline bool eq(const BigInt&, const Bigint&) { /* ...*/ }
(b) void putValues(int *arr, int size);
```
答：
* (a) 这个是一个内联函数的定义，所以应该放在头文件中。因为头文件通常被多个源文件包含，这样可以使得内联函数的定义对所有调用点可见。
* (b) 这个只是一个函数声明，通常应该放在头文件中。函数的定义通常放在源文件中。
## 练习 6.44：
### 将 6.2.2 节（第 189 页）的 isShorter 函数改写成内联函数。
答：
```
inline bool isShorter(const string &s1, const string &s2) {
	return s1.size() < s2.size();
}
```
## 练习 6.45：
### 回顾在前面的练习中你编写的那些函数，它们应该是内联函数吗？如果是，将它们改写成内联函数;如果不是,说明原因。
答：
* 以下这些类型的函数通常不应该是内联函数：
* 大型函数：如果函数体很大，那么内联它将导致生成的代码量大幅增加，这可能导致程序的整体性能降低。
* 递归函数：编译器通常不能将递归函数内联。每次递归调用都需要它自己的函数调用上下文。
* 频繁修改的函数：如果一个函数经常需要修改，那么将它内联可能导致需要重新编译大量代码，因为内联函数的定义必须出现在每一个调用它的文件中。
* 调用频率不高的函数：如果函数的调用频率不高，那么内联的收益将大大降低。
## 练习 6.46：
### 能把 isShorter 函数定义成 constexpr 函数吗？如果能，将它改写成constexpr函数;如果不能,说明原因。
答：
* 不能，如果试图在constexpr函数中调用非constexpr函数，编译器会报错，string类型的成员函数size不是constexpr 函数。
## 练习 6.47:
### 改写6.3.2节(第205页)练习中使用递归输出vector内容的程序,使其有条件地输出与执行过程有关的信息。例如，每次调用时输出 vector对象的大小。分别在打开和关闭调试器的情况下编译并执行这个程序。
答：
```
#include <iostream>
#include <vector>

void printVectorRecursively(const std::vector<int>& v, const size_t index = 0) {
#ifndef NDEBUG
	std::cout << "Size of v: " << v.size() << std::endl;
#endif
	if (index >= v.size()) {
		return;		//基本情况：已打印完所有元素
	}
	std::cout << v[index] << std::endl;
	printVectorRecursively(v, index + 1);	//递归情况：打印当前元素，然后递归处理其余元素
}

int main() {
	std::vector<int> vec = { 1,2,3,4,5 };
	printVectorRecursively(vec);

	return 0;
}

```
## 练习 6.48：
### 说明下面这个循环的含义，它对 assert 的使用合理吗？
```
	string s;
	while (cin >> s&&s != sought) { }	//空函数体
	assert(cin);
```
答：
* 不合理。assert是用来检查在程序的开发和测试阶段预期中程序本身的逻辑错误，而不应用于处理用户输入错误。因为 assert 在发布版本中通常会被禁用，如果依赖 assert 来处理运行环境或用户交互错误，那么在发布版本中，这些错误就不会被正确处理。
## 练习 6.49：
### 什么是候选函数？什么是可行函数？
答：
* 候选函数具备两个特征：一是与被调用的函数同名，二是其声明在调用点可见。
* 可行函数也有两个特征:一是其形参数量与本次调用提供的实参数量相等，二是每个实参的类型与对应的形参类型相同，或者能转换成形参的类型。
## 练习 6.50：
### 已知有第 217 页对函数 f 的声明，对于下面的每一个调用列出可行函数。其中哪个函数是最佳匹配？如果调用不合法，是因为没有可匹配的函数还是因为调用具有二义性？
```
(a) f(2.56, 42)		(b) f(42)	(c) f(42, 0)	(d) f(2.56, 3.14)
```
答：
```
(a) 可行函数：void f(int, int)	void f(double, double = 3.14)。调用不合法：调用具有二义性。
(b) 可行函数：void f(int)		void f(double, double = 3.14)。最佳匹配：void f(int)
(c) 可行函数：void f(int, int)	void f(double, double = 3.14)。最佳匹配：void f(int, int)
(d) 可行函数：void f(int, int)	void f(double, double = 3.14)。最佳匹配：void f(double, double = 3.14)
```
## 练习 6.51:
### 编写函数 f的4个版本，令其各输出一条可以区分的消息。验证上一个练习的答案，如果你回答错了，反复研究本节的内容直到你弄清自己错在何处。
答：
```
void f() {
	std::cout << "In f().";
}

void f(int) {
	std::cout << "In f(int).";
}

void f(int, int) {
	std::cout << "In f(int,int).";
}

void f(double, double = 3.14) {
	std::cout << "In f(double,double = 3.14).";
}
```
## 练习 6.52：
### 已知有如下声明，
```
void manip(int, int);
double dobj;
```
请指出下列调用中每个类型转换的等级(参见 6.6.1 节，第219页)。
```
(a) manip('a', 'z');	(b) manip(55.3, dobj);
```
答：
* (a) 'a':等级3 	'z'：等级3 	(b)55.3：等级4	 dobj：等级4
## 练习 6.53:
### 说明下列每组声明中的第二条语句会产生什么影响,并指出哪些不合法(如果有的话）。
```
(a)
int calc(int&, int&);
int calc(const int&, const int&);
(b)
int calc(char*, char*);
int calc(const char*, const char*);
(c)
int calc(char, char*);
int calc(char* const, char* const);
```
答：
* (a) 产生函数重载。(b) 产生函数重载。(c) 不合法，顶层const不影响传入函数的对象。
## 练习 6.54：
### 编写函数的声明，令其接受两个 int 形参并且返回类型也是 int；然后声明一个 vector 对象，令其元素是指向该函数的指针。
答：
```
#include <vector>

int func(int, int);
int main() {
	std::vector<int(*)(int, int)> vec;

	return 0;
}
```
## 练习 6.55：
### 编写 4 个函数，分别对两个 int 值执行加、减、乘、除运算；在上一题创建的 vector 对象中保存指向这些函数的指针。
答：
```
#include <vector>

int add(int number_1, int number_2) {
	return number_1 + number_2;
}

int subtrack(int number_1, int number_2) {
	return number_1 - number_2;
}

int multiply(int number_1, int number_2) {
	return number_1 * number_2;
}

int divide(int number_1, int number_2) {
	return number_1 / number_2;
}

int main() {
	std::vector<int(*)(int, int)> vec = { add,subtrack,multiply,divide };
	
	return 0;
}

```
## 练习 6.56：
### 调用上述 vector 对象中的每个元素并输出其结果。
答：
```
#include <iostream>
#include <vector>

int add(int number_1, int number_2) {
	return number_1 + number_2;
}

int subtrack(int number_1, int number_2) {
	return number_1 - number_2;
}

int multiply(int number_1, int number_2) {
	return number_1 * number_2;
}

int divide(int number_1, int number_2) {
	return number_1 / number_2;
}

int main() {
	std::vector<int(*)(int, int)> vec = { add,subtrack,multiply,divide };
	for (auto i : vec) {
		std::cout << i(9, 3) << std::endl;
	}

	return 0;
}
```
## 练习 7.23：
### 编写你自己的 Screen 类。
答：
```
class Screen {
public:
	typedef std::string::size_type pos;
	Screen() = default;
	Screen(pos ht, pos wd, char c) :height(ht), width(wd), contents(ht * wd, c) {}
	char get()const { return contents[cursor]; }
	char get(pos row, pos column) const { return contents[row * width + column]; }
	Screen& move(pos row, pos column);
private:
	pos cursor = 0, height = 0, width = 0;
	std::string contents;
};
inline Screen& Screen::move(pos row, pos column) {
	cursor = row * width + column;
	return *this;
}
```
## 练习 7.24：
### 给你的 Screen 类添加三个构造函数：一个默认构造函数；另一个构造函数接受宽和高的值，然后将 contents 初始化成给定数量的空白；第三个构造函数接受宽和高的值以及一个字符，该字符作为初始化之后屏幕的内容。
答：
```
class Screen {
public:
	typedef std::string::size_type pos;

	// Default constructor
	Screen() = default;
	// Constructor with width and height parameters,initializes contents with spaces
	Screen(pos ht, pos wd) :height(ht), width(wd), contents(ht * wd, ' ') {}
	// Constructor with width, height and a character for contents initialization
	Screen(pos ht, pos wd, char c) :height(ht), width(wd), contents(ht * wd, c) {}

	char get()const { return contents[cursor]; }
	char get(pos row, pos column) const { return contents[row * width + column]; }
	Screen& move(pos row, pos column);

private:
	pos cursor = 0, height = 0, width = 0;
	std::string contents;
};
inline Screen& Screen::move(pos row, pos column) {
	cursor = row * width + column;
	return *this;
}
```
## 练习 7.25：
### Screen 能安全地依赖于拷贝和赋值操作的默认版本吗？如果能，为什么？如果不能，为什么？
答：
* Screen 类可以安全地依赖编译器生成的默认拷贝构造函数和赋值操作。因为Screen 类定义中的数据成员是可以被直接拷贝的简单类型，以及有良好定义的拷贝和赋值操作的std::string类型。
## 练习 7.26:
### 将Sales data::avg price定义成内联函数。
答:
```
inline double Sales_data::avg_price() const {
	return units_sold ? revenue / units_sold : 0;
}
```
