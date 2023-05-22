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
## 练习 5.9：
### 编写一段程序，使用一系列 if 语句统计从 cin 读入的文本中有多少元音字母。
答：  
```
#include <iostream>
#include <cctype>

using std::cout;

int main() {
	unsigned aCnt = 0, eCnt = 0, iCnt = 0, oCnt = 0, uCnt = 0;
	char ch;
	while (std::cin >> ch) {
		ch = std::tolower(ch);	//将大写字母转换为小写
		//如果ch是元音字母，将其对应的计数值加1
		if (ch == 'a') {
			++aCnt;
		} else if (ch == 'e') {
			++eCnt;
		} else if (ch == 'i') {
			++iCnt;
		} else if (ch == 'o') {
			++oCnt;
		} else if (ch == 'u') {
			++uCnt;
		}
	}
	//输出结果
	cout << "Number of vowel a: \t" << aCnt << '\n'
		<< "Number of vowel e: \t" << eCnt << '\n'
		<< "Number of vowel i: \t" << iCnt << '\n'
		<< "Number of vowel o: \t" << oCnt << '\n'
		<< "Number of vowel u: \t" << uCnt << std::endl;

	return 0;
}

```
## 练习 5.10：
### 我们之前实现的统计元音字母的程序存在一个问题：如果元音字母以大写形式出现，不会被统计在内。编写一段程序，既统计元音字母的小写形式，也统计大写形式，也就是说，新程序遇到'a'和'A'都应该递增 aCnt 的值，以此类推。
答：  
```
#include <iostream>

using std::cout;

int main() {
	unsigned aCnt = 0, eCnt = 0, iCnt = 0, oCnt = 0, uCnt = 0;
	char ch;
	while (std::cin >> ch) {
		//如果ch是元音字母，将其对应的计数值加1
		switch (ch) {
		case 'a': case 'A':
			++aCnt;
			break;
		case 'e': case 'E':
			++eCnt;
			break;
		case 'i': case 'I':
			++iCnt;
			break;
		case 'o': case 'O':
			++oCnt;
			break;
		case 'u': case 'U':
			++uCnt;
			break;
		default:
			break;
		}
	}
	//输出结果
	cout << "Number of vowel a: \t" << aCnt << '\n'
		<< "Number of vowel e: \t" << eCnt << '\n'
		<< "Number of vowel i: \t" << iCnt << '\n'
		<< "Number of vowel o: \t" << oCnt << '\n'
		<< "Number of vowel u: \t" << uCnt << std::endl;

	return 0;
}
```
## 练习 5.11:
### 修改统计元音字母的程序,使其也能统计空格、制表符和换行符的数量。
答：  
```
#include <iostream>

using std::cout;

int main() {
	unsigned aCnt = 0, eCnt = 0, iCnt = 0, oCnt = 0, uCnt = 0;
	unsigned spaceCnt = 0, tabCnt = 0, nlCnt = 0;
	char ch;
	while (std::cin.get(ch)) {
			//如果ch是元音字母或空白字符，将其对应的计数值加1
			switch (ch) {
			case 'a': case 'A':
				++aCnt;
				break;
			case 'e': case 'E':
				++eCnt;
				break;
			case 'i': case 'I':
				++iCnt;
				break;
			case 'o': case 'O':
				++oCnt;
				break;
			case 'u': case 'U':
				++uCnt;
				break;
			case' ':
				++spaceCnt;
				break;
			case'\t':
				++tabCnt;
				break;
			case'\n':
				++nlCnt;
				break;
			default:
				break;
			}
		}
	//输出结果
	cout << "Number of vowel a: \t" << aCnt << '\n'
		<< "Number of vowel e: \t" << eCnt << '\n'
		<< "Number of vowel i: \t" << iCnt << '\n'
		<< "Number of vowel o: \t" << oCnt << '\n'
		<< "Number of vowel u: \t" << uCnt << '\n'
		<< "Number of space: \t" << spaceCnt << '\n'
		<< "Number of tab: \t" << tabCnt << '\n'
		<< "Number of new line: \t" << nlCnt << std::endl;

	return 0;
}
```
## 练习 5.12:
### 修改统计元音字母的程序，使其能统计以下含有两个字符的字符序列的数量:ff、fl 和 fi。
答：  
```
#include <iostream>

using std::cout;

int main() {
	unsigned aCnt = 0, eCnt = 0, iCnt = 0, oCnt = 0, uCnt = 0;
	unsigned ffCnt = 0, flCnt = 0, fiCnt = 0;
	char ch, prev_ch = '\0';	// prev_ch用于存储上一个字符
	while ((ch = std::cin.get()) != EOF) {
		//如果ch是元音字母，将其对应的计数值加1
		switch (ch) {
		case 'a': case 'A':
			++aCnt;
			break;
		case 'e': case 'E':
			++eCnt;
			break;
		case 'i':
			if (prev_ch == 'f')++fiCnt;	// 如果上一个字符是'f', 增加'fi'的计数
		case 'I':
			++iCnt;
			break;
		case 'o': case 'O':
			++oCnt;
			break;
		case 'u': case 'U':
			++uCnt;
			break;
		case 'f':
			if (prev_ch == 'f')++ffCnt;	// 如果上一个字符是'f', 增加'ff'的计数
			break;
		case 'l':
			if (prev_ch == 'f')++flCnt;	// 如果上一个字符是'f', 增加'fl'的计数
			break;
		default:
			break;
		}
		prev_ch = ch;	// 更新prev_ch以存储当前字符
	}
	//输出结果
	cout << "Number of vowel a: \t" << aCnt << '\n'
		<< "Number of vowel e: \t" << eCnt << '\n'
		<< "Number of vowel i: \t" << iCnt << '\n'
		<< "Number of vowel o: \t" << oCnt << '\n'
		<< "Number of vowel u: \t" << uCnt << '\n'
		<< "Number of sequence ff: \t" << ffCnt << '\n'
		<< "Number of sequence fl: \t" << flCnt << '\n'
		<< "Number of sequence fi: \t" << fiCnt << std::endl;

	return 0;
}
```
## 练习5.13:
### 下面显示的每个程序都含有一个常见的编程错误,指出错误在哪里,然后修改它们。
```
(a) 
	unsigned aCnt = 0, eCnt = 0, iouCnt = 0;
	char ch = next_text();
	switch (ch) {
	case 'a': aCnt++;
	case 'e': eCnt++;
	default: iouCnt++;
	}	    
(b) 
	unsigned index=some_value();
	switch (index) {
	case 1: 
		int ix = get_value();
		ivec[ix] = index;
		break;
	default:
		ix = ivec.size() - 1;
		ivec[ix] = index;
	}
(c)
	unsigned evenCnt = 0, oddCnt = 0;
	int digit = get_num() % 10;
	switch (digit) {
	case 1, 3, 5, 7, 9:
		oddCnt++;
		break;
	case 2, 4, 6, 8, 10:
		evenCnt++;
		break;
	}
(d)
	unsigned ival = 512, jval = 1024, kval = 4096;
	unsigned bufsize;
	unsigned swt = get_bufCnt();
	switch (swt) {
	case ival:
		bufsize = ival * sizeof(int);
		break;
	case jval:
		bufsize = jval * sizeof(int);
		break;
	case kval:
		bufsize = kval * sizeof(int);
		break;
	}
```
答：  
```
//(a) 的问题是switch语句中没有使用break语句，导致一旦满足某个case，后面的case和default都会执行。
	unsigned aCnt = 0, eCnt = 0, iouCnt = 0;
	char ch = next_text();
	switch (ch) {
	case 'a':
		aCnt++;
		break;
	case 'e':
		eCnt++;
		break;
	default: 
		iouCnt++;
		break;
	}
//(b) 的问题在于ix只在第一个case中声明和定义，因此在default中是无法访问的。
	unsigned index = some_value();
	int ix;
	switch (index) {
	case 1:
		ix = get_value();
		ivec[ix] = index;
		break;
	default:
		ix = ivec.size() - 1;
		ivec[ix] = index;
	}
//(c) 的问题是case的值并不能像这样使用逗号分隔，它需要使用多个case语句。
	unsigned evenCnt = 0, oddCnt = 0;
	int digit = get_num() % 10;
	switch (digit) {
	case 1: case 3: case 5: case 7: case 9:
		oddCnt++;
		break;
	case 2: case 4: case 6: case 8: case 0: // 这里是0而不是10，因为digit只能是0-9的整数
		evenCnt++;
		break;
	}
//(d) 的问题在于case语句的表达式必须是常量表达式，而不是变量。
	constexpr unsigned ival = 512, jval = 1024, kval = 4096; // 使用constexpr定义常量表达式
	unsigned bufsize;
	unsigned swt = get_bufCnt();
	switch (swt) {
	case ival:
		bufsize = ival * sizeof(int);
		break;
	case jval:
		bufsize = jval * sizeof(int);
		break;
	case kval:
		bufsize = kval * sizeof(int);
		break;
	}
```
## 练习 5.14:
### 编写一段程序，从标准输入中读取若干 string 对象并查找连续重复出现的单词。所谓连续重复出现的意思是：一个单词后面紧跟着这个单词本身。要求记录连续重复出现的最大次数以及对应的单词。如果这样的单词存在，输出重复出现的最大次数；如果不存在,输出一条信息说明任何单词都没有连续出现过。例如，如果输入是how now now now brown cow cow那么输出应该表明单词now连续出现了3次。
答：  
```
#include <iostream>
#include <string>

int main() {
	std::string word = "", preWord = "", maxWord = "";
	int count = 0, maxCount = 0;

	while (std::cin >> word) {
		if (word == preWord) {
			++count;
			if (count > maxCount) {
				maxWord = word;
				maxCount = count;
			}
		} else {
			preWord = word;
			count = 1;
		}
	}

	if (maxCount > 1) {
		std::cout << "The word " << maxWord << " occured " << maxCount << " times consecutively." << std::endl;
	} else {
		std::cout << "No word was occured consecutively." << std::endl;
	}

	return 0;
}

```
## 练习 5.15：
### 说明下列循环的含义并改正其中的错误。
```
(a)
	for (int ix = 0; ix != sz; ++ix){/* ...*/}
	if(ix!=sz)
		// ...
(b)
	int ix;
	for (ix != sz; ++ix) {/* ...*/ }
(c)
	for (int ix = 0; ix != sz; ++ix, ++sz) {/* ...*/ }
```
答：  
```
//(a) 变量 ix 在 for 循环之外是不可见的，因此 if(ix!=sz) 将无法通过编译。如果需要在 for 循环之外使用 ix，那么应该在 for 循环外部声明这个变量
	int ix;
	for (ix = 0; ix != sz;++ix){/* ...*/}
	if (ix != sz) {
		// ...
	}
//(b) for 循环的初始化部分并未初始化 ix，那么条件语句左侧需要一个空语句
	int ix = 0;
	for (; ix != sz; ++ix) {/* ...*/ }
//(c) ix 和 sz 同时增加，那么 ix 永远不可能等于 sz，这将会导致无限循环。
	for (int ix = 0; ix != sz; ++ix) {/* ...*/ }
```
## 练习 5.16:
### while循环特别适用于那种条件保持不变、反复执行操作的情况,例如,当未达到文件末尾时不断读取下一个值。for循环则更像是在按步骤迭代,它的索引值,在某个范围内依次变化。根据每种循环的习惯用法各自编写一段程序,然后分别用另一种循环改写。如果只能使用一种循环,你倾向于使用哪种呢?为什么?
答：  
```
	//原始while循环
	int num;
	while (cin >> num) {
		// ...
	}
	//转化为for循环：
	for (int num; cin >> num;) {
		// ...
	}
	
	//原始for循环：
	for (int i = 0; i < 10; ++i) {
		cout << i << " ";
	}
	//转化为while循环：
	int i = 0;
	while (i < 10) {
		cout << i << " ";
		++i;
	}
```
* 如果只能使用一种循环，我选择for循环，因为for循环在大多数情况下可以替代while循环，但反之并非如此。如果能选两种，我首选while。好看。
## 练习 5.17：
### 假设有两个包含整数的 vector 对象，编写一段程序，检验其中一个 vector对象是否是另一个的前缀。为了实现这一目标，对于两个不等长的 vector 对象，只需·挑出长度较短的那个,把它的所有元素和另一个vector对象比较即可。例如,如果两个vector对象的元素分别是0、1、1、2和0、1、1、2、3、5、8,则程序的返回结果应该为真。
答：  
```
#include <iostream>
#include <vector>

int main() {
	std::vector<int> vec_1 = { 0, 1, 1, 2 };
	std::vector<int> vec_2 = { 0, 1, 1, 2, 3, 5, 8 };
	bool is_prefix = true;

	// 取得长度较短的那个vector的长度
	auto len = vec_1.size() < vec_2.size() ? vec_1.size() : vec_2.size();

	// 遍历较短的vector的每一个元素，和较长的vector做比较
	for (int i = 0; i < len; ++i)
	{
		if (vec_1[i] != vec_2[i]) {
			is_prefix = false;
		}
	}

	if (is_prefix) {
		std::cout << "vec1 is a prefix of vec2\n";
	} else {
		std::cout << "vec1 is not a prefix of vec2\n";
	}

	return 0;
}
```
## 练习 5.18：
### 说明下列循环的含义并改正其中的错误。
```
(a)
	do
		int v1, v2;
		cout << "Please enter two numbers to sum:";
		if (cin >> v1 >> v2)
			cout << "Sum is: " << v1 + v2 << endl;
	while (cin);
(b)
	do {
		//...
	} while (int ival = get_response());
(c)
	do {
		int ival = get_response();
	} while (ival);
```
答：  
```
//(a) 这个 do-while 循环不包含花括号，正确的做法是将循环体放在花括号中。
	do {
		int v1, v2;
		cout << "Please enter two numbers to sum:";
		if (cin >> v1 >> v2)
			cout << "Sum is: " << v1 + v2 << endl;
	} while (cin);
//(b) do-while 循环条件中使用的变量必须定义在循环体外。
	int ival;
	do {
		//...
	} while (ival = get_response());
//(c) 变量 ival 在 do 语句块中被声明，所以它的作用范围只在 do 语句块中，while 不能访问它。
	int ival;
	do {
		ival = get_response();
	} while (ival);
```
## 练习 5.19:
### 编写一段程序,使用do while循环重复地执行下述任务:首先提示用户输入两个string对象，然后挑出较短的那个并输出它。
答：  
```
#include <iostream>
#include <string>

int main() {
	do {
		std::string s1, s2;
		std::cout << "Please enter two strings: ";
		std::cin >> s1 >> s2;
		std::cout << "The shorter one is: " << (s1 <= s2 ? s1 : s2) << std::endl;
		std::cout << "Would you like to compare another pair of strings? (y/n): ";
		char c;
		std::cin >> c;
		if (c != 'y' && c != 'Y') {
			break;
		}
	} while (true);
	
	return 0;
}
```
## 练习 5.20： 
### 编写一段程序，从标准输入中读取 string 对象的序列直到连续出现两个相同的单词或者所有单词都读完为止。使用 while 循环一次读取一个单词，当一个单词连续出现两次时使用 break 语句终止循环。输出连续重复出现的单词，或者输出一个消息说明没有任何单词是连续重复出现的。
答：  
```
#include <iostream>
#include <string>

int main() {
	std::string prevWord = "", currWord = "";
	bool isRepeated = false;

	while (std::cin >> currWord) {
		if (prevWord == currWord) {
			isRepeated = true;
			break;
		}
		prevWord = currWord;
	}

	if (isRepeated) {
		std::cout << "The word '" << currWord << "' was repeated." << std::endl;
	} else {
		std::cout << "No word was repeated." << std::endl;
	}

	return 0;
}
```
## 练习 5.21：
### 修改 5.5.1 节（第 171 页）练习题的程序，使其找到的重复单词必须以大写字母开头。
答：  
```
#include <iostream>
#include <string>
#include <cctype>

int main() {
	std::string prevWord = "", currWord = "";
	bool isRepeated = false;

	while (std::cin >> currWord) {
		if (!std::isupper(currWord[0])) {
			prevWord = currWord;
			continue;
		} else if (prevWord == currWord) {
			isRepeated = true;
			break;
		}
		prevWord = currWord;
	}

	if (isRepeated) {
		std::cout << "The word '" << currWord << "' was repeated." << std::endl;
	} else {
		std::cout << "No word was repeated." << std::endl;
	}

	return 0;
}
```
## 练习 5.22:
### 本节的最后一个例子跳回到 begin,其实使用循环能更好地完成该任务。重写这段代码，注意不再使用 goto 语句。
```
begin:
	int sz = get_size();
	if (sz <= 0) {
		goto begin;
	}
```
答：  
```
	int sz;
	do {
		sz = get_size();
	} while (sz <= 0);
```
* 这段代码并没能模拟goto例子的全部效果，使用标准循环结构时，难以做到在每轮循环中都重新初始化及销毁sz的同时还不影响sz的作用域。
## 练习 5.23：
### 编写一段程序，从标准输入读取两个整数，输出第一个数除以第二个数的结果。
答：  
```
#include <iostream>

int main() {
	int num_1, num_2;
	while (true) {
		std::cout << "Please enter two integers: " << std::endl;
		std::cin >> num_1 >> num_2;

		double result = static_cast<double>(num_1) / num_2;
		std::cout << "The result of " << num_1 << " divided by " << num_2 << " is " << result << std::endl;
		break;	//跳出循环
	}

	return 0;
}
```
## 练习 5.24：
### 修改你的程序，使得当第二个数是 0 时抛出异常。先不要设定 catch 子句，运行程序并真的为除数输入 0，看看会发生什么？
答：  
```
#include <iostream>
#include <stdexcept>

int main() {
	int num_1, num_2;
	while (true) {
		std::cout << "Please enter two integers: " << std::endl;
		std::cin >> num_1 >> num_2;
		if (num_2 == 0) {
			throw std::invalid_argument("The second number cannot be zero!");
		}
		double result = static_cast<double>(num_1) / num_2;
		std::cout << "The result of " << num_1 << " divided by " << num_2 << " is " << result << std::endl;
		break;	//没有抛出异常则跳出循环
	}

	return 0;
}
```
* 除数为0时，程序会停止工作。
## 练习 5.25:
### 修改上一题的程序，使用try 语句块去捕获异常。catch子句应该为用户输出一条提示信息，询问其是否输入新数并重新执行try 语句块的内容。
答：  
```
#include <iostream>
#include <stdexcept>

int main() {
	while (true) {	
		try {
			std::cout << "Please enter two integers: " << std::endl;
			int num_1, num_2;
			std::cin >> num_1 >> num_2;
			if (num_2 == 0) {
				throw std::invalid_argument("The second number cannot be zero!");
			}
			double result = static_cast<double>(num_1) / num_2;
			std::cout << "The result of " << num_1 << " divided by " << num_2 << " is " << result << std::endl;
			break;	//没有抛出异常则跳出循环
		}
		catch (const std::invalid_argument &e) {
			std::cout << "Exception: " << e.what() << std::endl;
			std::cout << "Would you like to enter two new numbers? (y/n)" << std::endl;
			char c;
			std::cin >> c;
			if (c != 'y') {
				break;
			}
		}
	}

	return 0;
}
```
