## 练习 14.1:
### 在什么情况下重载的运算符与内置运算符有所区别?在什么情况下重载的运算符又与内置运算符一样？
答：
* 重载运算符与内置运算符的不同之处：
* 定义方式：内置运算符是语言内置的，它们的操作和语义都是预先定义好的，而重载运算符则是用户自定义的，可以为其提供特定于类型的实现。
* 适用对象：内置运算符适用于内置数据类型，例如 int、double 等。重载的运算符通常用于自定义的数据类型（类或结构体），但也可以对某些内置类型进行重载（前提是至少有一个参数是用户定义的类型）。
* 使用方式：可以显式地通过函数名直接调用重载的运算符函数。
* 求值顺序和短路求值：逻辑与&&、逻辑或||、条件:?和逗号,的重载版本不会保留求值顺序和短路求值的特性。
* 重载运算符与内置运算符的相似之处：
* 优先级与结合性：重载的运算符的优先级和结合性与其对应的内置运算符完全相同。例如，如果为自定义类重载了operator+和operator*，那么*的优先级仍然高于+。
## 练习 14.2:
### 为Sales_data编写重载的输入、输出、加法和复合赋值运算符。
答：
```
#include <iostream>
#include <string>

class Sales_data {
	friend std::istream& operator>>(std::istream&, Sales_data&);   // 输入
	friend std::ostream& operator<<(std::ostream&, const Sales_data&); // 输出
	friend Sales_data operator+(const Sales_data&, const Sales_data&);  // 加法

public:
	Sales_data(const std::string &s, unsigned n, double p) : bookNo(s), units_sold(n), revenue(n*p) { }
	Sales_data() : Sales_data("", 0, 0.0f) { }
	Sales_data(const std::string &s) : Sales_data(s, 0, 0.0f) { }
	Sales_data(std::istream &is) { is >> *this; }

	Sales_data& operator+=(const Sales_data&); // 复合赋值

	std::string isbn() const { return bookNo; }

private:
	inline double avg_price() const {
		return units_sold ? revenue / units_sold : 0;
	}

	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

// 输入
std::istream& operator>>(std::istream& is, Sales_data& item) {
	double price = 0.0;
	is >> item.bookNo >> item.units_sold >> price;
	if (is) {
		item.revenue = item.units_sold * price;
	} else {
		item = Sales_data();
	}
	return is;
}

// 输出
std::ostream& operator<<(std::ostream& os, const Sales_data& item) {
	os << item.isbn() << " " << item.units_sold << " " << item.revenue << " " << item.avg_price();
	return os;
}

// 加法
Sales_data operator+(const Sales_data& lhs, const Sales_data& rhs) {
	Sales_data sum = lhs;
	sum += rhs;
	return sum;
}

// 复合赋值
Sales_data& Sales_data::operator+=(const Sales_data& rhs) {
	units_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}
```
## 练习 14.3:
### string和vector都定义了重载的=以比较各自的对象,假设svec1和svec2是存放string的vector,确定在下面的表达式中分别使用了哪个版本的=?
```
(a) "cobble" == "stone"
(b) svec1[0] == svec2[0]
(c) svec1 == svec2
(d) "svec1[0] == "stone"
```
答：
```
(a) 内置版本
(b) string
(c) vector
(d) string
```
## 练习 14.4：
### 如何确定下列运算符是否应该是类的成员？
```
(a) %
(b) %=
(c) ++
(d) ->
(e) <<
(f) &&
(g) ==
(h) ()
```
答：
```
(a) % ：取模运算符通常是对内建数值类型进行操作，但如果在类中有合适的语义，它可能是一个成员。
(b) %= ：赋值运算符的扩展，通常应该是类的成员。
(c) ++ ：自增运算符需要改变对象的状态，应该是类的成员。
(d) -> ：箭头运算符用于指针和迭代器类似的对象，应该是类的成员。
(e) << ：这是输出流的插入运算符。通常，至少有一个操作数不是类类型（例如std::ostream），所以这应该是非成员函数。
(f) && ：逻辑与运算符。如果在类中有合适的语义，它可以是成员。但通常，它应该是非成员函数。
(g) == ：相等运算符通常应该是非成员函数以支持交换操作数的对称性，即a == b和b == a应该有相同的含义。
(h) () ：函数调用运算符必须是类的成员。
```
## 练习 14.5:
### 在7.5.1节的练习7.40 (第261页)中,编写了下列类中某一个的框架,请问在这个类中应该定义重载的运算符吗?如果是,请写出来。
```
(a) Book
(b) Date
(c) Employee
(d) Vehicle
(e) Object
(f) Tree
```
答：
* 除非有特定的理由，否则可能不需要自定义赋值运算符。
```
#include <iostream>
#include <string>

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

// 输出运算符
std::ostream& operator<<(std::ostream& os, const Object& obj) {
	os << "ID: " << obj.getId() << ", Name: " << obj.getName();
	return os;
}

//相等运算符
bool operator==(const Object& lhs, const Object& rhs) {
	return lhs.getId() == rhs.getId();
}

//不等运算符
bool operator!=(const Object& lhs, const Object& rhs) {
	return !(lhs == rhs);
}
```
## 练习 14.6：
### 为你的 Sales data 类定义输出运算符。
答：
```
// 输出
std::ostream& operator<<(std::ostream& os, const Sales_data& item) {
	os << item.isbn() << " " << item.units_sold << " " << item.revenue << " " << item.avg_price();
	return os;
}
```
## 练习 14.7:
### 你在13.5 节的练习(第 470 页)中曾经编写了一个String类，为它定义一个输出运算符。
答：
```
// 输出运算符
std::ostream& operator<<(std::ostream &os, const String &str){
	os << str.begin();
	return os;
}
```
## 练习 14.8:
### 你在7.5.1节的练习7.40 (第261页)中曾经选择并编写了一个类,为它定义一个输出运算符。
答：
```
// 输出运算符
std::ostream& operator<<(std::ostream& os, const Object& obj) {
	os << "ID: " << obj.getId() << ", Name: " << obj.getName();
	return os;
}
```
## 练习 14.9:
### 为你的Sales_data类定义输入运算符。
答：
```
// 输入
std::istream& operator>>(std::istream& is, Sales_data& item) {
	double price = 0.0;
	is >> item.bookNo >> item.units_sold >> price;
	if (is) {
		item.revenue = item.units_sold * price;
	} else {
		item = Sales_data();
	}
	return is;
}
```
## 练习 14.10：
### 对于 Sales_data 的输入运算符来说如果给定了下面的输入将发生什么情况？
```
(a) 0-201-99999-9 10 24.95
(b) 10 24.95 0-210-99999-9
```
答：
```
(a) 正常执行 。
(b) 将.95读入price ，检测输入流的状态并不能防止这种输入错误。
```
## 练习 14.11：
### 下面的 Sales_data 输入运算符存在错误吗？如果有，请指出来。对于这个输入运算符如果仍然给定上个练习的输入将发生什么情况？
```
istream& operator>>(istream& in, Sales_data& s)
{
 double price; in >> s.bookNo >> s.units_sold >> price;
 s.revenue = s.units_sold * price; return in;
}
```
答：
* 输入检测变得更差了。给这个运算符输入上个练习的数据，所产生的结果是相同的。
## 练习 14.12:
### 你在 7.5.1 节的练习 7.40 (第261页)中曾经选择并编写了一个类，为它定义一个输入运算符并确保该运算符可以处理输入错误。
答：
* 除非有特定的理由，否则可能不需要自定义输入运算符。
## 练习 14.13：
### 你认为 Sales_data 类还应该支持哪些其他算术运算符（参见表 4.1，第124页) ?如果有的话,请给出它们的定义。
答：
* 除非有特定的理由，否则可能不需要自定义算术运算符。
## 练习 14.14:
### 你觉得为什么调用operator+=来定义operator+比其他方法更有效?
答：
* 代码的简洁性：不需要重复相同的逻辑。
* 维护性：如果需要改变加法的行为，只需在一个地方修改。
* 一致性：确保加法和复合加法的行为始终保持一致。
## 练习 14.15：
### 你在 7.5.1 节的练习 7.40 (第261 页)中曾经选择并编写了一个类，你认为.它应该含有其他算术运算符吗?如果是,请实现它们;如果不是,解释原因。
答：
* 除非有特定的理由，否则可能不需要自定义算术运算符。
