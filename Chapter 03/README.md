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

	//一次输入一个词
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

	//output the longer
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
	string strTotal,str;

	//link strings
	while (getline(cin, str))
		strTotal += str;
	cout << strTotal << endl;

	cin.clear();	//clear EOF
	strTotal = "\x0";
	//link strings with spaces
	while (getline(cin, str)) {
		if (!strTotal.empty())
			strTotal += " ";
		strTotal += str;
	}
		cout << strTotal << endl;

	return 0;
}
···
