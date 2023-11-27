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
(d) -> ：箭头运算符必须是类的成员。
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
## 练习 14.27：
### 为你的 StrBlobPtr 类添加递增和递减运算符。
答：
```
StrBlobPtr& StrBlobPtr::operator++(){
	// 如果 curr 已经指向了容器的尾后位置，则无法递增它
	check(curr, "increment past end of StrBlobPtr");
	++curr; // 将curr在当前状态下向前移动一个元素
	return *this;
}
StrBlobPtr& StrBlobPtr::operator--(){
	// 如果 curr 是 0，则继续递减它将产生一个无效下标
	--curr; // 将curr在当前状态下向后移动一个元素
	check(curr, "decrement past begin of StrBlobPtr");
	return *this;
}

StrBlobPtr StrBlobPtr::operator++(int) {
	// 此处无须检查有效性,调用前置递增运算时才需要检查
	StrBlobPtr ret = *this; // 记录当前的值
	++*this; // 向前移动一个元素，前置++需要检查递增的有效性
	return ret; // 返回之前记录的状态
}
StrBlobPtr StrBlobPtr::operator--(int) {
	// 此处无须检查有效性,调用前置递减运算时才需要检查
	StrBlobPtr ret = *this; // 记录当前的值
	--*this; // 向前移动一个元素，前置--需要检查递增的有效性
	return ret; // 返回之前记录的状态
}
```
## 练习 14.28:
### 为你的StrBlobPtr类添加加法和减法运算符,使其可以实现指针的算术运算(参见3.5.3节,第106页)。
答：
```
StrBlobPtr& StrBlobPtr::operator+=(size_t n){
	curr += n;
	check(curr, "increment past end of StrBlobPtr");
	return *this;
}

StrBlobPtr& StrBlobPtr::operator-=(size_t n){
	curr -= n;
	check(curr, "decrement past begin of StrBlobPtr");
	return *this;
}

StrBlobPtr StrBlobPtr::operator+(size_t n) const{
	StrBlobPtr ret = *this;
	ret += n;
	return ret;
}

StrBlobPtr StrBlobPtr::operator-(size_t n) const{
	StrBlobPtr ret = *this;
	ret -= n;
	return ret;
}

ptrdiff_t operator-(const StrBlobPtr&lhs,const StrBlobPtr& rhs) {
	auto lshSp = lhs.wptr.lock();
	auto rhsSp = rhs.wptr.lock();
	if (lshSp != rhsSp) {
		throw std::runtime_error("Different base addresses");
	}
	return lhs.curr - rhs.curr;
}
```
## 练习 14.29:
### 为什么不定义const版本的递增和递减运算符?
答：
* 因为递增和递减运算符需要修改对象本身。
## 练习 14.30:
### 为你的 StrBlobPtr类和在 12.1.6 节练习12.22 (第423 页)中定义的ConstStrBlobPtr类分别添加解引用运算符和箭头运算符。注意：因为ConstStrBlobPtr 的数据成员指向 const vector,所以ConstStrBlobPtr 中的运算符必须返回常量引用。
答：
```
// StrBlobPtr
std::string& StrBlobPtr::operator*() const {
	auto p = check(curr, "dereference past end");
	return (*p)[curr];		// （*p)是对象所指的 vector
} 
std::string* StrBlobPtr::operator->() const{ // 将实际工作委托给解引用运算符
	return & this->operator*();
}
// ConstStrBlobPtr
const std::string& ConstStrBlobPtr::operator*() const {
	auto p = check(curr, "dereference past end");
	return (*p)[curr];
} 
const std::string* ConstStrBlobPtr::operator->() const{
	return & this->operator*();
}
```
## 练习 14.31：
### 我们的 StrBlobPtr 类没有定义拷贝构造函数、赋值运算符及析构函数，为什么？
答：
* 使用合成版本的拷贝控制成员不会产生问题。
## 练习 14.32：
### 定义一个类令其含有指向StrBlobPtr对象的指针，为这个类定义重载的箭头运算符。
答：
```
class Wrapper {
public:
	Wrapper() = default;
	Wrapper(StrBlobPtr* sbp) :pSBP(sbp) {}
	//返回StrBlobPtr对象的operator->()会自动调用StrBlobPtr的operator->()
	StrBlobPtr operator->() const {
		return *pSBP;
	}
private:
	StrBlobPtr* pSBP=nullptr;
};
```
## 练习 14.33：
### 一个重载的函数调用运算符应该接受几个运算对象？
答： 
* 一个重载的函数调用运算符可以接受任意数量的运算对象（包括零个）。具体数量取决于你如何定义该运算符。
## 练习 14.34：
### 定义一个函数对象类，令其执行 if-then-else 的操作：该类的调用运算符接受三个形参,它首先检查第一个形参;如果成功返回第二个形参的值;如果不成功返回第三个形参的值。
答：
```
class IfThenElse {
public:
	int operator()(const bool condition, const int ifTrue, const int ifFalse) const {
		if (condition) {
			return ifTrue;
		} else {
			return ifFalse;
		}
	}
};
```
## 练习 14.35：
### 编写一个类似于 PrintString 的类，令其从 istream 中读取一行输入， 然后返回一个表示我们所读内容的 string。如果读取失败，返回空 string。
答：
```
#include <iostream>
#include <string>

using namespace std;

class InputString {
public:
	InputString(istream &input = cin) : is(input) { }
	string operator()() const {
		string str;
		getline(is, str);
		if (is) {
			return str;
		} else {
			return "";
		}
	}
private:
	istream &is;
};
```
## 练习 14.36:
### 使用前一个练习定义的类读取标准输入,将每一行保存为vector的一个元素。
答：
```
#include <iostream>
#include <string>
#include <vector>

using namespace std;

class InputString {
public:
	InputString(istream &input = cin) : is(input) { }
	vector<string> operator()() const {
		vector<string> vec;
		string str;
		while (getline(is, str)) {
			vec.push_back(str);
		}
		return vec;
	}
private:
	istream &is;
};
```
## 练习 14.37：
### 编写一个类令其检查两个值是否相等。使用该对象及标准库算法编写程序，令其替换某个序列中具有给定值的所有实例。
答：
```
#include <algorithm>
#include <iostream>
#include <vector>

class Equal {
public:
	Equal(const int& v) : value(v) {}

	bool operator()(const int& elem) const {
		return elem == value;
	}

private:
	int value;
};

int main() {
	std::vector<int> vec = { 1, 2, 3, 4, 3, 5, 3 };
	int valueToCheck = 3;
	int valueToReplaceWith = 9;

	std::replace_if(vec.begin(), vec.end(), Equal(valueToCheck), valueToReplaceWith);

	for (int i : vec) {
		std::cout << i << " ";
	}

	return 0;
}
```
## 练习 14.38:
### 编写一个类令其检查某个给定的 string对象的长度是否与一个阈值相等。 使用该对象编写程序,统计并报告在输入的文件中长度为1的单词有多少个、长度为2的单词有多少个、……、长度为10的单词又有多少个。
答：
```
#include <iostream>
#include <fstream>
#include <string>
#include <vector>

class IsLength {
public:
	IsLength(int len) : length(len) {}

	bool operator()(const std::string& str) const {
		return str.length() == length;
	}

private:
	int length;
};

int main() {
	std::ifstream file("example.txt");
	if (!file) {
		std::cerr << "Cannot open file." << std::endl;
		return 1;
	}

	std::vector<int> counts(10, 0);
	std::string word;

	while (file >> word) {
		for (int i = 1; i <= 10; ++i) {
			if (IsLength(i)(word)) {
				counts[i - 1]++;
				break;
			}
		}
	}

	for (int i = 1; i <= 10; ++i) {
		std::cout << "Words of length " << i << ": " << counts[i - 1] << std::endl;
	}

	return 0;
}
```
## 练习 14.39：
### 修改上一题的程序令其报告长度在 1 至9之间的单词有多少个、长度在 10以上的单词又有多少个。
答：
```
#include <iostream>
#include <fstream>
#include <string>
#include <vector>

class IsLength {
public:
	IsLength(int len) : length(len) {}

	bool operator()(const std::string& str) const {
		return length >= 10 ? str.length() >= length : str.length() == length;
	}

private:
	int length;
};

int main() {
	std::ifstream file("example.txt");
	if (!file) {
		std::cerr << "Cannot open file." << std::endl;
		return 1;
	}

	std::vector<int> counts(9, 0); // 用于存储长度1到9的单词数量
	int count10plus = 0;           // 用于存储长度10以上的单词数量
	std::string word;

	while (file >> word) {
		bool counted = false;
		for (int i = 1; i <= 9; ++i) {
			if (IsLength(i)(word)) {
				counts[i - 1]++;
				counted = true;
				break;
			}
		}
		if (!counted && IsLength(10)(word)) {
			count10plus++;
		}
	}

	for (int i = 1; i <= 9; ++i) {
		std::cout << "Words of length " << i << ": " << counts[i - 1] << std::endl;
	}
	std::cout << "Words of length 10 and above: " << count10plus << std::endl;

	return 0;
}
```
## 练习 14.40：
### 重新编写 10.3.2 节（第 349 页）的 biggies 函数，使用函数对象类替换其中的 lambda 表达式。
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

