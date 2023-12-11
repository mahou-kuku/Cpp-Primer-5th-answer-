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
* 用绑定派生类的对象的基类指针或引用去调用虚函数时静态类型与动态类型会不同。
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
## 练习 15.11:
### 为你的Quote类体系添加一个名为debug的虚函数,令其分别显示每个类的数据成员。
答：
```
class Quote {
public:
    // ... 其他成员保持不变 ...

    virtual void debug() const {
        std::cout << "Quote - bookNo: " << bookNo << ", price: " << price << std::endl;
    }
};

class Bulk_quote : public Quote {
public:
    // ... 其他成员保持不变 ...

    void debug() const override {
        Quote::debug(); // 显示基类部分的信息
        std::cout << "Bulk_quote - min_qty: " << min_qty << ", discount: " << discount << std::endl;
    }
};
```
## 练习 15.12：
### 有必要将一个成员函数同时声明成 override 和 final 吗？ 为什么？
答：
* 将一个成员函数同时声明为 override 和 final 是完全合理的，因为这两个关键字各自有其独特的用途和意义。override 关键字可以捕获潜在的函数类型错误，final 关键字用于阻止进一步的覆盖。
## 练习 15.13:
### 给定下面的类,解释每个print函数的机理:
```
class base {
public:
	string name() { return basename; }
	virtual void print(ostream &os) { os << basename; }
private:
	string basename;
};
class derived : public base {
public:
	void print(ostream &os) { print(os); os << " " << i; }
private:
	int i;
};
```
### 在上述代码中存在问题吗？如果有，你该如何修改它？
答：
* 应该给派生类的虚函数体内的基类虚函数调用加上作用域运算符：
```
void print(ostream &os) { base::print(os); os << " " << i; }
```
## 练习 15.14：
### 给定上一题中的类以及下面这些对象，说明在运行时调用哪个函数：
```
base bobj; 	base *bp1 = &bobj; 	base &br1 = bobj;
derived dobj; 	base *bp2 = &dobj; 	base &br2 = dobj;
(a)bobj.print();	(b)dobj.print();	(c)bp1->name();
(d)bp2->name();		(e)br1.print();		(f)br2.print();
```
答：
```
(a)base::print		(b)derived::print	(c)base::name
(d)base::name		(e)base::print		(f)derived::print
```
## 练习 15.15:
### 定义你自己的Disc_quote和Bulk_quote
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

// 用于保存折扣值和购买量的类,派生类使用这些数据可以实现不同的价格策略
class Disc_quote : public Quote {
public:
	Disc_quote() = default; 
	Disc_quote(const std::string& book, double price, std::size_t qty, double disc) : 
		Quote(book, price),quantity(qty), discount(disc) { } 
	double net_price(std::size_t) const = 0;
protected:
	std::size_t quantity = 0; // 折扣适用的购买量
	double discount = 0.0; // 表示折扣的小数值
};

class Bulk_quote : public Disc_quote { // Bulk_quote 继承自 Quote
public:
	Bulk_quote() = default;
	Bulk_quote(const std::string& book, double price, std::size_t qty, double disc) :
		Disc_quote(book, price, qty, disc) { }
	// 覆盖基类的函数版本以实现基于大量购买的折扣政策
	double net_price(std::size_t) const override;
};

double Bulk_quote::net_price(size_t cnt) const {
	if (cnt >= quantity) {
		return cnt * (1 - discount) * price;
	} else {
		return cnt * price;
	}
}
```
## 练习 15.16：
### 改写你在 15.2.2 节（第 533 页）练习中编写的数量受限的折扣策略，令其继承 Disc_quote。
答：
```
class LimitedDiscount : public Disc_quote {
public:
	LimitedDiscount() = default;
	LimitedDiscount(const std::string& book, double price, std::size_t qty, double disc) :
		Disc_quote(book, price, qty, disc) { }
	double net_price(size_t limit) const {
		if (limit <= quantity) {
			return limit * price * (1 - discount);
		} else {
			return quantity * price * (1 - discount) + (limit - quantity) * price;
		}
	}
};

```
## 练习 15.17：
### 尝试定义一个 Disc_quote 的对象，看看编译器给出的错误信息是什么？
答：
* “Disc_quote”: 不能实例化抽象类
## 练习 15.18:
### 假设给定了第543页和第544页的类,同时已知每个对象的类型如注释所示,判断下面的哪些赋值语句是合法的。解释那些不合法的语句为什么不被允许:
```
Base *p = &d1; // d1 has type Pub_Derv
p = &d2; // d2 has type Priv_Derv
p = &d3; // d3 has type Prot_Derv
p = &dd1; // dd1 has type Derived_from_Public
p = &dd2; // dd2 has type Derived_from_Private
p = &dd3; // dd3 has type Derived_from_Protected
```
答：
```
Base *p = &d1; // 合法
p = &d2; // 不合法
p = &d3; // 不合法
p = &dd1; // 合法
p = &dd2; // 不合法
p = &dd3; //不合法
```
* 派生类到基类的指针转换的可行性取决于继承的类型。公有继承允许这种转换，而私有和受保护继承则不允许，除非这种转换发生在派生类或其友元的内部。
## 练习 15.19：
### 假设543页和544页的每个类都有如下形式的成员函数：
```
void memfcn(Base &b) { b = *this; }
```
### 对于每个类，分别判断上面的函数是否合法。
答：
* Derived_from_Private 类的void memfcn(Base &b)不合法。
## 练习 15.20:
### 编写代码检验你对前面两题的回答是否正确。
答：
```
class Base {
public:
	void memfcn(Base &b) { b = *this; }
	void pub_mem();
protected:
	int prot_mem; 
private:
	char priv_mem;
};

