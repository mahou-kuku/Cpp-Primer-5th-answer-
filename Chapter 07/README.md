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
## 练习 7.11：
### 在你的 Sales_data 类中添加构造函数，然后编写一段程序令其用到每个构造函数。
答：
```
#include <iostream>
#include <string>

struct Sales_data {
	Sales_data() = default;
	Sales_data(const std::string &s) :bookNo(s) {}
	Sales_data(const std::string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {}
	Sales_data(std::istream &is);

	std::string isbn() const {
		return bookNo;
	}
	Sales_data& combine(const Sales_data&rhs);
	double avg_price() const;

	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

Sales_data add(const Sales_data &lhs, const Sales_data &rhs) {
	Sales_data sum = lhs;
	return sum.combine(rhs);
}

std::ostream& print(std::ostream &os, const Sales_data &item) {
	os << item.isbn() << " " << item.units_sold << " "
		<< item.revenue << " " << item.avg_price();
	return os;
}

std::istream& read(std::istream &is, Sales_data &item) {
	double price = 0;
	is >> item.bookNo >> item.units_sold >> price;
	item.revenue = item.units_sold*price;
	return is;
}

Sales_data::Sales_data(std::istream &is) {
	read(is, *this);
}

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

int main()
{
	Sales_data item_1;
	Sales_data item_2("0-201-78345-X");
	Sales_data item_3("0-201-78345-X", 3, 20);
	Sales_data item_4(std::cin);

	print(std::cout, item_1) << std::endl;
	print(std::cout, item_2) << std::endl;
	print(std::cout, item_3) << std::endl;
	print(std::cout, item_4) << std::endl;

	return 0;
}
```
## 练习 7.12:
### 把只接受一个 istream作为参数的构造函数定义移到类的内部。
答：
```
struct Sales_data;  // 前向声明
std::istream& read(std::istream &is, Sales_data &item);

struct Sales_data {
	Sales_data() = default;
	Sales_data(const std::string &s) :bookNo(s) {}
	Sales_data(const std::string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {}
	Sales_data(std::istream &is) {
		read(is, *this);
	}

	std::string isbn() const {
		return bookNo;
	}
	Sales_data& combine(const Sales_data&rhs);
	double avg_price() const;

	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

std::istream& read(std::istream &is, Sales_data &item) {
	double price = 0;
	is >> item.bookNo >> item.units_sold >> price;
	item.revenue = item.units_sold*price;
	return is;
}
```
## 练习 7.13:
### 使用istream构造函数重写第229页的程序。
答：
```
	Sales_data total(std::cin);
	if (std::cin) {
		Sales_data trans(std::cin);
		while (std::cin) {
			if (total.isbn() == trans.isbn()) {
				total.combine(trans);
			} else {
				print(std::cout, total) << std::endl;
				total = trans;
			}
			read(std::cin,trans);
		}
		print(std::cout, total) << std::endl;
	} else {
		std::cerr << "No data?!" << std::endl;
	}
```
## 练习 7.14：
### 编写一个构造函数，令其用我们提供的类内初始值显式地初始化成员。
答：
```
// 显式初始化
Sales_data::Sales_data(): bookNo(""), units_sold(0), revenue(0.0) {
    
}
```
## 练习 7.15:
### 为你的Person类添加正确的构造函数。
答：
```
struct Person {
	Person() = default;
	Person(const std::string &s_1, const std::string &s_2) :name(s_1), address(s_2){}
	Person(std::istream &is);
	std::string getName() const { return name; }
	std::string getAddress() const { return address; }
	std::string name;
	std::string address;
};

// Person 的接口函数声明
std::ostream& print(std::ostream &os, const Person &item);
std::istream& read(std::istream &is, Person &item);

Person::Person(std::istream &is) {
	read(is, *this);
}

std::ostream& print(std::ostream &os, const Person &human) {
	os << human.getName() << " " << human.getAddress();
	return os;
}

std::istream& read(std::istream &is, Person &human) {
	is >> human.name >> human.address;
	return is;
}
```
## 练习 7.16：
### 在类的定义中对于访问说明符出现的位置和次数有限定吗？如果有，是什么？什么样的成员应该定义在public说明符之后?什么样的成员应该定义在private 说明符之后？
答：
* 访问说明符的出现位置和次数并没有限制。
* 当希望成员在类的外部被访问，如接口，通常被声明为public。类的内部实现细节，如数据成员，通常被声明为 private。
## 练习 7.17:
### 使用class和struct时有区别吗?如果有,是什么?
答：
* 在C++中，class 和 struct 的主要区别在于他们默认的访问级别。如果不指定访问修饰符，class 中的成员默认为 private 访问权限，而 struct 中的成员默认为 public 访问权限。
## 练习 7.18：
### 封装是何含义？它有什么用处？
答：
* 封装的概念：隐藏内部实现，只暴露接口。
* 封装可以使代码更容易维护和理解，同时也可以防止外部代码错误地访问和修改内部状态。
## 练习 7.19:
### 在你的Person 类中,你将把哪些成员声明成public 的?哪些声明成private 的？解释你这样做的原因。
答：
* getName()和getAddress()两个成员函数应该被声明为public，它们是Person类对外提供的接口，允许外部的代码获取Person对象的name和address的值。同时，由于这两个函数都是const的，它们不会修改Person对象的状态，保证了数据的安全性。此外，Person类的构造函数也应该声明为public，因为我希望外部的代码能够创建Person对象。
* 应该将name和address两个数据成员声明为private，这可以使类的内部状态能被良好地封装，对外部代码隐藏实现的具体细节，确保这些数据成员不会被外部的代码直接访问和修改。
## 练习 7.20：
### 友元在什么时候有用?请分别列举出使用友元的利弊。
答：
* 当某个函数需要访问类的私有成员，但又不能或者不应该是类的成员函数时，需要使用友元函数。或者当两个类需要共享一些数据，但又不希望完全公开这些数据时，可以将其中一个类声明为另一个类的友元。
* 优点：
* 更大的灵活性：友元机制提供了一种方式，使得非类成员函数也能访问类的私有成员。
* 对于某些函数，使用友元是必要的。
* 缺点：
* 破坏封装：理论上，类的私有成员只应该能被类的成员函数访问。友元打破了这种封装，让外部函数可以访问类的私有成员。这可能会绕过成员函数的实现导致数据被不恰当地修改。
* 增加依赖：当将一个函数或者类声明为友元，就增加了类与这个函数或者类之间的耦合。这可能会让代码更难以维护，因为需要在修改类的时候同时考虑这些友元的影响。
## 练习 7.21：
### 修改你的 Sales_data 类使其隐藏实现的细节。你之前编写的关于Sales_data 操作的程序应该继续使用，借助类的新定义重新编译该程序，确保其工作正常。
答：
```
#include <iostream>
#include <string>

struct Sales_data {
	friend Sales_data add(const Sales_data &lhs, const Sales_data &rhs);
	friend std::ostream& print(std::ostream &os, const Sales_data &item);
	friend std::istream& read(std::istream &is, Sales_data &item);

	Sales_data() = default;
	Sales_data(const std::string &s) :bookNo(s) {}
	Sales_data(const std::string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {}
	Sales_data(std::istream &is);

	std::string isbn() const {
		return bookNo;
	}
	Sales_data& combine(const Sales_data&rhs);
private:
	double avg_price() const;

	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};
Sales_data add(const Sales_data &lhs, const Sales_data &rhs);
std::ostream& print(std::ostream &os, const Sales_data &item);
std::istream& read(std::istream &is, Sales_data &item);

Sales_data::Sales_data(std::istream &is) {
	read(is, *this);
}

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

std::ostream& print(std::ostream &os, const Sales_data &item) {
	os << item.isbn() << " " << item.units_sold << " "
		<< item.revenue << " " << item.avg_price();
	return os;
}

std::istream& read(std::istream &is, Sales_data &item) {
	double price = 0;
	is >> item.bookNo >> item.units_sold >> price;
	item.revenue = item.units_sold*price;
	return is;
}

int main()
{
	Sales_data total(std::cin);
	if (std::cin) {
		Sales_data trans(std::cin);
		while (std::cin) {
			if (total.isbn() == trans.isbn()) {
				total.combine(trans);
			} else {
				print(std::cout, total) << std::endl;
				total = trans;
			}
			read(std::cin,trans);
		}
		print(std::cout, total) << std::endl;
	} else {
		std::cerr << "No data?!" << std::endl;
	}

	return 0;
}
```
## 练习 7.22:
### 修改你的Person类使其隐藏实现的细节。
答：
```
struct Person {
	Person() = default;
	Person(const std::string &s_1, const std::string &s_2) :name(s_1), address(s_2) {}
	Person(std::istream &is) { read(is); }

	std::string getName() const { return name; }
	std::string getAddress() const { return address; }
	std::ostream& print(std::ostream &os) const {
		os << getName() << " " << getAddress();
		return os;
	}

	std::istream& read(std::istream &is) {
		is >> name >> address;
		return is;
	}
private:
	std::string name;
	std::string address;
};
```
## 练习 7.27:
### 给你自己的Screen类添加move、 set 和 display 函数，通过执行下面的代码检验你的类是否正确。
```
	Screen myScreen(5, 5, 'X');
	myScreen.move(4, 0).set('#').display(std::cout);
	std::cout << "\n";
	myScreen.display(std::cout);
	std::cout << "\n";
```
答：
```
#include <iostream>
#include <string>

class Screen {
public:
	typedef std::string::size_type pos;

	Screen() = default;
	Screen(pos ht, pos wd) :height(ht), width(wd), contents(ht * wd, ' ') {}
	Screen(pos ht, pos wd, char c) :height(ht), width(wd), contents(ht * wd, c) {}

	char get()const { return contents[cursor]; }
	char get(pos row, pos column) const { return contents[row * width + column]; }
	Screen& move(pos row, pos column);
	Screen& set(char c);
	Screen& set(pos row, pos column, char c);
	Screen& display(std::ostream &os);
	const Screen& display(std::ostream &os) const ;

private:
	void doDisplay(std::ostream &os) const { os << contents; }

	pos cursor = 0, height = 0, width = 0;
	std::string contents;
};
inline Screen& Screen::move(pos row, pos column) {
	cursor = row * width + column;
	return *this;
}

inline Screen& Screen::set(char c) {
	contents[cursor] = c;
	return *this;
}

inline Screen& Screen::set(pos row, pos column, char c) {
	contents[row * width + column] = c;
	return *this;
}

inline Screen& Screen::display(std::ostream &os) {
	doDisplay(os);
	return *this;
}

inline const Screen& Screen::display(std::ostream &os) const {
	doDisplay(os);
	return *this;
}

int main()
{
	Screen myScreen(5, 5, 'X');
	myScreen.move(4, 0).set('#').display(std::cout);
	std::cout << "\n";
	myScreen.display(std::cout);
	std::cout << "\n";

	return 0;
}
```
## 练习 7.28:
### 如果move, set和display函数的返回类型不是Screen&而是Screen,则在上一个练习中将会发生什么情况？
答：
* 将会创建一个临时对象，并在该临时对象上进行操作，myScreen本身不会受到任何影响。这将导致代码的行为和预期的不同。
* 此外，由于每次调用move，set或display都会创建一个新的Screen对象，这会增加额外的开销，导致程序运行效率下降。
## 练习 7.29:
### 修改你的 Screen 类，令move、 set 和 display 函数返回 Screen 并检查程序的运行结果，在上一个练习中你的推测正确吗？
答：
* 正确
## 练习 7.30:
### 通过this指针使用成员的做法虽然合法,但是有点多余。讨论显式地使用指针访问成员的优缺点。
答：
* 优点:
* 代码清晰：使用this指针可以明确表明正在访问的是类的成员，而不是某个局部变量或全局变量。这使得代码更加清晰，易于理解。
* 消除命名冲突：当局部变量或函数参数与类的成员同名时，使用this指针可以消除命名冲突。
* 缺点:
* 代码冗余：在大多数情况下，显式地使用this指针是不必要的，因为编译器会自动插入this指针。过度使用this指针可能会使代码看起来冗余。
* 可读性降低：虽然使用this指针可以增强代码的清晰性，但是过度使用可能会使代码变得难以阅读。每次看到this指针时，都需要思考一下正在访问的是什么，这可能会打断思路，使代码更难理解。
## 练习 7.31:
### 定义一对类X和Y,其中X包含一个指向Y的指针,而Y包含一个类型为X的对象。
答：
```
class Y;

class X {
	Y*p = nullptr;
};

class Y {
	X x;
};
```
## 练习 7.32:
### 定义你自己的Screen和 Window_mgr,其中 clear 是Window_mgr的成员，是 Screen 的友元。
答：
```
class Screen;

class Window_mgr {
public:
	using ScreenIndex = std::vector<Screen>::size_type;

	Window_mgr();	//initialize screens

	void clear(ScreenIndex i);
private:
	std::vector<Screen> screens;
};

class Screen {
public:
	friend void Window_mgr::clear(Window_mgr::ScreenIndex);

	using pos = std::string::size_type;

	Screen() = default;
	Screen(pos ht, pos wd) :height(ht), width(wd), contents(ht * wd, ' ') {}
	Screen(pos ht, pos wd, char c) :height(ht), width(wd), contents(ht * wd, c) {}

	char get()const { return contents[cursor]; }
	char get(pos row, pos column) const { return contents[row * width + column]; }
	Screen& move(pos row, pos column);
	Screen& set(char c);
	Screen& set(pos row, pos column, char c);
	Screen& display(std::ostream &os);
	const Screen& display(std::ostream &os) const ;

private:
	void doDisplay(std::ostream &os) const { os << contents; }

	pos cursor = 0, height = 0, width = 0;
	std::string contents;
};

Window_mgr::Window_mgr() :screens{ (24,80,' ') } {}

void Window_mgr::clear(ScreenIndex i) {
	Screen &s = screens[i];
	s.contents = std::string(s.height*s.width, ' ');
}

inline Screen& Screen::move(pos row, pos column) {
	cursor = row * width + column;
	return *this;
}

inline Screen& Screen::set(char c) {
	contents[cursor] = c;
	return *this;
}

inline Screen& Screen::set(pos row, pos column, char c) {
	contents[row * width + column] = c;
	return *this;
}

inline Screen& Screen::display(std::ostream &os) {
	doDisplay(os);
	return *this;
}

inline const Screen& Screen::display(std::ostream &os) const {
	doDisplay(os);
	return *this;
}
```
## 练习 7.33：
### 如果我们给 Screen 添加一个如下所示的 size 成员将发生什么情况？如果出现了问题，请尝试修改它。
```
pos Screen::size() const {
	return height * width;
}
```
答：
```
//需要明确指定pos是在Screen类中定义的
Screen::pos Screen::size() const {
	return height * width;
}
```
## 练习 7.34：
### 如果我们把第 256 页 Screen 类的 pos 的 typedef 放在类的最后一行会发生什么情况？
答：
* 会导致编译错误。在 typedef 声明之前的成员函数和成员变量将无法识别 pos。
## 练习 7.35：
### 解释下面代码的含义，说明其中的 Type 和 initVal 分别使用了哪个定义。如果代码存在错误，尝试修改它。
```
typedef string Type;
Type initVal();
class Exercise {
public:
	typedef double Type;
	Type setVal(Type);
	Type initVal();
private:
	int val;
};
Type Exercise::setVal(Type parm) {
	val = parm + initVal();
	return val;
}
```
答：
* 类定义内部使用的是double的别名Type，类定义外部使用的是string的别名Type，setVal的定义在类外部，返回类型将使用string的别名Type。例外的是Exercise::指定了剩余的setVal定义将处于Exercise类的作用域之内，所以形参parm将使用类定义内部的double的别名Type。
```
//fixed
typedef string TypeStr;
TypeStr initVal();
class Exercise {
public:
	typedef double TypeDouble;
	TypeDouble setVal(TypeDouble);
	TypeDouble initVal();
private:
	TypeDouble val;
};
Exercise::TypeDouble Exercise::setVal(TypeDouble parm) {
	val = parm + initVal();
	return val;
}
```
## 练习 7.36:
### 下面的初始值是错误的,请找出问题所在并尝试修改它。
```
struct X {
	X(int i, int j) :base(i), rem(base % j) {}
	int rem, base;
};
```
答：
```
struct X {
	X(int i, int j) :base(i), rem(i % j) {}
	int rem, base;
};
```
## 练习 7.37:
### 使用本节提供的Sales_data类，确定初始化下面的变量时分别使用了哪个构造函数,然后罗列出每个对象所有数据成员的值。
```
Sales_data first_item(cin);
int main() {
	Sales_data next;
	Sales_data last("9-999-99999-9");
}
```
答：
```
//使用Sales_data(std::istream &is)，各数据成员的值根据输入内容改变
Sales_data first_item(cin);	
int main() {
	//使用Sales_data(const std::string &s = "")，各数据成员的值:  bookNo:""  units_sold:0  revenue:0.0
	Sales_data next;
	//使用Sales_data(const std::string &s = "")，各数据成员的值： bookNo:"9-999-99999-9"  units_sold:0  revenue:0.0
	Sales_data last("9-999-99999-9");	
}
```
## 练习 7.38：
### 有些情况下我们希望提供 cin 作为接受 istream&参数的构造函数的默认实参，请声明这样的构造函数。
答：
```
Sales_data(std::istream &is=std::cin);
```
## 练习 7.39：
### 如果接受 string 的构造函数和接受 istream&的构造函数都使用默认实参,这种行为合法吗?如果不,为什么?
答：
* 不合法。将产生二义性调用错误，对重载函数的调用不明确。
## 练习 7.40:
### 从下面的抽象概念中选择一个(或者你自己指定一个),思考这样的类需要哪些数据成员,提供一组合理的构造函数并阐明这样做的原因。
```
(a)book		(b)Date		(c)Employee
(d)Vehicle	(e)Object	(f)Tree
```
答：
```
class Object {
public:
    // 有时需要能够创建无属性的对象，因此提供一个默认构造函数是合理的
    Object() : id(counter++), name("Unnamed") {}
    
    // 也可能想要在创建对象时就给予它一个名字，所以提供一个接受名字的构造函数也是合理的
    Object(const std::string& name) : id(counter++), name(name) {}

    // 获取对象的ID和名字的函数
    int getId() const { return id; }
    const std::string& getName() const { return name; }

    // 修改对象名字的函数
    void setName(const std::string& newName) { name = newName; }

private:
    static int counter;  // 静态成员，用于生成唯一的ID
    int id;             // 每个对象的唯一ID
    std::string name;   // 对象的名字
};

// 初始化静态成员
int Object::counter = 0;
```
## 练习7.41:
### 使用委托构造函数重新编写你的 Sales_data类，给每个构造函数体添加一条语句，令其一旦执行就打印一条信息。用各种可能的方式分别创建Sales_data对象，认真研究每次输出的信息直到你确实理解了委托构造函数的执行顺序。
答：
```
//先为委托构造函数的参数求值，再由被委托的构造函数来设置对象的初始状态，随后被委托构造函数的函数体先执行，最后执行委托构造函数的函数体。
Sales_data() :Sales_data("", 0, 0.0) { std::cout << "In defautlt constructor." << std::endl; }
Sales_data(const std::string &s) :Sales_data(s, 0, 0.0) { std::cout << "In (const string&) constructor." << std::endl; }
Sales_data(const std::string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {
	std::cout << "In (const string&,unsigned,double) constructor." << std::endl;
}
```
## 练习 7.42：
### 对于你在练习 7.40（参见 7.5.1 节，第 261 页）中编写的类，确定哪些构造函数可以使用委托。如果可以的话，编写委托构造函数。如果不可以，从抽象概念列表中重新选择一个你认为可以使用委托构造函数的,为挑选出的这个概念编写类定义。
答：
```
class Object {
public:
	// 默认构造函数，所有其他构造函数都会委托给这个构造函数,以合理的默认值初始化数据成员
	Object() : id(counter++), name("Unnamed") {}

	// 接受名字的构造函数，通过委托默认构造函数实现共享的初始化代码
	Object(const std::string& newName) : Object() { name = newName; }

	// 获取对象的ID和名字的函数
	int getId() const { return id; }
	const std::string& getName() const { return name; }

	// 修改对象名字的函数
	void setName(const std::string& newName) { name = newName; }

private:
	static int counter;  // 静态成员，用于生成唯一的ID
	int id;             // 每个对象的唯一ID
	std::string name;   // 对象的名字
};

// 初始化静态成员
int Object::counter = 0;
```
## 练习 7.43:
### 假定有一个名为 NoDefault 的类，它有一个接受 int的构造函数，但是没有默认构造函数。定义类c, c有一个NoDefault类型的成员,定义c的默认构造函数。
答：
```
struct NoDefult {
	NoDefult(int i){}
};
struct C {
	C():c_member(0) {}
private:
	NoDefult c_member;
};
```
## 练习 7.44:
### 下面这条声明合法吗?如果不,为什么?
```
vector<NoDefult> vec(10);
```
答：
* 不合法。vec将声明10个值初始化的NoDefult对象，而NoDefult没有默认构造函数，会导致值初始化操作产生编译错误。
## 练习 7.45：
### 如果在上一个练习中定义的vector的元素类型是 c，则声明合法吗？为什么？
答：
* 合法。C有默认构造函数，并且在初始值列表中显式地初始化了NoDefult类数据成员。
## 练习 7.46：
### 下面哪些论断是不正确的？为什么？
```
(a)一个类必须至少提供一个构造函数。
(b)默认构造函数是参数列表为空的构造函数。
(c)如果对于类来说不存在有意义的默认值,则类不应该提供默认构造函数。
(d)如果类没有定义默认构造函数，则编译器将为其生成一个并把每个数据成员初始化成相应类型的默认值。
```
答：
* (a)不正确。一个类不一定需要程序员显式地提供一个构造函数。如果程序员没有为类定义任何构造函数，编译器会自动为其生成一个默认构造函数。
* (b)不完全正确。默认构造函数是参数列表为空，或者所有参数都有默认值的构造函数。
* (c)不完全正确。即使对于类来说不存在有意义的默认值，类也可以提供一个默认构造函数。默认构造函数的任务是默认初始化或值初始化类的成员，没有“有意义的默认值”并不意味着不能有默认构造函数。
* (d)不正确。如果类没有定义任何构造函数，编译器才会自动为其生成一个默认构造函数。
## 练习 7.47：
### 说明接受一个string 参数的 sales_data 构造函数是否应该是 explicit的，并解释这样做的优缺点。
答：
* 是否将Sales_data的单参数构造函数设置为explicit，这主要取决于对该构造函数的预期使用方式。
* 优点：避免由于自动类型转换导致的代码混淆和错误。
* 缺点：有的隐式类型转换是自然和合理的，使用explicit会阻碍这种便捷性。
## 练习 7.48：
### 假定 Sales_data 的构造函数不是 explicit 的，则下述定义将执行什么样的操作？
```
	string null_isbn("9-999-99999-9");
	Sales_data item1(null_isbn);
	Sales_data item2("9-999-99999-9");
```
### 如果Sales data的构造函数是explicit的,又会发生什么呢?
答：
* item1和item2都是通过string对象进行直接初始化，直接初始化不受构造函数的explicit关键字影响。
## 练习 7.49:
### 对于combine函数的三种不同声明,当我们调用i.combine(s)时分别发生什么情况？其中 i 是一个 Sales_data，而 s 是一个string 对象。
```
	(a)Sales_data &combine(Sales_data);
	(b)Sales_data &combine(Sales_data&);
	(c)Sales_data &combine(const Sales_data&) const;
```
答：
* (a) 会使用Sales_data(const std::string &s)构造函数将s转换为Sales_data对象。这是一个隐式的类型转换。
* (b) 会产生编译错误，从string到Sales_data的隐式转换，生成的将是一个临时的Sales_data对象。这是一个右值，所以不能被非const的Sales_data&引用绑定。
* (c) 与(a)相同，会将s通过构造函数转换为Sales_data对象。而函数的参数是一个const的Sales_data引用，这个调用是合法的。
## 练习 7.50：
### 确定在你的 Person 类中是否有一些构造函数应该是 explicit 的。
答：
```
	// std::istream对象通常不会通过隐式转换从其他类型得到。该单参数构造函数不需要使用explicit
	Person(std::istream &is) {
		read(is);
	}
```
## 练习 7.51:
### vector将其单参数的构造函数定义成explicit的,而string则不是,你觉得原因何在？
答：
* 如果不将vector的单参数构造函数定义成explicit的，可能会出现一些令人困惑的拷贝初始化或隐式转换。譬如：vector\<int>v = 10;（一个包含10个默认初始化int的vector），而string的情况则很自然合理，string s = "Hello world!"
## 练习 7.52：
### 使用 2.6.1 节（第 64 页）的 Sales_data 类，解释下面的初始化过程。如果存在问题，尝试修改它。
```
Sales_data item = { "978-0590353403",25,15.99 };
```
答：
```
//原始版本Sales_data类有类内初始值，并不是一个聚合类
//修改如下
struct Sales_data {
	std::string bookNo;
	unsigned units_sold;
	double rebenue;
};
```
## 练习7.53:
### 定义你自己的Debug。
答：
```
class Debug {
public:
	constexpr Debug(bool b = true) : hw(b), io(b), other(b) {
	}
	constexpr Debug(bool h, bool i, bool o) : hw(h), io(i), other(o) {
	}
	constexpr bool any() { return hw || io || other; } 
	void set_io(bool b) { io = b; } 
	void set_hw(bool b) { hw = b; } 
	void set_other(bool b) { hw = b; }
private:
	bool hw; 
	bool io; 
	bool other; 
};
```
## 练习 7.54:
### Debug 中以 set_开头的成员应该被声明成 constexpr 吗？如果不，为什么？
答：
* 不应该。void 返回类型对于constexpr函数是禁止的
## 练习 7.55:
### 7.5.5节(第266页)的Data类是字面值常量类吗?请解释原因。
答：
* std::string不是字面值类，所以包含 std::string 作为数据成员的Data类也不是字面值类。
