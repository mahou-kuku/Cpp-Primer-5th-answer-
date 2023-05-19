## 练习 5.1：
### 什么是空语句？什么时候会用到空语句？
答：  
* 空语句由一个单独的分号构成。它实际上什么都不做。当需要在代码中提供一个语句，但又不想执行任何实际操作时，就可以使用空语句。
## 练习 5.2：
### 什么是块？什么时候会用到块？
答：  
* 块是指由一对花括号所包围的一段代码。这段代码中可以包含零条或多条语句，甚至可以嵌套包含其他的块。块的作用主要有以下两个方面：
* 限制作用域：在块中定义的变量，其作用域仅限于该块。当代码执行离开这个块时，这些变量就会被销毁。这对于防止命名冲突以及管理内存使用非常有用。
* 组织代码：块也可以用来将相关的代码组织在一起，增强代码的可读性和可维护性。
## 练习5.3：
### 使用逗号运算符(参见4.10 节，第140页)重写 1.4.1 节(第10页)的 while循环，使它不再需要块，观察改写之后的代码的可读性提高了还是降低了。
答：  
```
while (val <= 10)
    sum += val, ++val;
```
* 虽然这样的代码更简短，但是它的可读性可能降低了。一般来说，使用块和新行来组织代码可以使代码更清晰、更易读。
## 练习 5.4:
### 说明下列例子的含义，如果存在问题,试着修改它。
```
	(a) while (string::iterator iter != s.end()) {/*...*/ }
	(b) while(bool status=find(word)){/*...*/}
		    if(!status){/*...*/}
```
答：  
* (a) 尝试使用 string::iterator 类型的变量 iter，但并没有声明它。应该在循环之前声明和初始化这个变量。修改后的代码可能如下：
```
	string s = "example";
	string::iterator iter = s.begin();
	while (iter != s.end()) {
		/* ...*/
		++iter;
	}
```
* (b) 使用一个函数 find(word) 并将其返回值赋给一个 bool 类型的变量 status。但是，status 是在 while 循环的条件部分声明的，所以在循环体之外是无法访问的。修改后的代码可能如下：
```
    while(bool status=find(word)){
    	if (!status) {
    	/*...*/
    	}
    }
```
## 练习 5.5：
### 写一段自己的程序，使用 if else 语句实现把数字成绩转换成字母成绩的要求。
答：  
```
#include <iostream>
#include <string>

int main() {
	unsigned grade;
	std::string letterGrade;
	std::cout << "Please enter your numeric grade: ";
	std::cin >> grade;

	if (grade < 60) {
		letterGrade = "F";
	} else if (grade < 70) {
		letterGrade = "D";
	} else if (grade < 80) {
		letterGrade = "C";
	} else if (grade < 90) {
		letterGrade = "B";
	} else if (grade < 100) {
		letterGrade = "A";
	} else {
		letterGrade = "A++";
		grade = 100;
	}

	if (grade >= 60 && grade != 100) {
		int lastDigit = grade % 10;
		if (lastDigit >= 8) {
			letterGrade += "+";
		} else if (lastDigit <= 2) {
			letterGrade += "-";
		}
	}
	std::cout << "Your letter grade is: " << letterGrade << std::endl;

	return 0;
}
```
## 练习 5.6:
### 改写上一题的程序,使用条件运算符(参见4.7节,第134页)代替if else语句。
答：  
```
#include <iostream>
#include <string>

int main() {
	unsigned grade;
	std::string letterGrade;
	std::cout << "Please enter your numeric grade: ";
	std::cin >> grade;

	letterGrade = (grade < 60) ? "F" :
		(grade < 70) ? "D" :
		(grade < 80) ? "C" :
		(grade < 90) ? "B" :
		(grade < 100) ? "A" : "A++";

	letterGrade += (grade < 60 || grade > 100) ? "" :
		(grade % 10 >= 8) ? "+" :
		(grade % 10 <= 2) ? "-" : "";
	std::cout << "Your letter grade is: " << letterGrade << std::endl;

	return 0;
}
```
## 练习 5.7:
### 改正下列代码段中的错误。
```
	(a) if (ival1 != ival2)
		ival1 = ival2
	    else ival1 = ival2 = 0;
	    
	(b) if (ival < minval)
		minval = ival;
		occurs = 1;
	
	(c) if (int ival = get_value()) 
		cout << "ival = " << ival << endl;
	    if (!ival)
		cout << "ival = 0\n";
		
	(d) if (ival = 0)
		ival = get_value();
```
答：  
* (a):
```
	if (ival1 != ival2)
		ival1 = ival2;
	else ival1 = ival2 = 0;
```
* (b):
```
	if (ival < minval) {
		minval = ival;
		occurs = 1;
	}
```
* (c):
```
	if (int ival = get_value())
		cout << "ival = " << ival << endl;
	else if (!ival)
		cout << "ival = 0\n";
```
* (d):
```
	if (ival == 0)
		ival = get_value();
```
## 练习 5.8:
### 什么是“悬垂else” ? C++语言是如何处理else子句的?
答：  
* 因为 if 语句可以嵌套，而且每个 if 语句都可以选择是否包含 else 分支。如果有多个连续的 if 语句并且都没有 else 分支，当最后一个 if 后面出现了一个 else，那么这个 else 应该归属于哪个 if 就是所谓的 "悬垂 else" 问题。
* C++的规则是：else子句会匹配最近的尚未匹配的if。
