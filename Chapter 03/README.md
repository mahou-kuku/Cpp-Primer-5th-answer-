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
	string itemName;
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