class IsShorter {
public:
	bool operator()(const string& a, const string& b) const {
		return a.size() < b.size();
	}
};

class GE {
private:
	vector<string>::size_type sz;
public:
	GE(vector<string>::size_type sz) : sz(sz) {}
	bool operator()(const string& a) const {
		return a.size() >= sz;
	}
};

class Print{
public:
	void operator()(const string &s) { cout << s << " "; }
};

void elimDups(vector<string> &words){
	sort(words.begin(), words.end());
	auto end_unique = unique(words.begin(), words.end());
	words.erase(end_unique, words.end());
}

void biggies(vector<string> &words,vector<string>::size_type sz){
	elimDups(words); 
	stable_sort(words.begin(), words.end(),IsShorter());
	auto wc = find_if(words.begin(), words.end(), GE(sz));
	auto count = words.end() - wc;
	cout << count << " " << (count>1? "words": "word") << " of length " << sz << " or longer" << endl;
	for_each(wc, words.end(), Print());
	cout << endl;
}
int main() {
	vector<string> words = { "abc","abcd","abcde" };
	biggies(words, 4);

	return 0;
}
```
## 练习 14.41:
### 你认为C++11新标准为什么要增加lambda?对于你自己来说,什么情况下会使用lambda,什么情况下会使用类？
答：
* C++11 新标准增加 Lambda 主要是为了提供一种更方便、更灵活的方式来定义和使用匿名函数对象。
* 使用 Lambda 的情况：
* 当需要一个简单的函数对象，特别是只用一次的情况。
* 在使用标准库算法，需要传递自定义的比较函数、条件函数等，这使得代码更加紧凑和易于阅读。
* 当需要一个闭包来捕获和使用当前作用域中的变量。
* 使用类代替 Lambda 的情况：
* 当需要重用该函数对象，或者它足够复杂，使得将其作为一个完整的类更有意义。
* 当函数对象需要维护自己的状态或需要多于一个调用运算符时。
## 练习 14.42:
### 使用标准库函数对象及适配器定义一条表达式,令其
```
(a)统计大于1024的值有多少个。
(b)找到第一个不等于pooh的字符串。
(c)将所有的值乘以2。
```
答：
```
(a)
auto count = std::count_if(vec.begin(), vec.end(), std::bind(std::greater<int>(), _1, 1024));
(b)
auto it = std::find_if(vec.begin(), vec.end(), std::bind(std::not_equal_to<std::string>(), _1, "pooh"));
(c)
std::transform(vec.begin(), vec.end(), vec.begin(), std::bind(std::multiplies<int>(), _1, 2));
```
## 练习 14.43：
### 使用标准库函数对象判断一个给定的 int 值是否能被 int 容器中的所有元素整除。
答：
```
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

