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
	Sales_data& combine(const Sales_data&);
	//数据成员
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

Sales_data & Sales_data::combine(const Sales_data &rhs) {
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
	Sales_data& combine(const Sales_data&);
	//数据成员
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

Sales_data & Sales_data::combine(const Sales_data &rhs) {
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
				total.combine(trans);
			} else {
				std::cout << total.isbn() << " " << total.units_sold << " "
					<< total.revenue << " " << total.revenue / total.units_sold << std::endl;
				total.bookNo = trans.bookNo;
				total.units_sold = trans.units_sold;
				total.revenue = trans.revenue;
			}
		}
		std::cout << total.isbn() << " " << total.units_sold << " "
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
## 练习 7.6：
### 对于函数 add、read 和 print，定义你自己的版本。
答：
```
Sales_data add(const Sales_data &lhs, const Sales_data &rhs) {
	Sales_data sum = lhs;
	return sum.combine(rhs);
}

std::ostream& print(std::ostream &os, const Sales_data &itme) {
	os << itme.isbn() << " " << itme.units_sold << " "
		<< itme.revenue << " " << itme.avg_price();
	return os;
}

std::istream& read(std::istream &is, Sales_data &itme) {
	is >> itme.bookNo >> itme.units_sold >> itme.revenue;
	return is;
}
```
## 练习 7.7：
### 使用这些新函数重写 7.1.2 节（第 233 页）练习中的交易处理程序。
答：
```
#include <iostream>
#include <string>

struct Person {
	std::string getName() const { return name; }
	std::string getAddress() const { return address; }
	std::string name;
	std::string address;
};

struct Sales_data {
	//新成员：关于 Sales_data 对象的操作
	std::string isbn() const {
		return bookNo;
	}
	Sales_data& combine(const Sales_data&rhs);
	double avg_price() const;
	//数据成员
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};
// Sales_data 的非成员接口函数
Sales_data add(const Sales_data &lhs, const Sales_data &rhs);
std::ostream& print(std::ostream &os, const Sales_data &itme);
std::istream& read(std::istream &is, Sales_data &itme);

Sales_data & Sales_data::combine(const Sales_data &rhs) {
	units_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}

double Sales_data::avg_price() const {
	if (units_sold) {
		return revenue / units_sold;
	} else {
		return 0;
	}
}

Sales_data add(const Sales_data &lhs, const Sales_data &rhs) {
	Sales_data sum = lhs;
	return sum.combine(rhs);
}

std::ostream& print(std::ostream &os, const Sales_data &itme) {
	os << itme.isbn() << " " << itme.units_sold << " "
		<< itme.revenue << " " << itme.avg_price();
	return os;
}

std::istream& read(std::istream &is, Sales_data &itme) {
	is >> itme.bookNo >> itme.units_sold >> itme.revenue;
	return is;
}

int main()
{
	Sales_data total;
	if (read(std::cin, total)) {
		Sales_data trans;
		while (read(std::cin, trans)) {
			if (total.isbn() == trans.isbn()) {
				total.combine(trans);
			} else {
				print(std::cout, total) << std::endl;
				total = trans;
			}
		}
		print(std::cout, total) << std::endl;
	} else {
		std::cerr << "No data?!" << std::endl;
		return -1;
	}
	return 0;
}
```
## 练习 7.8：
### 为什么 read 函数将其 sales_data 参数定义成普通的引用，而 print 将其参数定义成常量引用？
答： 
* read 函数需要修改sales_data对象的数据成员，所以需要普通引用形参。而 print 函数不修改sales_data对象的任何成员，所以可以使用const引用形参。
## 练习 7.9：
### 对于 7.1.2 节（第 233 页）练习中的代码，添加读取和打印 Person 对象的操作。
答：
```
std::ostream& print(std::ostream &os, const Person &human) {
	os << human.getName() << " " << human.getAddress();
	return os;
}

std::istream& read(std::istream &is, Person &human) {
	is >> human.name >> human.address;
	return is;
}
```
## 练习 7.10：
### 在下面这条 if语句中，条件部分的作用是什么？
```
if (read(read(cin, data1), data2))
```
答：
* 读取输入流的数据依次输入给data1和data2。
