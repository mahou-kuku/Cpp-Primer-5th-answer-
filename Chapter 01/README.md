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
```ruby
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
```ruby
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
```ruby
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
```ruby
std::cout << "The sum of " << v1;
<< " and " << v2;
<< " is " << v1 + v2 << std::endl;
```
### 如果程序是合法的,它输出什么？如果程序不合法,原因何在?应该如何修正?  
答：  
* 不合法，多了两个;或者少了两个std::cout  
* 以下是一种修正方案。  
```ruby
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
* 第四句正确。输出的是中间的第三个界定符，输出为： /*   
## 练习 1.9：  
### 编写程序，使用 while 循环将 50 到 100 的整数相加。  
答：  
```ruby
#include <iostream>
int main()
{
	int sum = 0, val = 50;
	// Keep executing the while as long as val is less than or equal to 100.
	while (val <= 100) {
		sum += val;		// assigns sum + val to sum
		++val;			// add 1 to val
	}
	std::cout << "Sum of 50 to 100 inclusive is " << sum << std::endl;
	return 0;
}
```
