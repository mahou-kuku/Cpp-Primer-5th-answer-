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

class Bulk_quote : public Disc_quote { // Bulk_quote 继承自 Disc_quote
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
## 练习 15.25:
### 我们为什么为Disc_quote定义一个默认构造函数? 如果去除掉该构造函数的话会对 Bulk_quote 的行为产生什么影响？
答：
* 因为Disc_quote还有其他构造函数，编译器不会再隐式地提供合成版本。如果去掉Disc_quote的默认构造函数，会导致Bulk_quote中请求合成默认构造函数的语句失效。
## 练习 15.26:
### 定义Quote和Bulk_quote的拷贝控制成员,令其与合成的版本行为一致。为这些成员以及其他构造函数添加打印状态的语句，使得我们能够知道正在运行哪个程序。使用这些类编写程序,预测程序将创建和销毁哪些对象。重复实验,不断比较你的预测和实际输出结果是否相同,直到预测完全准确再结束。
答：
```
#include <iostream>
#include <string>

using namespace std;

class Quote {
public:
	// 构造函数
	Quote() { cout << "Quote: default constructor\n"; }
	Quote(const std::string &book, double sales_price) : bookNo(book), price(sales_price) {
		cout << "Quote: constructor taking string and double\n";
	}
	// 拷贝构造函数
	Quote(const Quote& q) : bookNo(q.bookNo), price(q.price) {
		cout << "Quote: copy constructor\n";
	}
	// 拷贝赋值运算符
	Quote& operator=(const Quote& rhs) {
		if (this != &rhs) {
			bookNo = rhs.bookNo;
			price = rhs.price;
		}
		cout << "Quote: copy assignment operator\n";
		return *this;
	}
	// 析构函数
	virtual ~Quote() {
		cout << "Quote: destructor\n";
	}

	std::string isbn() const { return bookNo; }
	virtual double net_price(std::size_t n) const { return n * price; }
private:
	std::string bookNo;
protected:
	double price = 0.0;
};

class Disc_quote : public Quote {
public:
	Disc_quote() = default; 
	Disc_quote(const std::string& book, double price, std::size_t qty, double disc) : 
		Quote(book, price),quantity(qty), discount(disc) { } 
	double net_price(std::size_t) const = 0;
protected:
	std::size_t quantity = 0;
	double discount = 0.0;
};

class Bulk_quote : public Disc_quote { // Bulk_quote 继承自 Disc_quote
public:
	// 构造函数
	Bulk_quote() { cout << "Bulk_quote: default constructor\n"; }
	Bulk_quote(const std::string& book, double price, std::size_t qty, double disc)
		: Disc_quote(book, price, qty, disc) {
		cout << "Bulk_quote: constructor taking string, double, size_t, and double\n";
	}
	// 拷贝构造函数
	Bulk_quote(const Bulk_quote& bq) : Disc_quote(bq) {
		cout << "Bulk_quote: copy constructor\n";
	}
	// 拷贝赋值运算符
	Bulk_quote& operator=(const Bulk_quote& rhs) {
		Disc_quote::operator=(rhs);
		cout << "Bulk_quote: copy assignment operator\n";
		return *this;
	}
	// 析构函数
	~Bulk_quote() override {
		cout << "Bulk_quote: destructor\n";
	}
	double net_price(std::size_t cnt) const override {
		if (cnt >= quantity) {
			return cnt * (1 - discount) * price;
		} else {
			return cnt * price;
		}
	}
};

int main() {
	Quote q;
	Bulk_quote bq;

	Quote q2 = q; // 使用 Quote 的拷贝构造函数
	Bulk_quote bq2 = bq; // 使用 Bulk_quote 的拷贝构造函数

	q2 = q; // 使用 Quote 的拷贝赋值运算符
	bq2 = bq; // 使用 Bulk_quote 的拷贝赋值运算符

	return 0;
}
```
## 练习 15.27:
### 重新定义你的Bulk quote类,令其继承构造函数。
答：
```
class Bulk_quote : public Disc_quote { // Bulk_quote 继承自 Disc_quote
	using Disc_quote::Disc_quote;
public:
	//Bulk_quote() = default;
	//Bulk_quote(const std::string& book, double price, std::size_t qty, double disc) :
	//	Disc_quote(book, price, qty, disc) { }
	// 覆盖基类的函数版本以实现基于大量购买的折扣政策
	double net_price(std::size_t) const override;
};
```
## 练习 15.28:
### 定义一个存放Quote对象的vector,将Bulk_quote对象传入其中。  计算 vector中所有元素总的 net_price。
答：
```
int main() {
	vector<Quote> vec;
	for (int i = 0; i != 10; ++i) {
		vec.push_back(Bulk_quote("ABC", 10, 10, 0.5));
	}

	double total = 0;
	for (const auto&q : vec) {
		total += q.net_price(20);
	}
	cout << total << endl;

	return 0;
}
```
## 练习 15.29:
### 再运行一次你的程序,这次传入Quote对象的shared_ptr。如果这次计算出的总额与之前的程序不一致，解释为什么；如果一致，也请说明原因。
答：
* 不一致，使用Quote对象的shared_ptr调用虚函数会发生动态绑定，根据实际的动态类型来决定调用哪个版本的函数。
```
int main() {
	vector<shared_ptr<Quote>> vec;
	for (int i = 0; i != 10; ++i) {
		vec.push_back(make_shared<Bulk_quote>(Bulk_quote("ABC", 10, 10, 0.5)));
	}

	double total = 0;
	for (const auto&q : vec) {
		total += q->net_price(20);
	}
	cout << total << endl;

	return 0;
}
```
## 练习 15.30：
### 编写你自己的 Basket 类，用它计算上一个练习中交易记录的总价格。
答：
```
#include <iostream>
#include <string>
#include <memory>
#include <set>

using namespace std;

class Quote {
public:
	Quote() = default;
	Quote(const std::string &book, double sales_price) : bookNo(book), price(sales_price) { }
	std::string isbn() const { return bookNo; }
	virtual ~Quote() = default; // 对析构函数进行动态绑定
	// 返回给定数量的书籍的销售总额
	// 派生类负责改写并使用不同的折扣计算算法
	virtual double net_price(std::size_t n) const { return n * price; }
	// 该虚函数返回当前对象的一份动态分配的拷贝
	virtual Quote* clone() const & { return new Quote(*this); }
	virtual Quote* clone() && {return new Quote(std::move(*this)); }
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
		Quote(book, price), quantity(qty), discount(disc) { }
	double net_price(std::size_t) const = 0;
protected:
	std::size_t quantity = 0; // 折扣适用的购买量
	double discount = 0.0; // 表示折扣的小数值
};

class Bulk_quote : public Disc_quote { // Bulk_quote 继承自 Disc_Quote
	using Disc_quote::Disc_quote;
public:
	// 覆盖基类的函数版本以实现基于大量购买的折扣政策
	double net_price(std::size_t cnt) const override {
		if (cnt >= quantity) {
			return cnt * (1 - discount) * price;
		} else {
			return cnt * price;
		}
	}
	// 该虚函数返回当前对象的一份动态分配的拷贝
	Bulk_quote* clone() const & { return new Bulk_quote(*this); }
	Bulk_quote* clone() && {return new Bulk_quote(std::move(*this)); }
};

// 计算并打印销售给定数量的某种书籍所得的费用
double print_total(ostream &os, const Quote &item, size_t n) {
	// 根据传入item形参的对象类型调用Quote::net_price
	// 或者 Bulk_quote::net_price
	double ret = item.net_price(n);
	os << "ISBN: " << item.isbn() // 调用 Quote：：isbn
		<< " # sold: " << n << " total due: " << ret << endl;
	return ret;
}

class Basket {
public:
	// Basket 使用合成的默认构造函数和拷贝控制成员
	void add_item(const Quote& sale) { // 拷贝给定的对象
		items.insert(std::shared_ptr<Quote>(sale.clone()));
	}
	void add_item(Quote&& sale) { // 移动给定的对象
		items.insert(std::shared_ptr<Quote>(std::move(sale).clone()));
	}
	// 打印每本书的总价和购物篮中所有书的总价
	double total_receipt(std::ostream&) const;
private:
	// 该函数用于比较 shared_ptr，multiset 成员会用到它
	static bool compare(const std::shared_ptr<Quote> &lhs,
		const std::shared_ptr<Quote> &rhs) {
		return lhs->isbn() < rhs->isbn();
	}
	// multiset 保存多个报价，按照 compare 成员排序
	std::multiset<std::shared_ptr<Quote>, decltype(compare)*> items{ compare };
};

double Basket::total_receipt(ostream &os) const {
	double sum = 0.0; // 保存实时计算出的总价格
	// iter 指向ISBN相同的一批元素中的第一个
	// upper_bound 返回一个迭代器，该迭代器指向这批元素的尾后位置
	for (auto iter = items.cbegin(); iter != items.cend(); iter = items.upper_bound(*iter)) {
		// 我们知道在当前的 Basket 中至少有一个该关键字的元素
		// 打印该书籍对应的项目
		sum += print_total(os, **iter, items.count(*iter));
	}
	os << "Total Sale: " << sum << endl; // 打印最终的总价格
	return sum;
}

int main() {
	Basket bsk;
	bsk.add_item(Quote("123", 45));
	bsk.add_item(Bulk_quote("345", 45, 3, .15));
	bsk.add_item(Bulk_quote("345", 45, 3, .15));
	bsk.add_item(Bulk_quote("345", 45, 3, .15));
	bsk.total_receipt(cout);

	return 0;
}
```
## 练习 15.31:
### 已知s1、 s2、s3和 s4都是 string,判断下面的表达式分别创建了什么样的对象：
```
(a) Query(s1) | Query(s2) & ~ Query(s3);
(b) Query(s1) | (Query(s2) & ~ Query(s3));
(c) (Query(s1) & (Query(s2)) | (Query(s3) & Query(s4)));
```
答：
```
(a) NotQuery、WordQuery、AndQuery、OrQuery 。
(b) NotQuery、WordQuery、AndQuery、OrQuery 。
(c) WordQuery、AndQuery、OrQuery 。
```
## 练习 15.32：
### 当一个 Query 类型的对象被拷贝、移动、赋值或销毁时，将分别发生什么？
答：
* 将分别调用合成版本的拷贝、移动、赋值或析构函数。
## 练习 15.33:
### 当一个Query_base类型的对象被拷贝、移动、赋值或销毁时，将分别发生什么？
答：
* Query_base是一个抽象基类，不能创建一个抽象基类的对象。
## 练习 15.34：
### 针对图 15.3（第 565 页）构建的表达式：
```
(a)列举出在处理表达式的过程中执行的所有构造函数。
(b)列举出cout<<q所调用的rep
(c)列举出q.eval()所调用的eval
```
答：
```
(a)Query(const std::string&)
   WordQuery(const std::string&)
   Query_base()
   AndQuery(const Query&, const Query&)
   BinaryQuery(const Query&, const Query&, std::string)
   Query(std::shared_ptr<Query_base>)
   OrQuery(const Query&, const Query&)

(b)BinaryQuery的rep。
(c)OrQuery的eval。
```
## 练习 15.35：
### 实现 Query 类和 Query_base 类，其中需要定义 rep 而无须定义 eval。
答：
```
// 这是一个抽象基类，具体的查询类型从中派生，所有成员都是private的
class Query_base {
	friend class Query;
protected:
	using line_no = TextQuery::line_no; // 用于 eval 函数
	virtual ~Query_base() = default;
private:
	// eval 返回与当前 Query 匹配的 QueryResult
	virtual QueryResult eval(const TextQuery&) const = 0;
	// rep 是表示查询的一个 string
	virtual std::string rep() const = 0;
};

// 这是一个管理Query_base继承体系的接口类
class Query {
	// 这些运算符需要访问接受shared_ptr的构造函数，而该函数是私有的
	friend Query operator~(const Query &);
	friend Query operator|(const Query&, const Query&);
	friend Query operator&(const Query&, const Query&);
public:
	Query(const std::string&); // 构建一个新的 WordQuery
	// 接口函数：调用对应的 Query_base 操作
	QueryResult eval(const TextQuery &t) const ;
	std::string rep() const { return q->rep(); }
private:
	Query(std::shared_ptr<Query_base> query) : q(query) { }
	std::shared_ptr<Query_base> q;
};
```
## 练习 15.36:
### 在构造函数和rep成员中添加打印语句,运行你的代码以检验你对本节第一个练习中(a)、(b)两小题的回答是否正确。
答：
* 正确。
## 练习 15.37:
### 如果在派生类中含有shared_ptr<Query_base>类型的成员而非Query 类型的成员,则你的类需要做出怎样的改变?
答：
* 需要将相应函数的形参改为shared_ptr<Query_base>，对eval、rep的调用改为用->运算符调用，以及修改~、&、|重载运算符的形参、返回类型。
## 练习 15.38：
### 下面的声明合法吗？如果不合法，请解释原因；如果合法，请指出该声明的含义。
```
BinaryQuery a = Query("fiery") & Query("bird");
AndQuery b = Query("fiery") & Query("bird");
OrQuery c = Query("fiery") & Query("bird");
```
答：
* 都不合法，BinaryQuery是抽象类，不能创建一个抽象基类对象。无法从“Query”转换为“AndQuery”或“OrQuery” 。
## 练习 15.39:
### 实现Query类和Query_base类,求图15.3 (第565页)中表达式的值并打印相关信息,验证你的程序是否正确。
答：
```
#include <iostream>
#include <fstream>
#include <sstream>
#include <vector>
#include <map>
#include <set>
#include <string>
#include <memory>
#include <algorithm>
#include <iterator>

using namespace std;

class QueryResult; // 前向声明
class TextQuery {
public:
	using line_no = vector<string>::size_type;
	TextQuery(ifstream&);
	QueryResult query(const string&) const;
private:
	shared_ptr<vector<string>> file; // 输入文件
									 // 每个单词到它所在的行号的集合的映射
	map<string, shared_ptr<set<line_no>>> wm;
};

class QueryResult {
	friend std::ostream& print(std::ostream&, const QueryResult&);
public:
	QueryResult(string s, shared_ptr<set<TextQuery::line_no>> p,
		shared_ptr<vector<string>> f) : sought(s), lines(p), file(f) { }
	set<TextQuery::line_no>::iterator begin() const { return lines->begin(); }
	set<TextQuery::line_no>::iterator end() const { return lines->end(); }
	shared_ptr<vector<string>> get_file() const { return file; }
private:
	string sought; // 查询单词
	shared_ptr<set<TextQuery::line_no>> lines; // 出现的行号
	shared_ptr<vector<string>> file; // 输入文件
};

// 读取输入文件并建立单词到行号的映射
TextQuery::TextQuery(ifstream &is) : file(new vector<string>) {
	string text;
	while (getline(is, text)) { // 对文件中每一行
		file->push_back(text); // 保存此行文本
		int n = file->size() - 1; // 当前行号
		istringstream line(text); // 将行文本分解为单词
		string word;
		while (line >> word) { // 对行中每个单词
							   // 如果单词不在 wm中，以之为下标在wm中添加一项
			auto &lines = wm[word]; // lines 是一个 shared_ptr
			if (!lines) // 在我们第一次遇到这个单词时，此指针为空
				lines.reset(new set<line_no>); // 分配一个新的 set
			lines->insert(n); // 将此行号插入 set 中
		}
	}
}

QueryResult TextQuery::query(const string &sought) const {
	// 如果未找到 sought，我们将返回一个指向此 set 的指针
	static shared_ptr<set<line_no>> nodata(new set<line_no>);
	// 使用 find 而不是下标运算符来查找单词，避免将单词添加到 wm 中！
	auto loc = wm.find(sought);
	if (loc == wm.end()) {
		return QueryResult(sought, nodata, file); // 未找到
	} else {
		return QueryResult(sought, loc->second, file);
	}
}

ostream &print(ostream & os, const QueryResult &qr) {
	// 如果找到了单词,打印出现次数和所有出现的位置
	os << qr.sought << " occurs " << qr.lines->size() << " " << "times" << endl;
	// 打印单词出现的每一行
	for (auto num : *qr.lines) { // 对 set 中每个单词
								 // 避免行号从 0 开始给用户带来的困惑
		os << "\t(line " << num + 1 << ") " << *(qr.file->begin() + num) << endl;
	}
	return os;
}

// 这是一个抽象基类，具体的查询类型从中派生，所有成员都是private的
class Query_base {
	friend class Query;
protected:
	using line_no = TextQuery::line_no; // 用于 eval 函数
	virtual ~Query_base() = default;
private:
	// eval 返回与当前 Query 匹配的 QueryResult
	virtual QueryResult eval(const TextQuery&) const = 0;
	// rep 是表示查询的一个 string
	virtual std::string rep() const = 0;
};

// 这是一个管理Query_base继承体系的接口类
class Query {
	// 这些运算符需要访问接受shared_ptr的构造函数，而该函数是私有的
	friend Query operator~(const Query &);
	friend Query operator|(const Query&, const Query&);
	friend Query operator&(const Query&, const Query&);
public:
	Query(const std::string&); // 构建一个新的 WordQuery
	// 接口函数：调用对应的 Query_base 操作
	QueryResult eval(const TextQuery &t) const { return q->eval(t); }
	std::string rep() const { return q->rep(); }
private:
	Query(std::shared_ptr<Query_base> query) : q(query) { }
	std::shared_ptr<Query_base> q;
};

class WordQuery : public Query_base {
	friend class Query; // Query使用WordQuery构造函数
	WordQuery(const std::string &s) : query_word(s) { }
	// 具体的类：WordQuery 将定义所有继承而来的纯虚函数
	QueryResult eval(const TextQuery &t) const { return t.query(query_word); }
	std::string rep() const { return query_word; }
	std::string query_word; // 要查找的单词
};

// 能定义接受string的Query构造函数了
inline Query::Query(const std::string &s) : q(new WordQuery(s)) { }

class NotQuery : public Query_base {
	friend Query operator~(const Query &); 
	NotQuery(const Query &q) : query(q) { }
	// 具体的类：NotQuery 将定义所有继承而来的纯虚函数
	std::string rep() const { return "~(" + query.rep() + ")"; }
	// 返回运算对象的结果set中不存在的行
	QueryResult eval(const TextQuery& text) const {
		// 通过Query运算对象对 eval 进行虚调用
		auto result = query.eval(text);
		// 开始时结果 set 为空
		auto ret_lines = make_shared<set<line_no>>();
		// 我们必须在运算对象出现的所有行中进行迭代
		auto beg = result.begin(), end = result.end();
		// 对于输入文件的每一行，如果该行不在 result 当中，则将其添加到 ret_lines
		auto sz = result.get_file()->size();
		for (size_t n = 0; n != sz; ++n) {
			// 如果我们还没有处理完 result 的所有行
			// 检查当前行是否存在
			if (beg == end || *beg != n) {
				ret_lines->insert(n); // 如果不在 result当中,添加这一行
			} else if (beg != end) {
				++beg; // 否则继续获取result的下一行(如果有的话)
			}
		} return QueryResult(rep(), ret_lines, result.get_file());
	}
	Query query;
};
inline Query operator~(const Query &operand) {
	return std::shared_ptr<Query_base>(new NotQuery(operand));
}

class BinaryQuery : public Query_base {
protected:
	BinaryQuery(const Query &l, const Query &r, std::string s) :
		lhs(l), rhs(r), opSym(s) { }
	// 抽象类:BinaryQuery 不定义 eval
	std::string rep() const {
		return "(" + lhs.rep() + " " + opSym + " " + rhs.rep() + ")";
	}
	Query lhs, rhs; // 左侧和右侧运算对象
	std::string opSym; // 运算符的名字
};

class AndQuery : public BinaryQuery {
	friend Query operator& (const Query&, const Query&);
	AndQuery(const Query &left, const Query &right) : BinaryQuery(left, right, "&") { }
	// 具体的类: AndQuery继承了rep并且定义了其他纯虚函数
	// 返回运算对象查询结果 set 的交集
	QueryResult eval(const TextQuery& text) const {
		// 通过Query运算对象进行的虚调用,以获得运算对象的查询结果set
		auto left = lhs.eval(text), right = rhs.eval(text);
		// 保存 left 和 right 交集的 set
		auto ret_lines = make_shared<set<line_no>>();
		// 将两个范围的交集写入一个目的迭代器中
		// 本次调用的目的迭代器向ret添加元素
		set_intersection(left.begin(), left.end(), right.begin(), right.end(),
			inserter(*ret_lines, ret_lines->begin()));
		return QueryResult(rep(), ret_lines, left.get_file());
	}
};
inline Query operator&(const Query &lhs, const Query &rhs) {
	return std::shared_ptr<Query_base>(new AndQuery(lhs, rhs));
}

class OrQuery : public BinaryQuery {
	friend Query operator|(const Query&, const Query&);
	OrQuery(const Query &left, const Query &right) : BinaryQuery(left, right, "|") { }
	// 返回运算对象查询结果set的并集
	QueryResult eval(const TextQuery& text) const {
		// 通过Query成员1hs和rhs进行的虚调用
		// 调用 eval 返回每个运算对象的 QueryResult
		auto right = rhs.eval(text), left = lhs.eval(text);
		// 将左侧运算对象的行号拷贝到结果 set 中
		auto ret_lines = make_shared<set<line_no>>(left.begin(), left.end());
		// 插入右侧运算对象所得的行号
		ret_lines->insert(right.begin(), right.end());
		// 返回一个新的 QueryResult，它表示 lhs 和 rhs 的并集
		return QueryResult(rep(), ret_lines, left.get_file());
	}
};
inline Query operator|(const Query &lhs, const Query &rhs) {
	return std::shared_ptr<Query_base>(new OrQuery(lhs, rhs));
}

std::ostream& operator<<(std::ostream &os, const QueryResult &qr){
	return print(cout, qr);
}

int main() {
	ifstream ifs("example.txt");
	TextQuery tq(ifs);
	Query q = Query("fiery") & Query("bird") | Query("wind");
	auto qr = q.eval(tq);
	cout << qr << endl;

	return 0;
}
```
## 练习 15.40：
### 在 OrQuery 的 eval 函数中，如果 rhs 成员返回的是空集将发生什么？如果 lhs 是空集呢？如果 1hs 和 rhs 都是空集又将发生什么？
答：
* rhs 成员返回的是空集则只打印左侧侧运算对象的查询结果，lhs为空集则单独打印查询右侧运算对象，都是空集则查询结果将为0。
## 练习 15.41：
### 重新实现你的类，这次使用指向 Query_base 的内置指针而非shared_ptr。请注意，做出上述改动后你的类将不能再使用合成的拷贝控制成员。
答：
```
// 这是一个抽象基类，具体的查询类型从中派生，所有成员都是private的
class Query_base {
	friend class Query;
protected:
	using line_no = TextQuery::line_no; // 用于 eval 函数
	virtual ~Query_base() = default;
private:
	// eval 返回与当前 Query 匹配的 QueryResult
	virtual QueryResult eval(const TextQuery&) const = 0;
	// rep 是表示查询的一个 string
	virtual std::string rep() const = 0;
	// 为了支持深拷贝和正确地复制多态对象，在所有派生类中对其进行覆盖。
	virtual Query_base* clone() const = 0;
};

class Query {
	friend Query operator~(const Query &);
	friend Query operator|(const Query&, const Query&);
	friend Query operator&(const Query&, const Query&);

public:
	Query(const std::string &s); // 构建一个新的 WordQuery
	Query(const Query &other); // 拷贝构造函数
	Query& operator=(const Query &other); // 拷贝赋值运算符
	~Query(); // 析构函数

			  // 接口函数：调用对应的 Query_base 操作
	QueryResult eval(const TextQuery &t) const { return q->eval(t); }
	std::string rep() const { return q->rep(); }

private:
	Query(Query_base* query) : q(query) { }
	Query_base *q;
};
Query::Query(const Query &other) : q(nullptr) {
	if (other.q) {
		q = other.q->clone();
	}
}

Query& Query::operator=(const Query &other) {
	if (&other != this) {
		delete q;
		q = other.q ? other.q->clone() : nullptr;
	}
	return *this;
}

Query::~Query() {
	delete q;
}

class WordQuery : public Query_base {
	friend class Query; // Query使用WordQuery构造函数
	WordQuery(const std::string &s) : query_word(s) { }
	// 具体的类：WordQuery 将定义所有继承而来的纯虚函数
	QueryResult eval(const TextQuery &t) const { return t.query(query_word); }
	std::string rep() const { return query_word; }
	WordQuery* clone() const override { return new WordQuery(*this); }
	std::string query_word; // 要查找的单词
};

// 能定义接受string的Query构造函数了
inline Query::Query(const std::string &s) : q(new WordQuery(s)) { }

class NotQuery : public Query_base {
	friend Query operator~(const Query &); 
	NotQuery(const Query &q) : query(q) { }
	// 具体的类：NotQuery 将定义所有继承而来的纯虚函数
	std::string rep() const { return "~(" + query.rep() + ")"; }
	// 返回运算对象的结果set中不存在的行
	QueryResult eval(const TextQuery& text) const {
		// 通过Query运算对象对 eval 进行虚调用
		auto result = query.eval(text);
		// 开始时结果 set 为空
		auto ret_lines = make_shared<set<line_no>>();
		// 我们必须在运算对象出现的所有行中进行迭代
		auto beg = result.begin(), end = result.end();
		// 对于输入文件的每一行，如果该行不在 result 当中，则将其添加到 ret_lines
		auto sz = result.get_file()->size();
		for (size_t n = 0; n != sz; ++n) {
			// 如果我们还没有处理完 result 的所有行
			// 检查当前行是否存在
			if (beg == end || *beg != n) {
				ret_lines->insert(n); // 如果不在 result当中,添加这一行
			} else if (beg != end) {
				++beg; // 否则继续获取result的下一行(如果有的话)
			}
		} return QueryResult(rep(), ret_lines, result.get_file());
	}
	NotQuery* clone() const override { return new NotQuery(*this); }
	Query query;
};
inline Query operator~(const Query &operand) {
	return new NotQuery(operand);
}

class BinaryQuery : public Query_base {
protected:
	BinaryQuery(const Query &l, const Query &r, std::string s) :
		lhs(l), rhs(r), opSym(s) { }
	// 抽象类:BinaryQuery 不定义 eval
	std::string rep() const {
		return "(" + lhs.rep() + " " + opSym + " " + rhs.rep() + ")";
	}
	Query lhs, rhs; // 左侧和右侧运算对象
	std::string opSym; // 运算符的名字
};

class AndQuery : public BinaryQuery {
	friend Query operator& (const Query&, const Query&);
	AndQuery(const Query &left, const Query &right) : BinaryQuery(left, right, "&") { }
	// 具体的类: AndQuery继承了rep并且定义了其他纯虚函数
	// 返回运算对象查询结果 set 的交集
	QueryResult eval(const TextQuery& text) const {
		// 通过Query运算对象进行的虚调用,以获得运算对象的查询结果set
		auto left = lhs.eval(text), right = rhs.eval(text);
		// 保存 left 和 right 交集的 set
		auto ret_lines = make_shared<set<line_no>>();
		// 将两个范围的交集写入一个目的迭代器中
		// 本次调用的目的迭代器向ret添加元素
		set_intersection(left.begin(), left.end(), right.begin(), right.end(),
			inserter(*ret_lines, ret_lines->begin()));
		return QueryResult(rep(), ret_lines, left.get_file());
	}
	AndQuery* clone() const override { return new AndQuery(*this); }
};
inline Query operator&(const Query &lhs, const Query &rhs) {
	return new AndQuery(lhs, rhs);
}

class OrQuery : public BinaryQuery {
	friend Query operator|(const Query&, const Query&);
	OrQuery(const Query &left, const Query &right) : BinaryQuery(left, right, "|") { }
	// 返回运算对象查询结果set的并集
	QueryResult eval(const TextQuery& text) const {
		// 通过Query成员1hs和rhs进行的虚调用
		// 调用 eval 返回每个运算对象的 QueryResult
		auto right = rhs.eval(text), left = lhs.eval(text);
		// 将左侧运算对象的行号拷贝到结果 set 中
		auto ret_lines = make_shared<set<line_no>>(left.begin(), left.end());
		// 插入右侧运算对象所得的行号
		ret_lines->insert(right.begin(), right.end());
		// 返回一个新的 QueryResult，它表示 lhs 和 rhs 的并集
		return QueryResult(rep(), ret_lines, left.get_file());
	}
	OrQuery* clone() const override { return new OrQuery(*this); }
};
inline Query operator|(const Query &lhs, const Query &rhs) {
	return new OrQuery(lhs, rhs);
}
```
## 练习 15.42：
### 从下面的几种改进中选择一种，设计并实现它：
```
(a)按句子查询并打印单词,而不再是按行打印。
(b)引入一个历史系统,用户可以按编号查阅之前的某个查询,并可以在其中增加内容或者将其与其他查询组合。
(c)允许用户对结果做出限制,比如从给定范围的行中挑出匹配的进行显示。
```
答：
```
// (a) 替换TextQuery的构造函数
// 读取输入文件并建立单词到行号的映射
TextQuery::TextQuery(ifstream &is) : file(new vector<string>) {
	string line, sentence;
	line_no lineNo = 0;
	while (getline(is, line)) { // 逐行读取文本
		sentence += line + " "; // 将行添加到当前句子中
		// 检查当前句子是否结束
		if (line.find('.') != string::npos || line.find('!') != string::npos || line.find('?') != string::npos) {
			file->push_back(sentence); // 保存当前句子
			istringstream sentenceStream(sentence);
			string word;
			while (sentenceStream >> word) {
				auto &lines = wm[word];
				if (!lines)
					lines.reset(new set<line_no>);
				lines->insert(lineNo);
			}
			++lineNo;
			sentence.clear(); // 清空当前句子，准备下一个句子
		}
	}
}
```
