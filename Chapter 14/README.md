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
### 你在 7.5.1 节的练习 7.40 (第261 页)中曾经选择并编写了一个类，你认为它应该含有其他算术运算符吗?如果是,请实现它们;如果不是,解释原因。
答：
* 除非有特定的理由，否则可能不需要自定义算术运算符。
## 练习 14.16：
### 为你的 strBlob 类（参见 12.1.1 节，第 405 页)、StrBlobPtr 类（参见12.1.6节,第421页)、StrVec类(参见13.5节,第465页)和String类(参见13.5节,第470页)分别定义相等运算符和不相等运算符。
答：
```
// StrBlob
bool operator==(const StrBlob &lhs, const StrBlob &rhs) {
	return lhs.data == rhs.data;
}
bool operator!=(const StrBlob &lhs, const StrBlob &rhs){
	return !(lhs == rhs);
}
// StrBlobPtr
bool operator==(const StrBlobPtr &lhs, const StrBlobPtr &rhs){
	auto lshSp = lhs.wptr.lock();
	auto rhsSp = rhs.wptr.lock();
	if (lshSp && rhsSp && lshSp == rhsSp) {
		return lhs.curr == rhs.curr;
	}
	return false;
}
bool operator!=(const StrBlobPtr &lhs, const StrBlobPtr &rhs){
	return !(lhs == rhs);
}
// StrVec
bool operator==(const StrVec &lhs, const StrVec &rhs){
	return (lhs.size() == rhs.size() && std::equal(lhs.begin(), lhs.end(), rhs.begin()));
}
bool operator!=(const StrVec &lhs, const StrVec &rhs){
	return !(lhs == rhs);
}
// String
bool operator==(const String &lhs, const String &rhs){
	return (lhs.size() == rhs.size() && std::equal(lhs.begin(), lhs.end(), rhs.begin()));
}

bool operator!=(const String &lhs, const String &rhs){
	return !(lhs == rhs);
}
```
## 练习 14.17：
### 你在 7.5.1 节的练习 7.40（第 261 页）中曾经选择并编写了一个类，你认为它应该含有相等运算符吗？如果是，请实现它；如果不是,解释原因。
答：
```
bool operator==(const Object& lhs, const Object& rhs) {
    return lhs.getId() == rhs.getId();
}
```
## 练习 14.18:
### 为你的StrBlob类、StrBlobPtr类、StrVec类和String类定义关系运算符。
答：
```
// StrBlob
bool operator<(const StrBlob& lhs, const StrBlob& rhs) {
	return std::lexicographical_compare(lhs.data->begin(), lhs.data->end(), rhs.data->begin(), rhs.data->end());
}

bool operator<=(const StrBlob& lhs, const StrBlob& rhs) {
	return !(rhs < lhs);
}

bool operator>(const StrBlob& lhs, const StrBlob& rhs) {
	return rhs < lhs;
}

bool operator>=(const StrBlob& lhs, const StrBlob& rhs) {
	return !(lhs < rhs);
}
// StrBlobPtr
bool operator<(const StrBlobPtr& lhs, const StrBlobPtr& rhs) {
	return lhs.curr < rhs.curr;
}

bool operator<=(const StrBlobPtr& lhs, const StrBlobPtr& rhs) {
	return !(rhs < lhs);
}

bool operator>(const StrBlobPtr& lhs, const StrBlobPtr& rhs) {
	return rhs < lhs;
}

bool operator>=(const StrBlobPtr& lhs, const StrBlobPtr& rhs) {
	return !(lhs < rhs);
}
// StrVec
bool operator<(const StrVec& lhs, const StrVec& rhs) {
	return std::lexicographical_compare(lhs.begin(), lhs.end(), rhs.begin(), rhs.end());
}

bool operator<=(const StrVec& lhs, const StrVec& rhs) {
	return !(rhs < lhs);
}

bool operator>(const StrVec& lhs, const StrVec& rhs) {
	return rhs < lhs;
}

bool operator>=(const StrVec& lhs, const StrVec& rhs) {
	return !(lhs < rhs);
}
// String
bool operator<(const String& lhs, const String& rhs) {
	return std::lexicographical_compare(lhs.begin(), lhs.end(), rhs.begin(), rhs.end());
}

bool operator<=(const String& lhs, const String& rhs) {
	return !(rhs < lhs);
}

bool operator>(const String& lhs, const String& rhs) {
	return rhs < lhs;
}

bool operator>=(const String& lhs, const String& rhs) {
	return !(lhs < rhs);
}
```
### 练习 14.19:
### 你在7.5.1节的练习7.40 (第261页)中曾经选择并编写了一个类,你认为它应该含有关系运算符吗?如果是,请实现它;如果不是,解释原因。
答：
```
bool operator<(const Object& lhs, const Object& rhs) {
    return lhs.getId() < rhs.getId();
}
```
## 练习 14.20:
### 为你的Sales_data类定义加法和复合赋值运算符。
答：
```
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
```
## 练习 14.21：
### 编写 Sales_data 类的+和+=运算符，使得+执行实际的加法操作而+=调用+。相比于14.3 节（第 497 页）和 14.4 节（第 500 页)对这两个运算符的定义，本题的定义有何缺点？试讨论之。
答：
```
// 加法
Sales_data& Sales_data::operator+=(const Sales_data &rhs) {
	*this = *this + rhs;
	return *this;
}
// 复合赋值
Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs) {
	return Sales_data(lhs.isbn(), lhs.units_sold + rhs.units_sold, lhs.avg_price() + rhs.avg_price());
}
```
* 效率问题：在operator+=中，\*this + rhs会创建一个临时Sales_data对象，然后将这个临时对象的内容复制到*this。这意味着在每次调用operator+=时都会有额外的对象创建和销毁，而这些都是不必要的。
## 练习 14.22:
### 定义赋值运算符的一个新版本，使得我们能把一个表示 ISBN 的 string赋给一个 Sales_data 对象。
答：
```
Sales_data& Sales_data::operator=(const std::string &newBookNo){
	bookNo = newBookNo;
	return *this;
}
```
## 练习 14.23:
### 为你的StrVec类定义一个initializer_list赋值运算符。
答：
```
StrVec &StrVec::operator=(initializer_list<string> il){
	// alloc_n_copy 分配内存空间并从给定范围内拷贝元素
	auto data = alloc_n_copy(il.begin(), il.end());
	free(); // 销毁对象中的元素并释放内存空间
	elements = data.first; // 更新数据成员使其指向新空间
	first_free = cap = data.second;
	return *this;
}
```
## 练习 14.24:
### 你在 7.5.1节的练习7.40 (第261页)中曾经选择并编写了一个类，你认为它应该含有拷贝赋值和移动赋值运算符吗?如果是,请实现它们。
答：
```
// 拷贝赋值运算符
Object& Object::operator=(const Object& other) {
	if (this != &other) {  // 避免自赋值
		name = other.name;
		// 不改变id
	}
        return *this;
}
// 移动赋值运算符
Object& Object::operator=(Object&& other) noexcept {
	if (this != &other) {  // 避免自赋值
		name = std::move(other.name);
		// 不改变id
		other.name = "Unnamed";
	}
	return *this;
}
```
## 练习 14.25:
### 上题的这个类还需要定义其他赋值运算符吗?如果是,请实现它们;同时说明运算对象应该是什么类型并解释原因。
答：
```
// 使用string 作为参数的赋值运算符，更改Object的name
Object& Object::operator=(const std::string &newName){
	name = newName;
	return *this;
}
```
## 练习 14.26:
### 为你的StrBlob类、StrBlobPtr类、StrVec类和String类定义下标运算符。
答：
```
// StrBlob
string& StrBlob::operator[](size_t n){
	check(n, "[] out of range");
	return data->at(n);
}

const string& StrBlob::operator[](size_t n) const{
	check(n, "[] out of range");
	return data->at(n);
}
// StrBlobPtr
string& StrBlobPtr::operator[](size_t n){
	auto p = check(n + curr, "[] out of range.");
	return p->at(n + curr);
}

const string& StrBlobPtr::operator[](size_t n) const{
	auto p = check(n + curr, "[] out of range.");
	return p->at(n + curr);
}
// StrVec
std::string& StrVec::operator[](std::size_t n) { return elements[n]; }
const std::string& StrVec::operator[](std::size_t n) const { return elements[n]; }
// String
char& String::operator[](std::size_t n) { return elements[n]; }
const char& String::operator[](std::size_t n) const { return elements[n]; }
```
