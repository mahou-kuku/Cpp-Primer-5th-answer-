## 练习 1.1:  
### 查阅你使用的编译器的文档,确定它所使用的文件命名约定。编译并运行第2 页的 main 程序。  
答：  
```
所用编译器：Visual Studio 2015  
文件名后缀：.cpp  
开始菜单里找到“Visual Studio 2015”文件夹里面有“VS2015 开发人员命令提示”可以打开命令行，编译命令：CL prog1.cpp  
我刚打开命令行显示“PowerShell不是内部或外部命令”，右键计算机图标-属性-高级系统设置-环境变量-对着path双击把“变量值”全复制到笔记本里，  
在最后加上   %SystemRoot%\system32;%SystemRoot%;%SystemRoot%\System32\Wbem;%SYSTEMROOT%\System32\WindowsPowerShell\v1.0\;  
注意本来的“变量值”最后面有没有分号【;】没有加上分号再加上这段，复制，替换掉“变量值”栏里本来的内容，一路确定就可以用了。  
```
## 练习 1.2:  
### 改写程序，让它返回-1。返回值-1 通常被当作程序错误的标识。重新编译并运行你的程序，观察你的系统如何处理 main 返回的错误标识。   
答：  
* Windows7什么提示都没有  
* 使用 echo %errorlevel% 命令后，显示 -1  
## 练习1.3：  
### 编写程序，在标准输出上打印Hello, World。  
```
#include <iostream>
int main() 
{
	std::cout << "Hello， World。" << std::endl;
	return 0;
}
```
## 练习 1.4：  
### 我们的程序使用加法运算符+来将两个数相加。编写程序使用乘法运算符*，来打印两个数的积。  
答：  
```
#include <iostream>
int main()
{
	std::cout << "Enter two numbers:" << std::endl;
	int valueOne = 0, valueTwo = 0;
	std::cin >> valueOne >> valueTwo;
	std::cout << "The product of " << valueOne << " and " << valueTwo << " is " << valueOne * valueTwo << std::endl;
	return 0;
}
```
## 练习 1.5：  
### 我们将所有输出操作放在一条很长的语句中。重写程序，将每个运算对象的打印操作放在一条独立的语句中。  
答：  
```
#include <iostream>
int main()
{
	std::cout << "Enter two numbers:";
	std::cout << std::endl;
	int valueOne = 0, valueTwo = 0;
	std::cin >> valueOne >> valueTwo;
	std::cout << "the sum of ";
	std::cout << valueOne;
	std::cout << " and ";
	std::cout << valueTwo;
	std::cout << " is ";
	std::cout << valueOne + valueTwo;
	std::cout << std::endl;
	return 0;
}
```
## 练习 1.6:  
### 解释下面程序片段是否合法。  
```
std::cout << "The sum of " << v1;
<< " and " << v2;
<< " is " << v1 + v2 << std::endl;
```
### 如果程序是合法的,它输出什么？如果程序不合法,原因何在?应该如何修正?  
答：  
* 不合法，多了两个;或者少了两个std::cout  
* 以下是一种修正方案。  
```
std::cout << "The sum of " << v1
	<< " and " << v2
	<< " is " << v1 + v2 << std::endl;
```
## 练习 1.7：  
### 编译一个包含不正确的嵌套注释的程序，观察编译器返回的错误信息。  
答：  
```
/*Comment pair/*A comment pair appear inside another*/*/
```
错误信息:  
```
prog1.cpp(4): warning C4138: 在注释外找到“*/”
prog1.cpp(4): error C2059: 语法错误:“/”
```
## 练习 1.8：  
### 指出下列哪些输出语句是合法的（如果有的话)：  
```
std::cout << "/*";
std::cout << "*/";
std::cout << /* "*/" */;
std::cout << /* "*/" /* "/*" */;
```
### 预测编译这些语句会产生什么样的结果，实际编译这些语句来验证你的答案(编写一个小程序，每次将上述一条语句作为其主体)，改正每个编译错误。  
答：  
* 第一句正确。输出为：/*  
* 第二句正确。输出为：*/  
* 第三句错误。以下为改正：  
```
std::cout << /* "*/" */";
```
* 输出为：*/  
* 第四句正确。输出的是中间的第三个注释界定符，输出为： /*   
## 练习 1.9：  
### 编写程序，使用 while 循环将 50 到 100 的整数相加。  
答：  
```
#include <iostream>
int main()
{
	int sum = 0, val = 50;
	while (val <= 100) {
		sum += val;	
		++val;
	}
	std::cout << "Sum of 50 to 100 inclusive is " << sum << std::endl;
	return 0;
}
```
## 练习 1.10：  
### 除了++运算符将运算对象的值增加 1 之外，还有一个递减运算符（—）实现将值减少 1。 编写程序，使用递减运算符在循环中按递减顺序打印出 10 到 0 之间的整数。  
答：  
```
#include <iostream>
int main()
{
	int val = 9;
	while (val >= 1) {
		std::cout << "Countdown: " << val << std::endl;
		--val;
	}
	return 0;
}
```
## 练习 1.11:  
### 编写程序,提示用户输入两个整数,打印出这两个整数所指定的范围内的所有整数。  
答：  
```
#include <iostream>
int main()
{
	std::cout << "Enter two numbers to specify putout range: " << std::endl;
	int start = 0, end = 0;
	std::cin >> start >> end;
	while (start <= end) {
		std::cout << "Putout: " << start << std::endl;
		++start;
	}
	return 0;
}
```
## 练习 1.12：  
### 下面的 for 循环完成了什么功能？ sum 的终值是多少？  
```
int sum = 0;
for (int i = -100; i <= 100; ++i)
sum += i;
```
答：  
* 将从-100到100的每个数相加，sum的终值是0。  
## 练习1.13:  
### 使用for循环重做1.4.1节中的所有练习(练习 1.9、练习 1.10和练习 1.11)。  
答：  
* 练习 1.9  
```
#include <iostream>
int main()
{
	int sum = 0;
	for (int val = 50; val <= 100; ++val)
		sum += val;
	std::cout << "Sum of 50 to 100 inclusive is " << sum << std::endl;
	return 0;
}
```
* 练习 1.10  
```
#include <iostream>
int main()
{
	int val = 9;
	for (int val = 9; val >= 1; --val)
		std::cout << "Countdown: " << val << std::endl;
	return 0;
}
```
* 练习 1.11  
```
#include <iostream>
int main()
{
	std::cout << "Enter two numbers to specify putout range: " << std::endl;
	int start = 0, end = 0;
	std::cin >> start >> end;
	for (; start <= end; ++start)
		std::cout << "Putout: " << start << std::endl;
	return 0;
}
```
## 练习 1.14：  
### 对比 for 循环和 while 循环，两种形式的优缺点各是什么？  
答：  
* for 循环通常更适合具有固定次数的循环，而 while 循环在循环次数不确定的情况下更加灵活。  

* for 循环的优点：  
1. 结构清晰：for 循环的初始化、条件检测和更新操作都在一个语句中，使得循环的逻辑更加明确。  
2. 适用于有固定次数的循环：当知道循环需要执行的确切次数时，for 循环会更加简洁易懂。  
3. 可以在语句里使用计数器：for 循环可以方便地使用循环变量，比如用来计数。  
* for 循环的缺点：  
对于不确定次数的循环，for 循环可能不太适用，因为for 循环的语法结构可能无法清晰地表示循环条件。  
* while 循环的优点：  
1. 灵活性：while 循环适用于不确定次数的循环，只要满足条件就会一直执行循环体。  
2. 对于复杂的循环条件，while 循环可能更容易理解和实现。  
* while 循环的缺点：  
对于有固定次数的循环，while 循环可能比 for 循环更繁琐，需要在循环体内部手动更新循环条件。  
## 练习 1.15：  
### 编写程序，包含第 14 页“再探编译”中讨论的常见错误。熟悉编译器生成的错误信息。
答：  
* 语法错误(syntax error):  
```
#include <iostream>
int main()
{
	std::cout<<"Hurry!"		//没有写; 分号
	return 0;
}
```
* 类型错误(type error):  
```
int main()
{
	int val = 0;
	val = "Hello";		//给int类型变量赋值字符串字面值常量
	return 0;
}
```
* 声明错误（declaration error）：  
```
#include <iostream>
int main()
{
	int val = 0;
	std::cin >> va1;	//val写成了va1
	return 0;
}
```
## 练习 1.16：  
### 编写程序，从 cin读取一组数，输出其和。  
答：  
```
#include <iostream>
int main()
{
	int sum = 0, value = 0;
	while (std::cin >> value)
		sum += value;
	std::cout << "Sum is: " << sum << std::endl;
	return 0;
}
```
## 练习 1.17：  
### 如果输入的所有值都是相等的，本节的程序会输出什么？如果没有重复值，输出又会是怎样的？  
答：  
输入的所有值都是相等的，则输出该值以及重复输入的次数。(需要在输入的结尾添加文件结束符)  
如果没有重复值，则依次输出输入的所有值，以及这些值的计次均为1次。(需要在输入的结尾添加文件结束符)  
## 练习 1.18：  
### 编译并运行本节的程序，给它输入全都相等的值。再次运行程序，输入没有重复的值。  
答：  
* 本节程序如下：  
```
#include <iostream>
int main()
{
	int currVal = 0, val = 0;
	if (std::cin >> currVal) {
		int cnt = 1;
		while (std::cin >> val) {
			if (val == currVal)
				++cnt;
			else {
				std::cout << currVal << " occurs " << cnt << " times " << std::endl;
				currVal = val;
				cnt = 1;
			}
		}
		std::cout << currVal << " occurs " << cnt << " times " << std::endl;
	}
	return 0;
}
```
* 输出结果与练习 1.17相同。  
## 练习 1.19：  
### 修改你为 1.4.1 节练习 1.11（第 11 页)所编写的程序(打印一个范围内的数)，使其能处理用户输入的第一个数比第二个数小的情况。  
答：  
```
#include <iostream>
int main()
{
	std::cout << "Enter two numbers to specify putout range: " << std::endl;
	int start = 0, end = 0;
	std::cin >> start >> end;
	if (start > end) {
		int tempVal = start;
		start = end;
		end = tempVal;
	}
	while (start <= end) {
		std::cout << "Putout: " << start << std::endl;
		++start;
	}
	return 0;
}
```
## 练习 1.20:  
### 在网站http://www.informit.com/title/0321714113上,第1章的代码目录中包含了头文件Sales_item.h。将它拷贝到你自己的工作目录中。  
用它编写一个程序，读取一组书籍销售记录，将每条记录打印到标准输出上。  
答：  
```
#include <iostream>
#include "Sales_item.h"
int main()
{
	Sales_item item;
	while (std::cin >> item)
		std::cout << item << std::endl;
	return 0;
}
```
## 练习 1.21：  
### 编写程序，读取两个ISBN 相同的 Sales_item 对象，输出它们的和。  
答： 
* 这题问早了，让我纠结一个多小时。不过练习下面就开始讲成员函数了。  
```
#include <iostream>
#include "Sales_item.h"
int main()
{
	std::cout << "Enter two sales records with the same ISBN: " << std::endl;
	Sales_item itemOne,itemTwo;
	std::cin >> itemOne >> itemTwo;
	if (itemOne.isbn() == itemTwo.isbn())
		std::cout << "The total sales record is: " << itemOne + itemTwo << std::endl;
	else
		std::cout << "These two sales records have different ISBN." << std::endl;
	return 0;
}
```
## 练习 1.22：  
### 编写程序，读取多个具有相同 ISBN 的销售记录，输出所有记录的和。  
答：  
```
#include <iostream>
#include "Sales_item.h"
int main()
{
	Sales_item itemTotal;
	if (std::cin >> itemTotal) {
		Sales_item item;
		while (std::cin >> item) {
			if (item.isbn() == itemTotal.isbn())
				itemTotal += item;
			else {
				std::cout << itemTotal << std::endl;
				itemTotal = item;
			}
		}
		std::cout << itemTotal << std::endl;
	}
	return 0;
}
```
