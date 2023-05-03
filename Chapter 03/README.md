## 练习3.1：
### 使用恰当的using声明重做 1.4.1 节（第11 页）和2.6.2节（第67 页）的练习。
答：
* 1.4.1 节
```
#include <iostream>
using std::cout;
using std::cin;
using std::endl;

int main()
{
	//使用 while 循环将 50 到 100 的整数相加
	int sum = 0, val = 50;
	while (val <= 100) {
		sum += val;
		++val;
	}
	cout << "Sum of 50 to 100 inclusive is " << sum << endl;

	//打印出 10 到 0 之间的整数
	val = 9;
	while (val >= 1) {
		cout << "Countdown: " << val << endl;
		--val;
	}

	//提示用户输入两个整数
	//打印出这两个整数所指定的范围内的所有整数
	cout << "Enter two numbers to specify putout range: " << endl;
	int start = 0, end = 0;
	cin >> start >> end;
	while (start <= end) {
		cout << "Putout: " << start << endl;
		++start;
	}

	return 0;
}
```
* 2.6.2节
```
#include <iostream>
#include <string>
using std::cout;
using std::endl;
using std::cerr;
using std::cin;
using std::string;

struct Sales_data {
	string itemID;
	int quantity{ 0 };
	double price{ 0.0 };
	double income{ 0.0 };
};
int main()
{
	Sales_data dataTotal;
	if (cin >> dataTotal.itemID >> dataTotal.quantity >> dataTotal.price) {
		dataTotal.income = dataTotal.quantity*dataTotal.price;

		Sales_data data;
		int cnt = 1;
		while (cin >> data.itemID >> data.quantity >> data.price) {
			data.income = data.quantity*data.price;

			if (dataTotal.itemID == data.itemID) {
				dataTotal.quantity += data.quantity;
				dataTotal.income += data.income;
				++cnt;
			}
			else {
				cout << dataTotal.itemID << " " << dataTotal.quantity << " "
					<< dataTotal.income << " "
					<< dataTotal.income / dataTotal.quantity << " " << cnt << endl;

				dataTotal.itemID = data.itemID;
				dataTotal.quantity = data.quantity;
				dataTotal.income = data.income;
				cnt = 1;
			}
		}

		// 输出最后一条记录
		cout << dataTotal.itemID << " " << dataTotal.quantity << " "
			<< dataTotal.income << " "
			<< dataTotal.income / dataTotal.quantity << " " << cnt << endl;
	}
	else {
		cerr << "No data?!" << endl;
		return -1;
	}
	return 0;
}
```
## 练习3.2:
### 编写一段程序从标准输入中一次读入一整行,然后修改该程序使其一次读入一个词。
答：  
```
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::string;
using std::getline;

int main()
{
	string str;
	//一次读入一整行
	getline(cin, str);
	cout << str << endl;

	return 0;
}
```
* 修改后：
```
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::string;

int main()
{
	string str;
	//一次读入一个词
	cin >> str;
	cout << str << endl;

	return 0;
}
```
## 练习3.3:
### 请说明 string类的输入运算符和 getline函数分别是如何处理空白字符的。
答：  
* string类的输入运算符（>>）：
* 当使用输入运算符（>>）从输入流读取数据到string对象时，它会自动忽略前导空白字符（如空格、制表符和换行符）。然后，它会继续读取字符，直到遇到下一个空白字符为止。遇到空白字符后，输入运算符会停止读取，这意味着空白字符不会包含在结果字符串中。
* getline函数：
* getline函数的行为与输入运算符（>>）不同。当使用getline从输入流读取数据到string对象时，它会读取所有字符，包括空白字符，直到遇到换行符（默认情况下）为止。换行符不会包含在结果字符串中。
## 练习 3.4：
### 编写一段程序读入两个字符串，比较其是否相等并输出结果。如果不相等，输出较大的那个字符串。改写上述程序,比较输入的两个字符串是否等长,如果不等长，输出长度较大的那个字符串。
答：  
```
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::string;
using std::getline;

int main()
{
	string strOne, strTwo;
	//output the larger 
	getline(cin, strOne);
	getline(cin, strTwo);
	if (strOne == strTwo)
		cout << "They're the same string." << endl;
	else if (strOne > strTwo)
		cout << "The larger: " << strOne << endl;
	else
		cout << "The larger: " << strTwo << endl;

	return 0;
}
```
* 改写后：
```
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::string;
using std::getline;

int main()
{
	string strOne, strTwo;
	//Output the longer
	getline(cin, strOne);
	getline(cin, strTwo);
	if (strOne.size() == strTwo.size())
		cout << "They're the same length." << endl;
	else if (strOne.size() > strTwo.size())
		cout << "The longer: " << strOne << endl;
	else
		cout << "The longer: " << strTwo << endl;

	return 0;
}
```
## 练习 3.5:
### 编写一段程序从标准输入中读入多个字符串并将它们连接在一起,输出连接,成的大字符串。然后修改上述程序,用空格把输入的多个字符串分隔开来。
答：  
```
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::string;
using std::getline;

int main()
{
	string strTotal, str;

	//Link strings
	while (getline(cin, str))
		strTotal += str;
	cout << strTotal << endl;

	return 0;
}
```
* 修改后：
```
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::string;
using std::getline;

int main()
{
	string strTotal, str;
	//Link strings with spaces
	while (getline(cin, str)) {
		if (!strTotal.empty())
			strTotal += " ";
		strTotal += str;
	}
	cout << strTotal << endl;

	return 0;
}
```
## 练习 3.6：
### 编写一段程序，使用范围 for 语句将字符串内的所有字符用 x 代替。
答：  
```
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::string;
using std::getline;

int main()
{
	string str;
	getline(cin, str);
	for (auto &c : str)
		c = 'X';
	cout << str << endl;
	return 0;
}
```
## 练习3.7:
### 就上一题完成的程序而言,如果将循环控制变量的类型设为char将发生什么？先估计一下结果，然后实际编程进行验证。
答：  
* 如果将循环变量的类型设置为 char， c将会是字符串中字符的拷贝，对它进行的修改不会影响原始字符串。
## 练习 3.8：
### 分别用 while 循环和传统的 for 循环重写第一题的程序，你觉得哪种形式更好呢？为什么？
答：  
* 对于这个例子，我个人更倾向于使用 for 循环，因为代码更紧凑，将循环变量的声明、初始化、更新和条件检查都放在一行。这种写法在简单的情况下，如此例，可读性较好。
```
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::string;
using std::getline;

int main()
{
	string str;
	//using wihle
	getline(cin, str);
	decltype(str.size()) index = 0;
	while (index < str.size()) {
		str[index] = 'X';
		++index;
	}

	cout << str << endl;

	//using for
	getline(cin, str);
	for (decltype(str.size()) index = 0; index < str.size(); ++index)
		str[index] = 'X';

	cout << str << endl;
	return 0;
}
```
## 练习 3.9：
### 下面的程序有何作用？它合法吗？如果不合法，为什么？
```
	string s;
	cout << s[0] << endl;
```
答：  
* 这个程序试图输出字符串s的第一个字符。但是s是一个空字符串。在尝试访问空字符串的任何字符时，会产生未定义行为。所以，程序是不合法的。
## 练习 3.10：
### 编写一段程序，读入一个包含标点符号的字符串，将标点符号去除后输出字符串剩余的部分。
答：  
```
#include <iostream>
#include <string>
#include <cctype>

using std::cout;
using std::endl;
using std::cin;
using std::string;
using std::getline;
using std::ispunct;

int main()
{
	string str="Hello,World!!!";
	for (auto c : str)
		if (!ispunct(c))
			cout << c;

	cout << endl;
	return 0;
}
```
## 练习 3.11：
### 下面的范围 for 语句合法吗?如果合法，c 的类型是什么?
```
	const string s = "Keep out!";
	for (auto &c : s) {/* ... */ };
```
答：  
* 合法，c 的类型是 const char& 。
## 练习 3.12：
### 下列 vector 对象的定义有不正确的吗？如果有，请指出来。对于正确的，描述其执行结果；对于不正确的,说明其错误的原因。
```
	(a) vector<vector<int>> ivec;
	(b) vector<string> svec = ivec;
	(c) vector<string> svec(10, "null");
```
答：  
* (a)在C++11标准是正确的。ivec 是一个空的二维int向量。
* (b)是不正确的。vector<vector<int>> 和 vector<string> 是不兼容的类型，所以这个定义是错误的。
* (c)是正确的。这里定义了一个名为 svec 的字符串向量，其包含 10 个元素，每个元素的初始值都是字符串 "null"。
## 练习3.13:
### 下列的vector对象各包含多少个元素?这些元素的值分别是多少?
```
	(a) vector<int> v1;		(b) vector<int> v2(10);
	(c) vector<int> v3(10, 42);	(d) vector<int> v4{ 10 };
	(e) vector<int> v5{ 10,42 };	(f) vector<string> v6{ 10 };
	(g) vector<string> v7{ 10,"hi" };
```
答：  
* (a)空的int向量 (b)10个0 (c)10个42 (d)1个10 (e)2个元素，10和42 (f)10个默认初始化string (g)10个字符串 "hi"
## 练习3.14:
## 编写一段程序，用cin读入一组整数并把它们存入一个vector对象。
答：  
```
#include <iostream>
#include <vector>

using std::cout;
using std::endl;
using std::cin;
using std::vector;

int main()
{

	vector<int>digits;

	//Input loop
	{
		for (int number = 0; cin >> number;)
			digits.push_back(number);
	}

	//Output loop
	{
		for (auto i : digits)
			cout << i << endl;
	}

	return 0;
}
```
## 练习3.15：
### 改写上题的程序,不过这次读入的是字符串。
答：  
```
#include <iostream>
#include <vector>
#include <string>

using std::cout;
using std::endl;
using std::cin;
using std::vector;
using std::string;

int main()
{

	vector<string>text;

	//Input loop
	{
		for (string word; cin >> word;)
			text.push_back(word);
	}

	//Output loop
	{
		for (auto i : text)
			cout << i << endl;
	}

	return 0;
}
```
## 练习 3.16：
### 编写一段程序，把练习 3.13 中 vector 对象的容量和具体内容输出出来。检验你之前的回答是否正确，如果不对，回过头重新学习 3.3.1 节（第87页)直到弄明白错在何处为止。
答：  
```
#include <iostream>
#include <vector>
#include <string>

using std::cout;
using std::endl;
using std::vector;
using std::string;

int main() {
	vector<int> v1;
	vector<int> v2(10);
	vector<int> v3(10, 42);
	vector<int> v4{ 10 };
	vector<int> v5{ 10,42 };
	vector<string> v6{ 10 };
	vector<string> v7{ 10,"hi" };

	vector<vector<int>> intVecs{ v1,v2,v3,v4,v5 };
	vector<vector<string>>strVecs{ v6,v7 };

	for (const auto& intVec : intVecs) {
		cout << "Size: " << intVec.size() << ", Content: { ";
		for (const auto& elem : intVec) {
			cout << elem << ' ';
		}
		cout << "}" << endl;
	}

	for (const auto& strVec : strVecs) {
		cout << "Size: " << strVec.size() << ", Content: { ";
		for (const auto& elem : strVec) {
			cout << elem << ' ';
		}
		cout << "}" << endl;
	}

	return 0;
}
```
## 练习 3.17：
### 从 cin 读入一组词并把它们存入一个 vector 对象，然后设法把所有词都改写为大写形式。输出改变后的结果，每个词占一行。
答：  
```
#include <iostream>
#include <vector>
#include <string>
#include <cctype>

using std::cout;
using std::endl;
using std::cin;
using std::vector;
using std::string;

int main() {
	vector<string> words;
	string word;

	//从 cin 读入一组词并把它们存入一个 vector 对象
	while (cin >> word) {
		words.push_back(word);
	}

	//把所有词都改写为大写形式
	for (auto &w : words) {
		for (auto &c : w) {
			c = toupper(c);
		}
	}

	//输出改变后的结果，每个词占一行
	for (const auto &w : words) {
		cout << w << endl;
	}
	return 0;
}
```
## 练习 3.18:
### 下面的程序合法吗?如果不合法,你准备如何修改?
```
	vector<int> ivec;
	ivec[0] = 42;
```
答：  
* 不合法，以下为修改：
```
vector<int> ivec{ 42 };
```
## 练习3.19:
### 如果想定义一个含有10个元素的vector对象,所有元素的值都是42,请列举出三种不同的实现方法。哪种方法更好呢？为什么？
答：  
```
	//方法1
	vector<int> vec_1(10, 42);
	//方法2
	vector<int> vec_2{ 42,42,42,42,42,42,42,42,42,42 };
	//方法3
	vector<int> vec_3;
	for (int i = 0; i < 10; ++i) {
		vec_3.push_back(42);
	}
```
* 方法1更好，方法1在一行代码中简洁明了地完成了任务，可读性较高。而且一开始就分配了足够的内存，避免了可能的内存重新分配，因此在性能上可能更优。
## 练习 3.20:
### 读入一组整数并把它们存入一个 vector 对象，将每对相邻整数的和输出出来。改写你的程序，这次要求先输出第 1 个和最后 1 个元素的和，接着输出第 2 个和倒数第2个元素的和,以此类推。
答：  
```
#include <iostream>
#include <vector>

using std::cout;
using std::endl;
using std::cin;
using std::vector;

int main() {
	int number;
	vector<int> numbers;

	// 读入整数并存入vector对象
	while (cin >> number) {
		numbers.push_back(number);
	}

	//输出每对相邻整数的和
	cout << "Sum of adjacent elements:" << endl;
	for (size_t i = 1; i < numbers.size(); ++i) {
		cout << numbers[i - 1] + numbers[i] << endl;
	}

	//输出首尾对应整数的和
	cout << "Sum of first and last elements and so on:" << endl;
	for (size_t i = 0, j = numbers.size() - 1; i <= j; ++i, --j) {
		if (i == j) {
			cout << numbers[i] << endl;
		} else {
			cout << numbers[i] + numbers[j] << endl;
		}
	}

	return 0;
}
```
## 练习 3.21：
### 请使用迭代器重做 3.3.3 节（第 94 页）的第一个练习。
答：  
```
#include <iostream>
#include <vector>
#include <string>

using std::cout;
using std::endl;
using std::vector;
using std::string;

int main() {
	vector<int> v1;
	vector<int> v2(10);
	vector<int> v3(10, 42);
	vector<int> v4{ 10 };
	vector<int> v5{ 10,42 };
	vector<string> v6{ 10 };
	vector<string> v7{ 10,"hi" };

	vector<vector<int>> intVecs{ v1,v2,v3,v4,v5 };
	vector<vector<string>>strVecs{ v6,v7 };

	for (auto intVec = intVecs.cbegin(); intVec != intVecs.cend(); ++intVec) {
		cout << "Size: " << intVec->size() << ", Content: { ";
		for (const auto& elem : *intVec) {
			cout << elem << ' ';
		}
		cout << "}" << endl;
	}

	for (auto strVec = strVecs.cbegin(); strVec != strVecs.cend(); ++strVec) {
		cout << "Size: " << strVec->size() << ", Content: { ";
		for (auto elem = strVec->cbegin(); elem != strVec->cend(); ++elem) {
			cout << *elem << ' ';
		}
		cout << "}" << endl;
	}

	return 0;
}
```
## 练习 3.22:
### 修改之前那个输出 text第一段的程序,首先把 text 的第一段全都改成大写形式，然后再输出它。
答：  
```
#include <iostream>
#include <string>
#include <vector>
#include <cctype>

using std::cout;
using std::endl;
using std::vector;
using std::string;
using std::toupper;
int main() {
	string s;
	string y{ "These words will not be output." };
	vector<string>text(10, "some ");
	text.push_back(s);
	text.push_back(y);

	//输出全都修改成大写形式的 text 第一段。
	for (auto it = text.begin();
	it != text.end() && !it->empty(); ++it) {
		for (auto &c : *it) {
			c = toupper(c);
		}
		cout << *it << endl;
	}

	return 0;
}
```
## 练习 3.23：
### 编写一段程序，创建一个含有 10 个整数的 vector 对象，然后使用迭代器将所有元素的值都变成原来的两倍。输出vector对象的内容,检验程序是否正确。
```
#include <iostream>
#include <vector>

using std::cout;
using std::endl;
using std::vector;

int main() {
	vector<int> numbers = { 1,2,3,4,5,6,7,8,9,10 };

	for (auto it = numbers.begin(); it != numbers.end(); ++it) {
		*it *= 2;
	}

	//输出修改后的元素
	for (const auto &num : numbers) {
		cout << num << " ";
	}
	cout << endl;

	return 0;
}
```
## 练习 3.24：
### 请使用迭代器重做 3.3.3 节（第 94 页）的最后一个练习。
答：  
```
#include <iostream>
#include <vector>

using std::cout;
using std::endl;
using std::cin;
using std::vector;

int main() {
	int number;
	vector<int> numbers;

	// 读入整数并存入vector对象
	while (cin >> number) {
		numbers.push_back(number);
	}

	//输出每对相邻整数的和
	cout << "Sum of adjacent elements:" << endl;

	for (auto i = numbers.cbegin()+1; i != numbers.cend(); ++i) {
		cout << *(i - 1) + *i << endl;
	}

	//输出首尾对应整数的和
	cout << "Sum of first and last elements and so on:" << endl;
	for (auto i = numbers.cbegin(), j = numbers.cend() - 1; i < j; ++i, --j) {
		if (i == j) {
			cout << *i << endl;
		}
		else {
			cout << *i + *j << endl;
		}
	}

	return 0;
}
```
## 练习 3.25:
### 3.3.3 节(第 93 页)划分分数段的程序是使用下标运算符实现的，请利用迭代器改写该程序并实现完全相同的功能。
答：  
```
#include <iostream>
#include <vector>

using std::cout;
using std::endl;
using std::cin;
using std::vector;

int main() {
	vector<unsigned> scores(11, 0);
	unsigned grade;
	while (cin >> grade) {
		if (grade <= 100) {
			auto it = scores.begin() + (grade / 10);
			++(*it);
		}
	}

	//输出结果
	for (const auto &score : scores) {
		cout << score << " ";
	}
	cout << endl;

	return 0;
}
```
## 练习 3.26：
### 在 100 页的二分搜索程序中，为什么用的是 mid = beg +(end - beg） /2，而非 mid = (beg + end) /2;？
答：  
* 迭代器之间不能执行加法运算，会导致编译错误。
## 练习 3.27：
### 假设 txt_size 是一个无参数的函数，它的返回值是 int。请回答下列哪个定义是非法的？为什么？
```
	unsigned buf_size = 1024;
	(a) int ia[buf_size];	(b) int ia[4 * 7 - 14];
	(c) int ia[txt_size()];	(d)char st[11] = "fundamental";
```
答：  
* (a) 非法。数组的大小必须是一个常量表达式。
* (b) 合法。这个表达式是一个常量表达式。
* (c) 非法。数组的大小必须是一个常量表达式。txt_size()的返回值在编译时无法确定。
* (d) 非法。字符串 "fundamental" 包括空字符 '\0' 一共有 12 个字符，而数组 st 的大小只有 11。
## 练习 3.28：
### 下列数组中元素的值是什么？
```
string sa[10];
int ia[10];
int main() {
	string sa2[10];
	int ia2[10];
}
```
答：  
* sa 中的元素都是空字符串。 ia 中的元素都是0。 sa2 中的元素都是空字符串。 ia2 中的元素都是未定义的。
## 练习3.29：
### 相比于 vector来说，数组有哪些缺点，请列举一些。
答：  
* 固定大小：数组的大小在声明时就确定了，无法在运行时动态改变。而vector可以自动调整大小以适应数据的增长。
* 少量的成员函数：数组提供的操作有限，而vector作为一个类，提供了丰富的成员函数以方便地操作数据。
* 不易传递给函数：将数组作为参数传递给函数时，传递的是指向数组首元素的指针，丢失了数组大小信息。vector可以通过传递引用或const引用轻松地保留其大小信息。
## 练习 3.30：
### 指出下面代码中的索引错误。
```
	constexpr size_t array_size = 10;
	int ia[array_size];
	for (size_t ix = 1; ix <= array_size; ++ix)
		ia[ix] = ix;
```
答：  
*  ia 的有效索引范围是 0 到 array_size - 1。然而，循环条件是 ix <= array_size，这意味着当 ix == array_size 时，依然会执行循环体。这将导致数组越界，访问非法的内存区域。
## 练习 3.31：
### 编写一段程序，定义一个含有 10 个 int 的数组，令每个元素的值就是其下标值。
答：  
```
#include <iostream>

int main() {
	constexpr size_t array_size = 10;
	int arr[array_size];

	for (size_t i = 0; i < array_size; ++i) {
		arr[i] = i;
	}

	return 0;
}
```
## 练习3.32:
### 将上一题刚刚创建的数组拷贝给另外一个数组。利用vector重写程序,实现类似的功能。
答：  
```
#include <iostream>
#include <vector>

using std::vector;

int main() {
	//使用数组实现
	constexpr size_t array_size = 10;
	int arr1[array_size];
	int arr2[array_size];

	for (size_t i = 0; i < array_size; ++i) {
		arr1[i] = i;
	}

	for (size_t i = 0; i < array_size; ++i) {
		arr2[i] = arr1[i];
	}

	//使用vector实现
	vector<int> vec1;
	for (int i = 0; i < array_size; ++i) {
		vec1.push_back(i);
	}

	vector<int>vec2(array_size);

	for (size_t i = 0; i < array_size; ++i) {
		vec2[i] = vec1[i];
	}

	return 0;
}
```
## 练习3.33:
### 对于104页的程序来说，如果不初始化 scores将发生什么？
答：  
* 如果不初始化scores数组，它将包含未定义的值，这些值取决于内存中的原始内容。当根据输入的grade值递增scores数组中相应的元素时，这些值将被累加，导致统计结果不正确。
## 练习 3.34：
### 假定 p1 和 p2 指向同一个数组中的元素，则下面程序的功能是什么？什么情况下该程序是非法的？
```
p1 += p2 - p1;
```
答：  
* 该程序的功能实际上是使指针 p1 指向与 p2 相同的数组元素。如果指针 p1 和 p2 能保证指向同一个数组中的元素，则不必担心产生错误。
## 习 3.35：
### 编写一段程序，利用指针将数组中的元素置为0。
答：  
```
#include <iostream>

using std::cout;

int main() {
	int arr[] = { 1, 2, 3, 4, 5 }; // 初始化数组
	int length = sizeof(arr) / sizeof(arr[0]); // 计算数组长度

	// 使用指针遍历数组并将元素置为0
	for (int *p = arr; p < arr + length; ++p) {
		*p = 0;
	}

	// 输出修改后的数组
	for (int i = 0; i < length; ++i) {
		cout << arr[i] << " ";
	}

	return 0;
}
```
## 练习 3.36：
### 编写一段程序，比较两个数组是否相等。再写一段程序，比较两个 vector对象是否相等。
答：  
```
#include <iostream>
#include <vector>

using std::cout;
using std::endl;
using std::vector;

int main() {
	//比较两个数组是否相等
	int arr1[] = { 1, 2, 3, 4, 5 };
	int arr2[] = { 1, 2, 3, 4, 5 };
	int length1 = sizeof(arr1) / sizeof(arr1[0]);
	int length2 = sizeof(arr2) / sizeof(arr2[0]);
	bool arrays_are_equal = true;

	if (length1 != length2) {
		arrays_are_equal = false;
	}else {
		for (int i = 0; i < length1; ++i) {
			if (arr1[i] != arr2[i]) {
				arrays_are_equal = false;
				break;
			}
		}
	}

	if (arrays_are_equal) {
		std::cout << "The arrays are equal." << std::endl;
	}else {
		std::cout << "The arrays are not equal." << std::endl;
	}

	//比较两个vector对象是否相等
	std::vector<int> vec1 = { 1, 2, 3, 4, 5 };
	std::vector<int> vec2 = { 1, 2, 3, 4, 5 };

	if (vec1 == vec2) {
		std::cout << "The vectors are equal." << std::endl;
	}else {
		std::cout << "The vectors are not equal." << std::endl;
	}

	return 0;
}
```
