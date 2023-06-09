## 练习2.1:  
### 类型 int、 long、 long long和short的区别是什么?无符号类型和带符号类型的区别是什么? float和double的区别是什么?  
答： 
* int、long、long long 和 short 的区别： 这些都是整数类型，区别在于它们所能表示的数值范围不同。C++ 标准规定了它们的最小尺寸，但实际尺寸可能因编译器和硬件而异。一般来说，short 至少为 16 位，int 至少为 16 位（通常为 32 位），long 至少为 32 位（通常为 32 或 64 位），而 long long 至少为 64 位。  
* 无符号类型和带符号类型的区别： 带符号类型可以表示正数、负数和零。无符号类型只能表示非负数（包括零）。这是因为无符号类型将所有可用位用于表示正数值，而带符号类型则保留一个位用于表示数值的正负。因此，在相同的位宽下，无符号类型能表示的正数范围比带符号类型大。  
* float 和 double 的区别： 这两种类型都是浮点数类型，用于表示实数。它们的区别在于所能表示的数值范围和精度。float 通常有 32 位，而 double 通常有 64 位。由于 double 类型具有更多的位宽，因此它能表示的数值范围更大，精度也更高。然而，尽管 double 的精度更高，但其计算代价却与 float 相差无几，在某些机器上，双精度运算甚至比单精度运算还快。因此，在实际应用中通常选择 double 类型。  
## 练习 2.2:  
### 计算按揭贷款时,对于利率、本金和付款分别应选择何种数据类型?说明你,的理由。  
答：  
* 都可以选择 double 类型。这三者通常都是一个小数，需要有一定的精度来表示。使用 double 类型可以提供足够的精度，同时计算代价与使用 float 类型相差无几。  
## 练习 2.3：  
### 读程序写结果。  
```
  unsigned u = 10, u2 = 42;
	std::cout << u2 - u << std::endl;
	std::cout << u - u2 << std::endl;

	int i = 10, i2 = 42;
	std::cout << i2 - i << std::endl;
	std::cout << i - i2 << std::endl;
	std::cout << i - u << std::endl;
	std::cout << u - i << std::endl;
```
答：  
* 结果如下：  
```
32
4294967264
32
-32
0
0
```
## 练习 2.4：  
### 编写程序检查你的估计是否正确，如果不正确，请仔细研读本节直到弄明白问题所在。  
答：  
* 预估正确。虽然对那两个0没什么信心，但经过测试确实没出什么幺蛾子。  
## 练习 2.5：  
### 指出下述字面值的数据类型并说明每一组内几种字面值的区别：  
```
(a) 'a', L'a', "a", L"a"
(b) 10, 10u, 10L, 10uL, 012, 0xC
(c) 3.14, 3.14f, 3.14L
(d) 10, 10u, 10., 10e-2
```
答：  
```
(a) 
'a': char 类型，表示一个字符；
L'a': wchar_t 类型，表示一个宽字符；
"a": 字符串字面值，实际上是一个以空字符 '\0' 结尾的 char 数组；
L"a": 宽字符串字面值，实际上是一个以空字符 L'\0' 结尾的 wchar_t 数组。
(b) 
10: int 类型，十进制整数；
10u: unsigned int 类型，十进制无符号整数；
10L: long 类型，十进制长整数；
10uL: unsigned long 类型，十进制无符号长整数；
012: int 类型，八进制整数；
0xC: int 类型，十六进制整数。
(c) 
3.14: double 类型，双精度浮点数；
3.14f: float 类型，单精度浮点数；
3.14L: long double 类型，长双精度浮点数。
(d) 
10: int 类型，十进制整数；
10u: unsigned int 类型，十进制无符号整数；
10.: double 类型，双精度浮点数，等同于 10.0；
10e-2: double 类型，双精度浮点数，等同于 10 * 10^(-2)。
```
## 练习 2.6：  
### 下面两组定义是否有区别,如果有,请叙述之  
```
int month = 9, day = 7;
int month= 09, day= 07;
```
答：  
*  有区别。第一行用来初始化的值都是十进制整数，第二行用来初始化的值都是八进制整数，而且09是一个无效的八进制数字，会产生编译错误。  
## 练习 2.7：  
### 下述字面值表示何种含义？它们各自的数据类型是什么？  
```
(a) "Who goes with F\145rgus?\012"
(b) 3.14e1L
(c) 1024f
(d) 3.14L
```
答：  
```
(a)输出 Who goes with Fergus? 并换行。	string 类型。
(b)输出 3.14 	long double 类型
(c)会产生编译错误。 后缀f只对浮点字面值有效。
(d)输出 3.14 	long double 类型
```
## 练习2.8:  
### 请利用转义序列编写一段程序,要求先输出2M,然后转到新一行。修改程序使其先输出 2，然后输出制表符，再输出 M，最后转到新一行。  
答：  
```
#include <iostream>
int main()
{
	std::cout << "\x32\x4D\xA";
	return 0;
}
```
* 修改后：  
```
#include <iostream>
int main()
{
	std::cout << "\x32\x9\x4D\xA";
	return 0;
}
```
## 练习 2.9：  
### 解释下列定义的含义。对于非法的定义，请说明错在何处并将其改正。  
```
(a) std::cin >> int input_value;
(b) int i = { 3.14 };
(c) double salary = wage = 9999.99; 
(d) int i = 3.14;
```
答： 
* (a) std::cin >> int input_value;  
* 这一行试图从标准输入读取一个整数并将其存储在名为 input_value 的变量中。但是，这里的定义是非法的，因为在 std::cin 读取之前应该先声明变量。修正如下：  
```
int input_value;
std::cin >> input_value;
```
* (b) int i = { 3.14 };  
* 这一行试图将浮点数 3.14 赋值给整数类型变量 i。这是不合法的，因为使用花括号进行列表初始化时，编译器不允许可能丢失信息的转换。改正如下：  
```
double i = { 3.14 };
```
* (c) double salary = wage = 9999.99;  
* 这一行试图将 9999.99 赋值给名为 salary 和 wage 的两个 double 类型变量。然而，这里的定义是非法的，因为在将值赋给 salary 和 wage 之前应该先声明 wage。修正如下：  
```
double wage = 9999.99;
double salary = wage;
```
* (d) int i = 3.14;
* 这一行将浮点数 3.14 赋值给整数类型变量 i。虽然这种赋值是合法的，但会导致精度损失，因为浮点数会被截断为整数。在这种情况下，i 的值将为 3。如果想保留小数部分，应该使用浮点数类型：  
```
double i = 3.14;
```
## 练习2.10:  
### 下列变量的初值分别是什么?  
```
std::string global_str;
int global_int;
int main()
{
    int local_int;
    std::string local_str;
}
```
答：  
* global_str 初始化为空字符串。  
* global_int 初始化为0。  
* local_int 并未初始化，值是未定义的。  
* local_str 默认初始化为空字符串。  
## 练习 2.11:  
### 指出下面的语句是声明还是定义:  
```
(a) extern int ix = 1024;
(b) int iy;
(c) extern int iz;
```
答：  
* (a) 定义。
* (b) 定义。
* (c) 声明。
## 练习 2.12：  
### 请指出下面的名字中哪些是非法的？  
```
(a) int double = 3.14;
(b) int _;
(c) int catch-22;
(d) int 1_or_2 = 1;
(e) double Double = 3.14;
```
答：  
* (a) 非法 (c) 非法 (d) 非法。
## 练习 2.13：  
### 下面程序中j的值是多少？
```
int i = 42;
int main()
{
    int i = 100;
    int j = i;
}
```
答：  
* j的值是100。
## 练习 2.14：  
### 下面的程序合法吗？如果合法，它将输出什么？  
```
	int i = 100, sum = 0;
	for (int i = 0; i != 10; ++i)
		sum += i;
	std::cout << i << " " << sum << std::endl;
```
答：  
* 合法，输出：100 45。
## 练习2.15：  
### 下面的哪个定义是不合法的？为什么？  
```
(a) int ival = 1.01;
(b) int &rval1 = 1.01;
(c) int &rval2 = ival;
(d) int &rval3;
```
答： 
* (a) 合法。
* (b) 不合法，不能对字面值进行引用。
* (c) 合法。
* (d) 不合法，引用必须被初始化。
## 练习 2.16：  
### 考查下面的所有赋值然后回答：哪些赋值是不合法的？为什么？哪些赋值是合法的？它们执行了什么样的操作？  
```
int i = 0, &r1 = i;
double d = 0, &r2 = d;
(a) r2 = 3.14159;
(b) r2 = r1;
(c) i = r2;
(d) r1 = d;
```
答：  
* 全都合法。
* (a) 相当于d赋值为3.14159。
* (b) 相当于d=i
* (c) 相当于i=d
* (d) 相当于i=d
## 练习 2.17：  
### 执行下面的代码段将输出什么结果？  
```
	int i, &ri = i;
	i = 5; ri = 10;
	std::cout << i << " " << ri << std::endl;
```
答：  
输出为：10 10
## 练习2.18:  
### 编写代码分别更改指针的值以及指针所指对象的值。  
答：  
```
#include <iostream>
int main()
{
	int valOne = 0, valTwo = 0,*pVal=&valOne;
	pVal = &valTwo;		//Change pVal 's value
	*pVal = 2;		//Change valTwo 's value
	return 0;
}
```
## 练习 2.19：  
### 说明指针和引用的主要区别。  
答：  
* 1\. 本质上，指针是一个对象，它存储了一个地址值。这个地址值通常指向另一个对象。而引用并不是一个独立的对象，它只是某个已存在对象的别名。
* 2\. 引用必须在定义时进行初始化，它所引用的对象不能被改变。而指针在定义时可以不进行初始化，可以在任何时候改变所指向的对象。
* 3\. 访问引用时，无需解引用操作，直接使用引用名就可以访问所引用的对象。而访问指针所指向的对象时，需要使用解引用操作符（*）。
* 4\. 指针可以有空值（nullptr或者0），表示它不指向任何对象。而引用必须绑定到一个对象，不存在空引用的概念。
* 5\. 指针可以进行指针运算，例如递增、递减或者计算两个指针的差值等。而引用不支持这些运算。
## 练习 2.20：  
### 请叙述下面这段代码的作用。  
```
	int i = 42;
	int *p1 = &i;
	*p1 = *p1 * *p1;
```
答：  
* i 赋值为 i * i。
## 练习 2.21：  
### 请解释下述定义。在这些定义中有非法的吗？如果有，为什么？  
```
int i = 0;
(a) double* dp = &i;
(b) int *ip = i;
(c) int *p = &i;
```
答：  
* (a)非法。试图用 int 型对象的地址初始化 double 型指针。
* (b)非法。没有使用取地址符&，不能把 int 变量直接赋给指针。
* (c)合法。
## 练习 2.22：  
### 假设 p 是一个 int 型指针，请说明下述代码的含义。  
```
if (p) // ...
if (*p) // ...
```
答： 
```
if (p) // 判断 p 是不是空指针。
if (*p) // 判断 p 指向的对象的值是不是0。
```
## 练习 2.23：  
### 给定指针 p，你能知道它是否指向了一个合法的对象吗？如果能，叙述判断的思路；如果不能，也请说明原因。  
答：  
* 不能。指针 p 可能指向一个合法对象，也可能指向一个无效的内存地址。除非指针 p 是一个空指针，否则在大多数情况下，不能仅通过检查指针 p 本身来确定它是否指向一个合法的对象。
## 练习2.24:  
### 在下面这段代码中为什么p合法而lp非法?  
```
	int i = 42;
	void *p = &i;
	long *lp = &i;
```
答：  
* void* 是一种特殊的指针类型，可用于存放任意对象的地址，而long 类型的指针不可以指向 int 类型对象。
## 练习 2.25：  
### 说明下列变量的类型和值。  
```
(a) int* ip,i, &r = i;
(b) int i, *ip = 0;
(c) int* ip, ip2;
```
答：  
* (a) ip 是 int 型指针，值为：未定义。i 是 int 型变量，值为：未定义。r 是 int 型引用，值为：未定义（i 的值）。
* (b) i 是 int 型变量，值为：未定义。ip 是 int 型指针，值为：0（空指针）。
* (c) ip 是 int 型指针，值为：未定义。ip2 是 int 型变量，值为：未定义。
## 练习 2.26：  
### 下面哪些句子是合法的？如果有不合法的句子，请说明为什么？  
```
	(a)const int buf;
	(b)int cnt = 0;
	(c)const int sz = cnt;
	(d)++cnt; ++sz;
```
答：  
* (a)不合法。常量 buf 必须被初始化。
* (b)合法。
* (c)合法。
* (d)不合法。不能给常量赋值。
## 练习 2.27：  
### 下面的哪些初始化是合法的？请说明原因。  
```
(a)int i = -1, &r = 0;
(b)int *const p2 = &i2;
(c)const int i = -1, &r = 0;
(d)const int *const p3 = &i2;
(e)const int *p1 = &i2;
(f)const int &const r2;
(g)const int i2 = i, &r = i;
```
答：  
* (a)非法，r 必须引用一个 int 变量。
* (b)非法，p2 必须指向一个 int 变量。
* (c)合法，r 可以对字面值常量进行引用。
* (d)合法，p3 可以指向常量或非常量。
* (e)合法，p1 可以指向常量或非常量。
* (f)非法，引用必须被初始化。
* (g)合法，r 可以引用常量或非常量。
## 练习 2.28:  
### 说明下面的这些定义是什么意思,挑出其中不合法的。  
```
(a)int i, *const cp;
(b)int *p1, *const p2;
(c)const int ic, &r = ic;
(d)const int *const p3;
(e)const int *p;
```
答：  
* (a)声明定义一个 int 类型变量 i 和一个 int 类型 常量指针cp。	不合法，常量指针 cp 必须被初始化。
* (b)声明定义一个 int 类型指针 p1 和一个 int 类型 常量指针 p2 。	不合法，常量指针 p2 必须被初始化。
* (c)声明定义一个 int 类型常量 ic 和一个对 int 类型常量的引用 r。	不合法，常量 ic 必须被初始化。
* (d)声明定义一个指向 int 类型常量的常量指针。	不合法，常量指针 p3 必须被初始化。
* (e)声明定义一个指向 int 类型常量的指针。	合法。
## 练习2.29:  
### 假设已有上一个练习中定义的那些变量，则下面的哪些语句是合法的?请说明原因。  
```
(a)i = ic;
(b)p1 = p3;
(c)p1 = &ic;
(d)p3 = &ic;
(e)p2 = p1;
(f)ic = *p3;
```
答：  
* (a)合法，将常量 ic 的值拷贝给变量 i。
* (b)非法，不能给普通指针 p1 赋值指向常量的指针的值。
* (c)非法，不能给普通指针 p1 赋值常量的地址。
* (d)非法，不能给常量指针 p3 赋值。
* (e)非法，不能给常量指针 p2 赋值。
* (f)非法，不能给常量 ic 赋值。
## 练习 2.30：  
### 对于下面的这些语句，请说明对象被声明成了顶层 const 还是底层 const?  
```
	const int v2 = 0;
	int v1 = v2;
	int *p1 = &v1, &r1 = v1;
	const int *p2 = &v2, *const p3 = &i, &r2 = v2;
```
答：  
* v2 是顶层 const，r1 是顶层 const，p2 是底层 const，p3 既是顶层 const 也是底层 const，r2 既是顶层 const 也是底层 const。
## 练习 2.31：  
### 假设已有上一个练习中所做的那些声明，则下面的哪些语句是合法的？请说明顶层 const 和底层 const 在每个例子中有何体现。  
```
	r1 = v2;
	p1 = p2;
	p2 = p1;
	p1 = p3;
	p2 = p3;
```
答：  
* r1 = v2; 合法，允许对顶层 const 的值拷贝。
* p1 = p2; 非法，不允许将底层 const 的值拷贝给非底层 const。
* p2 = p1; 合法，允许将非底层 const 的值拷贝给底层 const。
* p1 = p3; 非法，不允许将底层 const 的值拷贝给非底层 const。
* p2 = p3; 合法，允许将底层 const 的值拷贝给底层 const。 p3 同时是顶层 const，顶层 const允许拷出。
## 练习 2.32:  
### 下面的代码是否合法？如果非法,请设法将其修改正确。  
```
int null = 0, *p = null;
```
答：  
* 非法，即使值为0也不能把 int 变量直接赋给指针，初始化空指针可以使用nullptr 。以下为修正：
```
int null = 0, *p = nullptr;
```
## 练习2.33:  
### 利用本节定义的变量,判断下列语句的运行结果。  
```
	a = 42; b = 42; c = 42; 
	d = 42; e = 42; g = 42;
```
答：  
* a = 42;合法 	 b = 42;合法	 c = 42;合法
* d = 42;非法	 e = 42;非法	 g = 42;非法
## 练习 2.34：  
### 基于上一个练习中的变量和语句编写一段程序，输出赋值前后变量的内容，你刚才的推断正确吗？如果不对，请反复研读本节的示例直到你明白错在何处为止。  
答：
* 正确。
## 练习 2.35：  
### 判断下列定义推断出的类型是什么，然后编写程序进行验证。  
```
	const int i = 42;
	auto j = i; const auto &k = i; auto *p = &i;
	const auto j2 = i, &k2 = i;
```
答：  
* j 是一个int ， k 是一个 const& ， p 是一个const int* ， j2 是一个const int ， k2 是一个 const& 。
## 练习2.36:  
### 关于下面的代码,请指出每一个变量的类型以及程序结束时它们各自的值。  
```
	int a = 3, b = 4;
	decltype(a) c = a;
	decltype((b))d = a;
	++c;
	++d;
```
答：  
* a 是 int 值为：4 ，   b 是 int 值为：4 ，  c 是 int 值为：4 ，  d 是 int& 值为：4 。
## 练习 2.37：
### 赋值是会产生引用的一类典型表达式，引用的类型就是左值的类型。也就是·说,如果i是int,则表达式i=x的类型是 int&。根据这一特点,请指出下面的代码中每一个变量的类型和值。
```
	int a = 3, b = 4;
	decltype(a) c = a;
	decltype(a = b)d = a;
```
答：
* a 是 int 值为：3 ，   b 是 int 值为：4 ，  c 是 int 值为：3 ，  d 是 int& 值为：3 。
## 练习 2.38：
### 说明由 decltype 指定类型和由 auto 指定类型有何区别。请举出一个例子, decltype指定的类型与 auto指定的类型一样;再举一个例子, decltype指定的类型与 auto 指定的类型不一样。
答：  
* decltype 会分析表达式并返回该表达式的类型，而不会执行表达式。decltype 更注重于保持类型信息的完整性，它可以推导出更精确的类型，包括引用和 const 限定符。
* auto 会根据变量的初始化表达式推导出变量的类型。auto 会自动忽略顶层 const 限定符和引用，因此它推导出的类型可能与原始类型略有不同。
* 例子1：decltype 和 auto 推导出相同的类型：
```
int a = 42;
auto b = a; // b 的类型为 int
decltype(a) c = a; // c 的类型为 int
```
* 例子2：decltype 和 auto 推导出不同的类型：
```
int x = 42;
const int &y = x;
auto z = y; // z 的类型为 int，因为 auto 会忽略 const 和引用
decltype(y) w = y; // w 的类型为 const int&，因为 decltype 保留了 const 和引用
```
## 练习 2.39： 
### 编译下面的程序观察其运行结果，注意，如果忘记写类定义体后面的分号会发生什么情况？记录下相关信息，以后可能会有用。
```
struct Foo{/* 此处为空 */} //注意： 没有分号
int main()
{
	return 0;
}
```
答：  
* 相关报错如下：
```
prog1.cpp(2): error C2628: “Foo”后面接“int”是非法的(是否忘记了“;”?)
prog1.cpp(3): error C3874: “main”的返回类型应为“int”而非“Foo”
prog1.cpp(4): error C2440: “return”: 无法从“int”转换为“Foo”
prog1.cpp(4): note: 无构造函数可以接受源类型，或构造函数重载决策不明确
```
## 练习 2.40：
### 根据自己的理解写出 sales_data类，最好与书中的例子有所区别。
答：
```
struct Sales_data { 
	std::string itemID; 
	std::string itemName;
	int quantity{ 0 };
	double price{ 0.0 };
	double income{ 0.0 };
};
```
## 练习 2.41
### 使用你自己的Sale_data类重写 1.20~1.25 的练习。眼下先把Sales_data类的定义和main函数放在一个文件里。
答：  
* 练习 1.20
```
#include <iostream>
#include <string>
struct Sales_data {
	std::string itemID;
	std::string itemName;
	int quantity{ 0 };
	double price{ 0.0 };
	double income{ 0.0 };
};
int main()
{
	Sales_data data;
	while (std::cin >> data.itemID >> data.quantity >> data.price)
		std::cout << data.itemID << " "
		<< data.quantity << " "
		<< data.quantity*data.price << " "
		<< (data.quantity*data.price) / data.quantity << std::endl;
	return 0;
}
```
* 练习 1.21
```
#include <iostream>
#include <string>
struct Sales_data {
	std::string itemID;
	std::string itemName;
	int quantity{ 0 };
	double price{ 0.0 };
	double income{ 0.0 };
};
int main()
{
	std::cout << "Enter two sales records with the same ISBN: " << std::endl;
	Sales_data dataOne, dataTwo;
	int totalCnt = 0;
	double totalIncome = 0.0;

	if (std::cin >> dataOne.itemID >> dataOne.quantity >> dataOne.price
		>> dataTwo.itemID >> dataTwo.quantity >> dataTwo.price) {
		dataOne.income = dataOne.quantity*dataOne.price;
		dataTwo.income = dataTwo.quantity*dataTwo.price;

		if (dataOne.itemID == dataTwo.itemID) {
			totalCnt = dataOne.quantity + dataTwo.quantity;
			totalIncome = dataOne.income + dataTwo.income;
			std::cout << "The total sales record is: " << dataOne.itemID << " "
				<< totalCnt << " " << totalIncome << " ";
			if (totalCnt != 0)
				std::cout << totalIncome / totalCnt << std::endl;
			else
				std::cout << "(no sales)" << std::endl;
			return 0;
		}else
			std::cerr << "These two sales records have different ISBN." << std::endl;
		return -1;
	}
}
```
* 练习 1.22
```
#include <iostream>
#include <string>
struct Sales_data {
	std::string itemID;
	std::string itemName;
	int quantity{ 0 };
	double price{ 0.0 };
	double income{ 0.0 };
};
int main()
{
	Sales_data dataTotal;
	if (std::cin >> dataTotal.itemID >> dataTotal.quantity >> dataTotal.price) {
		dataTotal.income = dataTotal.quantity*dataTotal.price;

		Sales_data data;
		while (std::cin >> data.itemID >> data.quantity >> data.price) {
			data.income = data.quantity*data.price;
			if (dataTotal.itemID == data.itemID) {
				dataTotal.quantity += data.quantity;
				dataTotal.income += data.income;
			}
			else {
				std::cout << dataTotal.itemID << " " << dataTotal.quantity << " "
					<< dataTotal.income << " " << dataTotal.income / dataTotal.quantity << std::endl;
				dataTotal.itemID = data.itemID;
				dataTotal.quantity = data.quantity;
				dataTotal.income = data.income;
			}
		}
		std::cout << dataTotal.itemID << " " << dataTotal.quantity << " "
			<< dataTotal.income << " " << dataTotal.income / dataTotal.quantity << std::endl;
	}
	return 0;
}
```
* 练习 1.23
```
#include <iostream>
#include <string>
struct Sales_data {
	std::string itemID;
	std::string itemName;
	int quantity{ 0 };
	double price{ 0.0 };
	double income{ 0.0 };
};
int main()
{
	Sales_data currData;
	if (std::cin >> currData.itemID >> currData.quantity >> currData.price) {
		currData.income = currData.quantity*currData.price;

		Sales_data data;
		int cnt = 1;
		while (std::cin >> data.itemID >> data.quantity >> data.price) {
			data.income = data.quantity*data.price;
			if (currData.itemID == data.itemID)
				++cnt;
			else {
				std::cout << currData.itemID << " occurs "
					<< cnt << " times " << std::endl;
				currData.itemID = data.itemID;
				cnt = 1;			
			}
		}
		std::cout << currData.itemID << " occurs "
			<< cnt << " times " << std::endl;
		currData.itemID = data.itemID;
	}
	return 0;
}
```
* 练习 1.24
```
输入如下：
1-333-55555-X 8 8 1-333-55555-X 8 8 1-333-55555-X 8 7 1-331-55554-T 7 7
输出如下：
1-333-55555-X occurs 3 times
1-331-55554-T occurs 1 times
```
* 练习 1.25
```
#include <iostream>
#include <string>
struct Sales_data {
	std::string itemID;
	std::string itemName;
	int quantity{ 0 };
	double price{ 0.0 };
	double income{ 0.0 };
};
int main()
{
	Sales_data dataTotal;
	if (std::cin >> dataTotal.itemID >> dataTotal.quantity >> dataTotal.price) {
		dataTotal.income = dataTotal.quantity*dataTotal.price;

		Sales_data data;
		while (std::cin >> data.itemID >> data.quantity >> data.price) {
			data.income = data.quantity*data.price;
			if (dataTotal.itemID == data.itemID) {
				dataTotal.quantity += data.quantity;
				dataTotal.income += data.income;
			}
			else {
				std::cout << dataTotal.itemID << " " << dataTotal.quantity << " "
					<< dataTotal.income << " " << dataTotal.income / dataTotal.quantity << std::endl;
				dataTotal.itemID = data.itemID;
				dataTotal.quantity = data.quantity;
				dataTotal.income = data.income;
			}
		}
		std::cout << dataTotal.itemID << " " << dataTotal.quantity << " "
			<< dataTotal.income << " " << dataTotal.income / dataTotal.quantity << std::endl;
	}
	else {
		std::cerr << "No data?!" << std::endl;
		return -1;
	}
	return 0;
}
```
## 练习2.42:
### 根据你自己的理解重写一个sales_data.h头文件,并以此为基础重做练习 2.41。
答：  
* 头文件内容如下，练习包含头文件，删除源文件内Sales_data类，其他内容同上。
```
#ifndef SALES_DATA_H
#define SALES_DATA_H
#include <string>
struct Sales_data {
	std::string itemID;
	std::string itemName;
	int quantity{ 0 };
	double price{ 0.0 };
	double income{ 0.0 };
};
#endif
```
