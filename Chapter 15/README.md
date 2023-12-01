## 练习 15.1：
### 什么是虚成员？
答：
* 用于实现多态行为。当一个派生类覆盖基类中的虚函数时，通过基类指针或引用调用该函数将会根据对象的实际类型来决定调用哪个版本的函数。
## 练习 15.2:
### protected 访问说明符与 private 有何区别？
答：
* private 和 protected 的主要区别在于对派生类的访问控制。private 成员在派生类中是不可见的，而 protected 成员在派生类中是可见的。
## 练习 15.3：
### 定义你自己的 Quote 类和 print_total 函数。
答：
```
class Quote {
public:
	Quote() = default;
	Quote(const std::string &book, double sales_price) : bookNo(book), price(sales_price) { } 
	std::string isbn() const { return bookNo; }
	// 返回给定数量的书籍的销售总额
	// 派生类负责改写并使用不同的折扣计算算法
	virtual double net_price(std::size_t n) const { return n * price; }
	virtual ~Quote() = default; // 对析构函数进行动态绑定
private:
	std::string bookNo; // 书籍的 ISBN 编号
protected:
	double price = 0.0; // 代表普通状态下不打折的价格
};

// 计算并打印销售给定数量的某种书籍所得的费用
double print_total(ostream &os,	const Quote &item, size_t n){
	// 根据传入item形参的对象类型调用Quote::net_price
	// 或者 Bulk_quote::net_price
	double ret = item.net_price(n);
	os << "ISBN: " << item.isbn() // 调用 Quote：：isbn
		<< " # sold: " << n << " total due: " << ret << endl;
	return ret;
}
```
## 练习 15.4：
### 下面哪条声明语句是不正确的？请解释原因。
```
class Base { ... };
(a) class Derived : public Derived { ... };
(b) class Derived : private Base { ... };
(c) class Derived : public Base;
```
答：
```
(a) 不正确，类不能是其自身的基类。
(b) 正确。
(c) 不正确，类声明中不能包含派生列表。
```
## 练习 15.5:
### 定义你自己的Bulk_quote类。
答：
```
class Bulk_quote : public Quote { // Bulk_quote 继承自 Quote
	Bulk_quote() = default;
	Bulk_quote(const std::string& book, double p, std::size_t qty, double disc) :
		Quote(book, p), min_qty(qty), discount(disc) { }
	// 覆盖基类的函数版本以实现基于大量购买的折扣政策
	double net_price(std::size_t) const override;
private:
	std::size_t min_qty = 0; // 适用折扣政策的最低购买量
	double discount = 0.0; // 以小数表示的折扣额
};

double Bulk_quote::net_price(size_t cnt) const{
	if (cnt >= min_qty) {
		return cnt * (1 - discount) * price;
	} else {
		return cnt * price;
	}
}
```
## 练习 15.6：
### 将 Quote 和 Bulk_quote 的对象传给 15.2.1 节 （第 529 页)练习中的 print_total 函数，检查该函数是否正确。
答：
```
#include <iostream>
#include <string>

using namespace std;

class Quote {
public:
	Quote() = default;
	Quote(const std::string &book, double sales_price) : bookNo(book), price(sales_price) { }
	std::string isbn() const { return bookNo; }
	// 返回给定数量的书籍的销售总额
	// 派生类负责改写并使用不同的折扣计算算法
	virtual double net_price(std::size_t n) const { return n * price; }
	virtual ~Quote() = default; // 对析构函数进行动态绑定
private:
	std::string bookNo; // 书籍的 ISBN 编号
protected:
	double price = 0.0; // 代表普通状态下不打折的价格
};

class Bulk_quote : public Quote { // Bulk_quote 继承自 Quote
public:
	Bulk_quote() = default;
	Bulk_quote(const std::string& book, double p, std::size_t qty, double disc) :
		Quote(book, p), min_qty(qty), discount(disc) { }
	// 覆盖基类的函数版本以实现基于大量购买的折扣政策
	double net_price(std::size_t) const override;
private:
	std::size_t min_qty = 0; // 适用折扣政策的最低购买量
	double discount = 0.0; // 以小数表示的折扣额
};

double Bulk_quote::net_price(size_t cnt) const{
	if (cnt >= min_qty) {
		return cnt * (1 - discount) * price;
	} else {
		return cnt * price;
	}
}

// 计算并打印销售给定数量的某种书籍所得的费用
double print_total(ostream &os, const Quote &item, size_t n) {
	// 根据传入item形参的对象类型调用Quote::net_price
	// 或者 Bulk_quote::net_price
	double ret = item.net_price(n);
	os << "ISBN: " << item.isbn() // 调用 Quote：：isbn
		<< " # sold: " << n << " total due: " << ret << endl;
	return ret;
}

int main() {
	Quote quote("ABC", 10);
	Bulk_quote bq("ABC", 10, 10, 0.5);
	print_total(cout, quote,20);
	print_total(cout, bq, 20);

	return 0;
}
```
## 练习 15.7:
### 定义一个类使其实现一种数量受限的折扣策略,具体策略是:当购买书籍的数量不超过一个给定的限量时享受折扣,如果购买量一旦超过了限量,则超出的部分将以原价销售。
答：
```
class LimitedDiscount : public Quote {
public:
	LimitedDiscount() = default;
	LimitedDiscount(const std::string& book, double p, std::size_t limit, double disc) :
		Quote(book, p), limit_(limit), discount(disc) { }
	double net_price(size_t quantity) const {
		if (quantity <= limit_) {
			return quantity * price * (1 - discount);
		} else {
			return limit_ * price * (1 - discount) + (quantity - limit_) * price;
		}
	}
private:
	std::size_t limit_ = 0;
	double discount = 0.0;
};
```
## 练习 15.8：
### 给出静态类型和动态类型的定义。
答：
* 静态类型是指编译时变量或表达式的已知类型，它由变量的声明或表达式的上下文决定。
* 动态类型是指变量或表达式在程序运行时所引用的实际对象的类型。
## 练习 15.9:
### 在什么情况下表达式的静态类型可能与动态类型不同?请给出三个静态类型与动态类型不同的例子。
答：
* 用基类的指针或引用去绑定派生类的对象静态类型与动态类型会不同。
```
class A {};
class B :public A {};
class C :public B {};
C c;
// 动态类型都是C
B& b = c;
A* a = &b;
A* a2 = &c;
```
## 练习 15.10:
### 回忆我们在8.1节(第279页)进行的讨论,解释第284页中将ifstream 传递给Sales data的read函数的程序是如何工作的。
答：
* 因为我们可以将基类的指针或引用绑定到派生类对象上。
