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