bool isDivisibleByAll(int number, const std::vector<int>& container) {	
	return std::all_of(container.begin(), container.end(), [number](int divisor) {
		std::modulus<int> mod;
		return divisor != 0 && mod(number, divisor) == 0;
	});
}

int main() {
	std::vector<int> container = { 2, 3, 4 }; // 示例容器
	int number = 12; // 需要检查的数字

	if (isDivisibleByAll(number, container)) {
		std::cout << number << " can be divided by all elements in the container." << std::endl;
	} else {
		std::cout << number << " cannot be divided by all elements in the container." << std::endl;
	}

	return 0;
}
```
## 练习 14.44：
### 编写一个简单的桌面计算器使其能处理二元运算。
答：
```
#include <iostream>
#include <string>
#include <functional>
#include <map>

using namespace std;

// 普通函数
int add(int i, int j) { return i + j; }
// lambda，其产生一个未命名的函数对象类
auto mod = [](int i, int j) { return i % j; };
// 函数对象类
struct divide {
	int operator()(int denominator, int divisor) {return denominator / divisor; }
};

int main() {
	map<string, function<int(int, int)>> binops = {
		{ "+", add }, // 函数指针
		{ "-", minus<int>() }, // 标准库函数对象
		{ "/", divide() }, // 用户定义的函数对象
		{ "*", [](int i, int j) { return i * j; } }, // 未命名的 lambda
		{ "%", mod } }; // 命名了的lambda 对象

	// 与用户交互部分
	string op;
	int lhs, rhs;
	while (true) {
		cout << "Enter an expression (for example, 3 + 4) and enter 'q' to quit: ";
		cin >> lhs;
		if (cin.fail()) {
			break; // 非数字输入，退出循环
		}
		cin >> op >> rhs;
		if (binops.find(op) != binops.end()) {
			cout << "Result: " << binops[op](lhs, rhs) << endl;
		} else {
			cout << "Unknown operator: " << op << endl;
		}
	}

	return 0;
}
```
## 练习 14.45:
### 编写类型转换运算符将一个Sales_data对象分别转换成 string和 double,你认为这些运算符的返回值应该是什么？
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
	operator std::string() const { return bookNo; }	// string 类型转换
	explicit operator double() const { return revenue; }	// double 类型转换

	std::string isbn() const { return bookNo; }

private:
	inline double avg_price() const {
		return units_sold ? revenue / units_sold : 0;
	}

	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};
```
## 练习 14.46：
### 你认为应该为 Sales_data 类定义上面两种类型转换运算符吗？应该把它们声明成 explicit 的吗？为什么？
答：
* 转换成 string
* 可能的理由：Sales_data 对象可能被转换为 string 来表示其 bookNo，也就是书籍的 ISBN 号。如果 bookNo 是 Sales_data 的主要标识或最重要的属性，这样的转换是有意义的。
* 是否应该是 explicit：如果将 Sales_data 隐式转换为 string 可能导致代码的含义不明确或易于误解，那么应该将这个运算符声明为 explicit。这样，只有在显式需要 string 表示时，才能进行转换。
* 转换成 double
* 可能的理由：将 Sales_data 转换为 double 可能意味着返回其 revenue 或 avg_price()。这在某些财务或统计上下文中可能是有意义的。
* 是否应该是 explicit：这种转换更可能需要被声明为 explicit，因为将一个复杂对象隐式转换为 double 可能会导致混淆。例如，不清楚转换后的 double 是指 revenue、avg_price() 还是其他内容。
## 练习 14.47：
### 说明下面这两个类型转换运算符的区别。
```
struct Integral {
	operator const int();
	operator int() const;
};
```
答：
```
struct Integral {
	operator const int();	// 转换为 const int
	operator int() const;	// 转换为 int 的const类型转换运算符
};
```
## 练习 14.48:
### 你在 7.5.1 节的练习7.40 (第261页)中曾经选择并编写了一个类，你认为它应该含有向bool的类型转换运算符吗?如果是,解释原因并说明该运算符是否应该是explicit的;如果不是,也请解释原因。
答：
* 基于 Object 类的当前定义，它似乎并不直接与“有效性”或类似的概念相关联。除非有额外的上下文说明何时一个 Object 应该被视为“无效”或“不成立”，否则添加向 bool 的转换可能不是必要的。
## 练习 14.49:
### 为上一题提到的类定义一个转换目标是bool的类型转换运算符,先不用在意这么做是否应该。
答：
```
class Object {
public:
	// 有时需要能够创建无属性的对象，因此提供一个默认构造函数是合理的
	Object() : id(counter++), name("Unnamed") {}
	// 也可能想要在创建对象时就给予它一个名字，所以提供一个接受名字的构造函数也是合理的
	Object(const std::string& name) : id(counter++), name(name) {}
	// bool类型转换运算符
	explicit operator bool() const { return name == "Unnamed" ? false : true; }
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
## 练习 14.50：
### 在初始化 ex1 和 ex2 的过程中，可能用到哪些类类型的转换序列呢?说明初始化是否正确并解释原因。
```
struct LongDouble {
	LongDouble(double = 0.0);
	operator double();
	operator float();
};
LongDouble ldObj;
int ex1 = ldObj;
float ex2 = ldObj;
```
答：
* ex2 会调用 operator float() ，ex1 会产生二义性错误。
## 练习 14.51：
### 在调用 calc 的过程中，可能用到哪些类型转换序列呢？说明最佳可行函数是如何被选出来的。
```
void calc(int);
void calc(LongDouble);
double dval;
calc(dval); // 哪个 calc?
```
答：
* 本人的编译器会调用void calc(int) 。
* 为了确定最佳匹配,编译器将实参类型到形参类型的转换划分成几个等级,具体排序如下所示：
* 1.精确匹配，包括以下情况： 
* 实参类型和形参类型相同。
* 实参从数组类型或函数类型转换成对应的指针类型。
* 向实参添加顶层 const 或者从实参中删除顶层 const。
* 2.通过 const 转换实现的匹配。
* 3.通过类型提升实现的匹配。
* 4.通过算术类型转换或指针转换实现的匹配。
* 5.通过类类型转换实现的匹配。
