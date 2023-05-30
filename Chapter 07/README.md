## 练习 7.1：
### 使用2.6.1 节练习定义的 Sales_data类为 1.6 节（第 21 页)的交易处理程序编写一个新版本。
答：
```
#include <iostream>
#include <string>

struct Sales_data {
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

int main()
{
	Sales_data total;
	if (std::cin >> total.bookNo >> total.units_sold >> total.revenue) {
		Sales_data trans;
		while (std::cin >> trans.bookNo >> trans.units_sold >> trans.revenue) {
			if (total.bookNo == trans.bookNo) {
				total.units_sold += trans.units_sold;
				total.revenue += trans.revenue;
			} else {
				std::cout << total.bookNo << " " << total.units_sold << " "
					<< total.revenue << " " << total.revenue / total.units_sold << std::endl;
				total.bookNo = trans.bookNo;
				total.units_sold = trans.units_sold;
				total.revenue = trans.revenue;
			}
		}
		std::cout << total.bookNo << " " << total.units_sold << " "
			<< total.revenue << " " << total.revenue / total.units_sold << std::endl;
	} else {
		std::cerr << "No data?!" << std::endl;
		return -1;
	}
	return 0;
}
```
## 练习 7.2：
### 曾在 2.6.2 节的练习（第 67 页）中编写了一个 Sales_data 类，请向这个类添加 combine 和 isbn 成员。
答：
```
struct Sales_data {
	//新成员：关于 Sales_data 对象的操作
	std::string isbn() const {
		return bookNo; 
	}
	Sales_data& conbine(const Sales_data&);
	//数据成员
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

Sales_data & Sales_data::conbine(const Sales_data &rhs) {
	units_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}
```
## 练习 7.3：
### 修改 7.1.1 节（第 229 页）的交易处理程序，令其使用这些成员。
答：
```
#include <iostream>
#include <string>

struct Sales_data {
	//新成员：关于 Sales_data 对象的操作
	std::string isbn() const {
		return bookNo; 
	}
	Sales_data& conbine(const Sales_data&);
	//数据成员
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

Sales_data & Sales_data::conbine(const Sales_data &rhs) {
	units_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}

int main()
{
	Sales_data total;
	if (std::cin >> total.bookNo >> total.units_sold >> total.revenue) {
		Sales_data trans;
		while (std::cin >> trans.bookNo >> trans.units_sold >> trans.revenue) {
			if (total.isbn() == trans.isbn()) {
				total.conbine(trans);
			} else {
				std::cout << total.bookNo << " " << total.units_sold << " "
					<< total.revenue << " " << total.revenue / total.units_sold << std::endl;
				total.bookNo = trans.bookNo;
				total.units_sold = trans.units_sold;
				total.revenue = trans.revenue;
			}
		}
		std::cout << total.bookNo << " " << total.units_sold << " "
			<< total.revenue << " " << total.revenue / total.units_sold << std::endl;
	} else {
		std::cerr << "No data?!" << std::endl;
		return -1;
	}
	return 0;
}
```
## 练习 7.4：
### 编写一个名为 Person 的类，使其表示人员的姓名和住址。使用 string 对象存放这些元素，接下来的练习将不断充实这个类的其他特征。
答：
```
#include <string>	

struct Person {
	std::string name;
	std::string address;
};
```
## 练习 7.5：
### 在你的 Person 类中提供一些操作使其能够返回姓名和住址。这些函数是否应该是 const 的呢？解释原因。
答：
```
#include <string>
	
struct Person {
	std::string getName() const { return name; }
	std::string getAddress() const { return address; }
	std::string name;
	std::string address;
};
```
* 这两个函数只是返回成员变量的值，不修改任何成员变量，因此它们应该被声明为 const 函数。const 成员函数可以被任何类型的对象调用，包括 const 对象、const 引用或 const 指针指向的对象。这提高了函数的可用性，可以在更多的情况下使用这个函数。
