## 练习4.1:
### 表达式5+10\*20/2的求值结果是多少?
答：  
* 105。
## 练习 4.2：
### 根据 4.12 节中的表，在下述表达式的合理位置添加括号，使得添加括号后运算对象的组合顺序与添加括号前一致。
```
*vec.begin()
*vec.begin() + 1
```
答：  
```
*(vec.begin());
(*(vec.begin())) + 1;
```
## 练习 4.3：
### C++语言没有明确规定大多数二元运算符的求值顺序，给编译器优化留下了·余地。这种策略实际上是在代码生成效率和程序潜在缺陷之间进行了权衡,你认为这可以接受吗?请说出你的理由。
答：  
* 可以接受。程序员可以在保持代码清晰和可维护的同时，充分利用编译器的优化能力。理由如下：
* 优化编译器性能：通过不强制要求特定的求值顺序，编译器可以生成更高效的代码，提升程序执行速度。
* 提高可移植性：不同的编译器可能对优化有不同的实现策略。通过允许编译器自由选择求值顺序，可以提高程序在不同环境中的可移植性。
* 强迫程序员写出更清晰的代码：由于求值顺序的不确定性，程序员会被迫避免在同一表达式中对同一个对象进行多次修改。这样的代码更容易理解和维护。
## 练习 4.4：
### 在下面的表达式中添加括号，说明其求值的过程及最终结果。编写程序编译该（不加括号的）表达式并输出其结果验证之前的推断。
```
12 / 3 * 4 + 5 * 15 + 24 % 4 / 2
```
答：  
* (((12 / 3) * 4) +( 5 * 15)) + ((24 % 4) / 2) = ((4 * 4) + 75) + (0 / 2) = (16 + 75) + 0 = 91 + 0 = 91
```
#include <iostream>

int main() {
	std::cout << (((12 / 3) * 4) +( 5 * 15)) + ((24 % 4) / 2) << std::endl;
	std::cout << 12 / 3 * 4 + 5 * 15 + 24 % 4 / 2 <<std::endl;
	
	return 0;
}
```
## 练习 4.5:
### 写出下列表达式的求值结果。
```
(a)-30 * 3 + 21 / 5;
(b)-30 + 3 * 21 / 5;
(c)30 / 3 * 21 % 5;
(d)-30 / 3 * 21 % 4;
```
答：  
* (a)-86 (b)-18 (c)0 (d)-2
## 练习 4.6：
### 写出一条表达式用于确定一个整数是奇数还是偶数。
答：  
```
	if (number % 2 == 0) {
		//number 是偶数
	} else {
		//number 是奇数
	}
```
## 练习 4.7：
### 溢出是何含义？写出三条将导致溢出的表达式。
答：  
* 溢出是指当计算或赋值给一个变量的结果超出了该数据类型所能表示的范围时所发生的现象。溢出可能导致结果不正确或者不可预测。
* 以下是三条可能导致溢出的表达式：
```
//对于有符号整数溢出
int a = 2147483647;
int b = a + 1; // 当a是int类型的最大值时，加1会导致溢出，结果会变为最小的负数。

//对于无符号整数溢出：
unsigned int c = 0;
unsigned int d = c - 1; // 当c为0时，减1会导致无符号整数溢出，结果会变为无符号整数的最大值。

//对于浮点数溢出：
float e = 3.402823466e+38F;
float f = e * 2; // 当e是float类型的最大值时，乘以2会导致溢出，结果会变为无穷大（infinity）。
```
## 练习4.8:
### 说明在逻辑与、逻辑或及相等性运算符中运算对象求值的顺序。
答：  
* 逻辑与运算符（&&）：逻辑与运算符遵循“短路求值”或“从左到右求值”的原则。这意味着，如果运算符左侧的表达式的值为false，那么整个逻辑与表达式的结果就确定为false，右侧的表达式将不会被求值或执行。
* 逻辑或运算符（||）：逻辑或运算符也遵循“短路求值”或“从左到右求值”的原则。如果运算符左侧的表达式的值为true，那么整个逻辑或表达式的结果就确定为true，右侧的表达式将不会被求值或执行。
* 相等性运算符（== 和 !=）：对于相等性运算符，C++并没有规定运算对象的求值顺序。也就是说，如果两侧的表达式中有一个或者都有副作用（例如，修改了某个对象的值），那么这个表达式的行为就是未定义的。
## 练习 4.9:
### 解释在下面的if语句中条件部分的判断过程。
```
const char *cp = "Hello Whorld";
if (cp && *cp)
```
答：  
* 这个 if 语句常常用于确保一个字符串指针非空并且指向的字符串非空。
* 首先检查指针 cp 是否为非空,确保 cp 有效地指向了某个内存位置。由于 cp 指向了一个字符串字面量，所以这个条件为真。接下来检查 cp 所指向的字符是否非零，由于 cp 指向的字符是 'H'，所以这个条件也为真。因此，整个 if 条件为真，if 后面的语句将会被执行。
## 练习4.10:
### 为 while循环写一个条件，使其从标准输入中读取整数，遇到42 时停止。
答：  
```
int num = 0;
while (std::cin >> num && num != 42) {
	// 空循环体，只用于读取和检查数字
}
```
## 练习 4.11：
### 书写一条表达式用于测试4 个值 a、b、c、d 的关系，确保 a 大于 b、b大于c、c 大于d。
答：  
```
(a > b) && (b > c) && (c > d)
```
## 练习 4.12:
### 假设i、j和k是三个整数,说明表达式i!=j<k的含义。
答：  
* < 的优先级高于 != ，所以i != j < k 会被解析为 i != (j < k)。
* 这是一个逻辑上的混乱，因为这是在将一个整数 i 与一个布尔值进行比较。如果 i 不等于 0 或 1，那么无论 j < k 的结果如何，i != (j < k) 的结果都是 true。如果 i 等于 0 或 1，那么 i != (j < k) 的结果取决于 j < k 的结果是否与 i 相等。
## 练习 4.13：
### 在下述语句中，当赋值完成后i 和 d 的值分别是多少？
```
int i; double d;
(a) d = i = 3.5;	(b)i = d = 3.5;
```
答：  
* (a)3.0	(b)3
## 练习 4.14:
### 执行下述 if语句后将发生什么情况？
```
if (42 = i) // ...
if (i = 42) // ...
```
答：  
* 由于42不是一个左值，第一条语句尝试对其赋值将会导致编译错误。	  第二条语句是合法的，但可能不是你预期的。i将被赋值为42，由于42是一个非零值，所以它会被视为true，if后面的语句块会被执行。
## 练习4.15:
### 下面的赋值是非法的,为什么?应该如何修改?
```
double dval; int ival; int *pi;
dval = ival = pi = 0;
```
答：  
* 非法的原因在于pi是一个int指针，而不是一个int对象。不能将一个指针类型的值赋给一个int变量，修改如下
```
dval = ival = 0;
pi = nullptr;
```
## 练习 4.16：
### 尽管下面的语句合法，但它们实际执行的行为可能和预期并不一样，为什么？应该如何修改？
```
if (p = getPtr() != 0)
if (i = 1024)
```
答：  
* if (p = getPtr() != 0)的问题在于关系运算符!=的优先级高于赋值运算符=，所以getPtr() != 0会首先被计算，结果是一个布尔值。这个布尔值会赋值给p。如希望p获得getPtr()的返回值，然后再检查p是否非零，需要添加括号以改变运算的顺序。
* if (i = 1024) 中，i被赋值为1024，然后检查i是否非零。由于1024是非零的，所以这个if语句的条件总是为true。如果是想检查i是否等于1024，应该使用比较运算符==，而不是赋值运算符=。
* 修改如下：
```
if ((p = getPtr()) != 0)
if (i == 1024)
```
## 练习 4.17：
### 说明前置递增运算符和后置递增运算符的区别。
答：  
* 区别在于操作顺序和性能。
* 前置递增运算符：首先增加变量的值，然后返回值被修改后的变量。因此将返回一个左值。
* 后置递增运算符：首先创建变量当前值的副本，然后再增加变量的值，最后返回这个变量原始值的副本。因此将返回一个右值。
## 练习 4.18：
### 如果第 132 页那个输出 vector 对象元素的 while 循环使用前置递增运算符，将得到什么结果？
答：  
* 一开始的时候，会跳过第一个元素（因为在访问它之前就已经递增了迭代器）；在迭代到最后一个元素时，递增迭代器到end()的位置，然后试图解引用它，这将是未定义的行为，可能会导致程序崩溃。
## 练习 4.19：
### 假设 ptr 的类型是指向 int 的指针、vec 的类型是 vector\<int>、ival的类型是int,说明下面的表达式是何含义?如果有表达式不正确,为什么?应该如何修改？
```
(a) ptr != 0 && *ptr++	(b)ival++ && ival
(c) vec[ival++] <= vec[ival]
```
答：  
* (a) 这个表达式首先检查 ptr 是否为非空指针。如果 ptr 是非空的，那么将解引用 ptr ，然后将 ptr 自增以指向下一个元素。
* (b) 这个表达式首先执行 ival++ 操作。如果 ival 在递增前的值非零，那么将执行 ival 表达式。
* (c) 这个表达式是错误的，C++没有定义<=的求值顺序，ival++ 的执行会使 ival 的值增加，而在计算 vec\[ival] 时又使用了 ival 的值，该行为是未定义的。修改如下。
```
bool result = vec[ival] <= vec[ival+1];
++ival;
```
## 练习 4.20:
### 假设 iter 的类型是 vector\<string>::iterator，说明下面的表达式是否合法。如果合法，表达式的含义是什么？如果不合法，错在何处？
```
(a) *iter++;		(b) (*iter)++;	(c) *iter.empty();
(d) iter->empty();	(e) ++*iter;	(f) iter++->empty();
```
答：  
* (a) 合法。这个表达式首先创建iter当前值的副本，然后iter自增指向下一个元素。最后将迭代器iter原始值的副本解引用，返回指向的string对象。
* (b) 不合法。\*iter 表示迭代器iter指向的string对象，string对象不能被递增。
* (c) 不合法。这个表达式试图调用iter的empty()成员函数，然后对返回值进行解引用。但是，iter是一个vector\<string>::iterator，它没有empty()成员函数。
* (d) 合法。这个表达式等价于(\*iter).empty();，它检查iter指向的string对象是否为空。
* (e) 不合法。这个表达式试图递增iter指向的string对象，string对象不能被递增。这个表达式等价于++(*iter);。
* (f) 合法。这个表达式调用iter指向的string对象的empty()成员函数,并将迭代器移动到下一个元素。
## 练习 4.21：
### 编写一段程序，使用条件运算符从vector\<int>中找到哪些元素的值是奇数，然后将这些奇数值翻倍。
答：  
```
#include <iostream>
#include <vector>

int main() {
	std::vector<int> vec = { 1,2,3,4,5,6,7,8,9,10 };

	//找出奇数并翻倍
	for (auto&i : vec) {
		i = (i % 2 != 0) ? i * 2 : i;
	}

	//输出修改后的vector
	for (const auto &i : vec) {
		std::cout << i << " ";
	}

	return 0;
}
```
## 练习 4.22：
### 本节的示例程序将成绩划分成 high pass、pass 和fail 三种，扩展该程序使其进一步将 60 分到 75 分之间的成绩设定为 low pass。要求程序包含两个版本：一个版本只使用条件运算符；另外一个版本使用 1 个或多个 if 语句。哪个版本的程序更容易理解呢？为什么？
答：  
* 一般来说， if 语句版本可能更容易理解一些。程序如下：
```
#include <iostream>
#include <string>

int main() {
	std::string finalgrade;
	unsigned grade;

	//使用条件运算符版本
	std::cin >> grade;
	finalgrade = (grade >= 90) ? "high pass"
		: (grade >= 75) ? "pass"
		: (grade >= 60) ? "low pass" : "fail";
	std::cout << finalgrade << std::endl;

	//使用if语句版本
	std::cin >> grade;
	if (grade >= 90) {
		finalgrade = "high pass";
	} else if (grade >= 75) {
		finalgrade = "pass";
	} else if (grade >= 60) {
		finalgrade = "low pass";
	} else {
		finalgrade = "fail";
	}
	std::cout << finalgrade << std::endl;

	return 0;
}
```
## 练习 4.23：
### 因为运算符的优先级问题，下面这条表达式无法通过编译。根据 4.12 节中的表（第 147 页）指出它的问题在哪里？应该如何修改？
```
string s = "word";
string p1 = s + s[s.size() - 1] == 's' ? "" : "s";
```
答：  
* 该表达式实际上是这样被解析的：
```
string p1 = ((s + s[s.size() - 1]) == 's') ? "" : "s";
```
* s + s\[s.size() - 1]会首先执行，然后结果会与's'进行比较。而将一个string与一个char进行比较是不合法的。修改如下：
```
string p1 = s + (s[s.size() - 1] == 's' ? "" : "s");
```
## 练习 4.24:
### 本节的示例程序将成绩划分成high pass、 pass和fail三种,它的依据是条件运算符满足右结合律。假如条件运算符满足的是左结合律，求值过程将是怎样的？
答：  
* 如果条件运算符满足的是左结合律，那么求值过程会发生改变。左结合性意味着我们首先计算 (grade > 90) ? "high pass" : (grade < 60)，这将返回 "high pass" 或者(grade < 60)的布尔值（这取决于grade的值是否大于90）。于是只有grade大于等于60小于等于90时才返回pass，其他情况都返回fail。这将是一个逻辑上的错误。
## 练习 4.25：
### 如果一台机器上 int 占 32 位、char 占 8 位，用的是 Latin-1 字符集，其中字符'q'的二进制形式是 \01110001，那么表达式~'q'<<6 的值是什么?
答：  
* 所有位运算符都会对小整型（比 int 类型小的类型，如 char、short）的操作数进行整型提升。所以答案是-7296 。
## 练习 4.26：
### 在本节关于测验成绩的例子中，如果使用 unsigned int 作为 quiz1 的类型会发生什么情况？
答：  
* 如果你的系统中unsigned int的位数小于28，那么这段代码就会有问题。尝试设置或访问一个不存在的位将导致未定义的行为。
## 练习 4.27：
### 下列表达式的结果是什么？
```
unsigned long ul1 = 3, ul2 = 7;
(a) ul1 & ul2;	(b) ul1 | ul2;
(c) ul1 && ul2;	(d) ul1 || ul2;
```
答：  
* (a) 3	 (b) 7 	(c) true  (d) true
## 练习4.28:
### 编写一段程序，输出每一种内置类型所占空间的大小。
答：  
```
#include <iostream>

using std::cout;
using std::endl;

int main() {
	cout << "char: " << sizeof(char) << "bytes" << endl;
	cout << "short: " << sizeof(short) << "bytes" << endl;
	cout << "int: " << sizeof(int) << "bytes" << endl;
	cout << "long: " << sizeof(long) << "bytes" << endl;
	cout << "long long: " << sizeof(long long) << "bytes" << endl;
	cout << "float: " << sizeof(float) << "bytes" << endl;
	cout << "double: " << sizeof(double) << "bytes" << endl;
	cout << "long double: " << sizeof(long double) << "bytes" << endl;
	cout << "bool: " << sizeof(bool) << "bytes" << endl;

	return 0;
}
```
* 本人电脑输出如下：
```
char: 1bytes
short: 2bytes
int: 4bytes
long: 4bytes
long long: 8bytes
float: 4bytes
double: 8bytes
long double: 8bytes
bool: 1bytes
```
## 练习 4.29：
### 推断下面代码的输出结果并说明理由。实际运行这段程序，结果和你想象的一样吗？如果不一样，为什么？
```
int x[10]; int *p = x;
cout << sizeof(x) / sizeof(*x) << endl;
cout << sizeof(p) / sizeof(*p) << endl;
```
答：  
* sizeof(x) / sizeof(\*x)语句将输出数组的元素个数，即10。	sizeof(p) / sizeof(\*p)语句输出的结果取决于不同的系统，本人电脑输出为2。
## 练习 4.30:
### 根据4.12节中的表(第147页),在下述表达式的适当位置加上括号,使得,加上括号之后表达式的含义与原来的含义相同。
```
(a) sizeof x + y;	(b) sizeof p->mem[i];
(c) sizeof a < b;	(d) sizeof f();
```
答：  
```
(a) (sizeof x) + y;	(b) sizeof ((p->mem)[i]);
(c) (sizeof a) < b;	(d) sizeof (f());
```
## 练习 4.31：
### 本节的程序使用了前置版本的递增运算符和递减运算符，解释为什么要用前置版本而不用后置版本。要想使用后置版本的递增递减运算符需要做哪些改动?使用后置版本重写本节的程序。
答：
* 在这个程序中，使用前置版本的递增和递减运算符会更有效率一些。这是因为后置版本的递增和递减运算符会创建一个临时对象，要使用后置版本的递增和递减运算符，可以这样写：
```
vector<int>::size_type cnt = ivec.size();
// 将把从size到1的值赋给ivec的元素
for (vector<int>::size_type ix = 0;
	ix != ivec.size(); ix++, cnt--)
	ivec[ix] = cnt;
```
## 练习4.32:
### 解释下面这个循环的含义。
```
constexpr int size = 5;
int ia[size] = { 1,2,3,4,5 };
for (int *ptr = ia, ix = 0;
ix != size&&ptr != ia + size;
++ix, ++ptr) {/* ...*/}
```
答：  
* 这是一个使用了两种遍历数组的方式的循环。
## 练习 4.33：
### 根据 4.12 节中的表（第 147 页）说明下面这条表达式的含义。
```
someValue ? ++x, ++y : --x, --y
```
答：  
* 该表达式的含义：(someValue ? (++x, ++y) : --x), --y	 这个表达式逻辑非常混乱，因为条件运算符和逗号都具有求值顺序，并且逗号会丢弃左侧表达式的结果。
## 练习 4.34:
### 根据本节给出的变量定义,说明在下面的表达式中将发生什么样的类型转换:
```
(a) if (fval)	(b) dbvl = fval + ival;	(c) dval + ival*cval;
```
需要注意每种运算符遵循的是左结合律还是右结合律。
答：  
* (a) fval转换成bool	(b) ival转换成flog，计算结果转换成double	(c) cval转成成int，计算结果转换成double
* 运算符的优先级和结合律先于类型转换生效。
## 练习 4.35：
### 假设有如下的定义
```
char cval;	int ival;	unsigned int ui;
float fval;	double dval;
```
请回答在下面的表达式中发生了隐式类型转换吗？如果有，指出来。
```
(a) cval = 'a' + 3;	(b) fval = ui - ival * 1.0;
(c) dval = ui * fval;	(d) cval = ival + fval + dval;
```
答：  
* (a) 'a'从char转换为int，和3相加后结果转换为char。
* (b)ival转换为douvle，和1.0相乘后ui转换为double，减去之前的相乘结果然后将结果转换为float 。
* (c) ui转换为float，和fvla相乘后结果转换为double。
* (d) ival转换为float，和fval相加后结果转换为double，再和dval相加后，将结果丢弃小数转换为char。
## 练习 4.36：
### 假设 i 是 int 类型，d 是 double 类型，书写表达式 i*=d 使其执行整数类型的乘法而非浮点类型的乘法。
答：  
* i \*= static_cast\<int>(d);
## 练习4.37:
### 用命名的强制类型转换改写下列旧式的转换语句。
int i; double d; const string \*ps; char \*pc; void \*pv;
(a) pv = (void*)ps;	(b) i = int(\*pc);
(c) pv = &d;		(d) pc = (char*)pv;
答：  
* (a) pv = static_cast\<void*>(const_cast<string*>(ps));	(b) i = static_cast\<int>(*pc);
* (c) pv = static_cast\<void*>(&d);				(d) pc = static_cast\<char*>(pv);
## 练习 4.38：
### 说明下面这条表达式的含义。
```
double slope = static_cast<double>(j / i);
```
答：  
* 先计算j/i的结果，然后将该值转换为double类型，最后用这个值对slope进行初始化。