struct Pub_Derv : public Base {
	void memfcn(Base &b) { b = *this; }
};
struct Prot_Derv : protected Base {
	void memfcn(Base &b) { b = *this; }
};
struct Priv_Derv : private Base {
	void memfcn(Base &b) { b = *this; }
};

struct Derived_from_Public : public Pub_Derv {
	void memfcn(Base &b) { b = *this; }
};
struct Derived_from_Protected : public Prot_Derv {
	void memfcn(Base &b) { b = *this; }
};
struct Derived_from_Private : public Priv_Derv {
	void memfcn(Base &b) { b = *this; }	// 编译错误
};
```
## 练习 15.21：
### 从下面这些一般性抽象概念中任选一个（或者选一个你自己的)，将其对应的一组类型组织成一个继承体系:
(a)图形文件格式(如gif、tiff、jpeg、 bmp)
(b)图形基元(如方格、圆、球、圆锥)
(c) C++语言中的类型(如类、函数、成员函数)
答：
```
class GraphicFile {
public:
	virtual ~GraphicFile() {}

	virtual void open() = 0; // 打开文件的纯虚函数
	virtual void close() = 0; // 关闭文件的纯虚函数
	virtual void display() = 0; // 显示图像的纯虚函数

	// 可以添加更多通用的方法和成员变量
};

class JPEG : public GraphicFile {
public:
	void open() override {
		// 实现打开 GIF 文件的逻辑
	}

	void close() override {
		// 实现关闭 GIF 文件的逻辑
	}

	void display() override {
		// 实现显示 GIF 图像的逻辑
	}
};

class BMP : public GraphicFile {
public:
	void open() override {
		// 实现打开 BMP 文件的逻辑
	}

	void close() override {
		// 实现关闭 BMP 文件的逻辑
	}

	void display() override {
		// 实现显示 BMP 图像的逻辑
	}
};

int main() {
	GraphicFile *file = new JPEG();
	file->open();
	file->display();
	file->close();

	delete file; // 记得释放资源

	return 0;
}
```
## 练习 15.22：
### 对于你在上一题中选择的类，为其添加合适的虚函数及公有成员和受保护的成员。
答：
```
class GraphicFile {
public:
	virtual ~GraphicFile() {}

	virtual void open() = 0; // 打开文件的纯虚函数
	virtual void close() = 0; // 关闭文件的纯虚函数
	virtual void display() = 0; // 显示图像的纯虚函数
	// 公有成员函数：获取文件名
	std::string getFileName() const {
		return fileName;
	}
protected:
	std::string fileName; // 文件名
	int width;            // 图像宽度
	int height;           // 图像高度

	// 受保护的成员函数：设置图像尺寸
	void setDimensions(int w, int h) {
		width = w;
		height = h;
	}
};

class JPEG : public GraphicFile {
public:
	void open() override {
		// 实现打开 GIF 文件的逻辑
	}

	void close() override {
		// 实现关闭 GIF 文件的逻辑
	}

	void display() override {
		// 实现显示 GIF 图像的逻辑
	}
	// 添加一个公有函数来设置图像尺寸
	void setSize(int width, int height) {
		// 可以在这里添加更多逻辑，比如检查尺寸的有效性
		setDimensions(width, height); // 调用基类的受保护方法
	}
};

class BMP : public GraphicFile {
public:
	void open() override {
		// 实现打开 BMP 文件的逻辑
	}

	void close() override {
		// 实现关闭 BMP 文件的逻辑
	}

	void display() override {
		// 实现显示 BMP 图像的逻辑
	}

	void setSize(int width, int height) {
		// 可以在这里添加更多逻辑，比如检查尺寸的有效性
		setDimensions(width, height);
	}
};
```
## 练习 15.23:
### 假设第550页的D1类需要覆盖它继承而来的fcn函数,你应该如何对其进行修改?如果你修改之后fcn匹配了Base中的定义,则该节的那些调用语句将如何解析？
答：
* 需要给D1类添加一个无参数的 int fcn() override 函数以覆盖 Base::fcn() 。
* 调用语句的解析：
* bp1->fcn();：因为 bp1 是指向 Base 类型的对象，所以这将调用 Base::fcn()。
* bp2->fcn();：bp2 是基类 Base 的指针，但它实际指向 D1 对象。由于 D1 有覆盖 Base::fcn()，这个调用将解析为 D1::fcn()。
* bp3->fcn();：bp3 是基类 Base 的指针，指向 D2 对象。D2 类覆盖了 Base::fcn()，因此这个调用将解析为 D2::fcn()。
## 练习 15.24：
### 哪种类需要虚析构函数？虚析构函数必须执行什么样的操作？
答：
* 如果一个类被设计为基类，并且预期通过基类的指针或引用来处理派生类对象，那么这个类应该有一个虚析构函数。
* 虚析构函数的主要任务是执行类所需的任何清理工作，确保对象被正确地销毁。
