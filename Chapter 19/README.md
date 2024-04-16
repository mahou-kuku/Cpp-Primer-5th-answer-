## 练习 19.1： 
### 使用 malloc 编写你自己的 operator new (size_t) 函数，使用 free 编写operator delete (void *)函数。
答：
```
void *operator new(size_t size){ 
	if (void *mem = malloc(size)) {
		return mem;
	} else {
		throw std::bad_alloc();
	}
}

void operator delete(void *mem) noexcept { free(mem); }
```
## 练习 19.2： 
### 默认情况下, allocator类使用operator new获取存储空间,然后使用 operator delete 释放它。利用上一题中的两个函数重新编译并运行你的 StrVec 程序(参见13.5节，第465页)。
答：
```
#define _SCL_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdlib>
#include <new>
#include <string>
#include <memory>

using namespace std;

void *operator new(size_t size){
	if (void *mem = malloc(size)) {
		cout << "In operator new " << endl;
		return mem;
	} else {
		throw std::bad_alloc();
	}
}

void operator delete(void *mem) noexcept {
	cout << "In operator delete " << endl; 
	free(mem);
}


// 类似vector类内存分配策略的简化实现
class StrVec {
public:
	StrVec() : elements(nullptr), first_free(nullptr), cap(nullptr) {} //allocator 成员进行默认初始化
	StrVec(const StrVec&); // 拷贝构造函数
	StrVec &operator=(const StrVec&); // 拷贝赋值运算符
	~StrVec(); // 析构函数
	void push_back(const string&); // 拷贝元素
	size_t size() const { return first_free - elements; }
	size_t capacity() const { return cap - elements; }
	string *begin() const { return elements; }
	string *end() const { return first_free; }
private:
	allocator<string> alloc; // 分配元素
	// 被添加元素的函数所使用
	void chk_n_alloc() { if (size() == capacity()) reallocate(); }
	// 工具函数，被拷贝构造函数、赋值运算符和析构函数所使用
	pair<string*, string*> alloc_n_copy(const string*, const string*);
	void free(); // 销毁元素并释放内存
	void reallocate(); // 获得更多内存并拷贝已有元素
	string *elements; // 指向数组首元素的指针
	string *first_free; // 指向数组第一个空闲元素的指针
	string *cap; // 指向数组尾后位置的指针
};

void StrVec::push_back(const string& s) {
	chk_n_alloc(); // 确保有空间容纳新元素
	// 在 first_free 指向的元素中构造 s 的副本
	alloc.construct(first_free++, s);
}

pair<string*, string*> StrVec::alloc_n_copy(const string *b, const string *e) {
	// 分配空间保存给定范围中的元素
	auto data = alloc.allocate(e - b);
	// 初始化并返回一个 pair，该 pair 由 data 和 uninitialized_copy 的返回值构成
	return{ data, uninitialized_copy(b, e , data) };
}

void StrVec::free() {
	// 不能传递给deallocate一个空指针,如果elements为0,函数什么也不做
	if (elements) {
		// 逆序销毁旧元素
		for (auto p = first_free; p != elements; /* 空 */) {
			alloc.destroy(--p);
		}
		alloc.deallocate(elements, cap - elements);
	}
}

StrVec::StrVec(const StrVec &s) {
	// 调用 alloc_n_copy分配空间以容纳与 s 中一样多的元素
	auto newdata = alloc_n_copy(s.begin(), s.end());
	elements = newdata.first;
	first_free = cap = newdata.second;
}

StrVec::~StrVec() { free(); }

StrVec &StrVec::operator=(const StrVec &rhs) {
	// 调用 alloc_n_copy分配内存,大小与rhs中元素占用空间一样多
	auto data = alloc_n_copy(rhs.begin(), rhs.end());
	free();
	elements = data.first;
	first_free = cap = data.second;
	return *this;
}

void StrVec::reallocate() {
	// 我们将分配当前大小两倍的内存空间
	auto newcapacity = size() ? 2 * size() : 1;
	// 分配新内存
	auto newdata = alloc.allocate(newcapacity);
	// 将数据从旧内存移动到新内存
	auto dest = newdata; // 指向新数组中下一个空闲位置
	auto elem = elements; // 指向旧数组中下一个元素
	for (size_t i = 0; i != size(); ++i) {
		alloc.construct(dest++, move(*elem++));
	}
	free(); // 一旦我们移动完元素就释放旧内存空间
	// 更新我们的数据结构，执行新元素
	elements = newdata;
	first_free = dest;
	cap = elements + newcapacity;
}


int main() {
	StrVec v;
	v.push_back("Hello");
	v.push_back("World");
	for (auto i = v.begin(); i != v.end(); ++i) {
		cout << *i << endl;
	}

	system("pause");
	return 0;
}
```
## 练习 19.3：
### 已知存在如下的类继承体系，其中每个类分别定义了一个公有的默认构造函数和一个虚析构函数：
```
class A { /* . . . */ };
class B : public A { /* . . . */ };
class C : public B { /* . . . */ };
class D : public B, public A { /* . . . */ };
```
### 下面的哪个dynamic_cast将失败？
```
(a) A *pa = new C;
 B *pb = dynamic_cast< B* >(pa);
(b) B *pb = new B;
 C *pc = dynamic_cast< C* >(pb);
(c) A *pa = new D;
 B *pb = dynamic_cast< B* >(pa);
```
答：
* (a) 成功转换
* (b) 将失败。
* (c) dynamic_cast语句可以成功转换，但是A *pa = new D;赋值语句会因为二义性问题而导致编译错误。
## 练习 19.4：
### 使用上一个练习定义的类改写下面的代码,将表达式*pa转换成类型C&：
```
if (C *pc = dynamic_cast< C* >(pa))
 // 使用 c 的成员
} else {
 // 使用 A 的成员
}
```
答：
```
	try {
		C& rc = dynamic_cast<C&>(*pa);
		// 使用C的成员
	} catch (const std::bad_cast &e) {
		std::cout << e.what() << std::endl;
	}
```
## 练习 19.5：
### 在什么情况下你应该使用dynamic_cast替代虚函数？
答：
* 想使用基类对象的指针或引用执行某个派生类操作并且该操作不是虚函数时。
## 练习 19.6：
### 编写一条表达式将Query_base指针动态转换为AndQuery指针(参见15.9.1节,第564页)。 分别使用AndQuery的对象以及其他类型的对象测试转换是否有效。 打印一条表示类型转换是否成功的信息,确保实际输出的结果与期望的一致。
答：
```
	Query_base* Query_basePtr = new AndQuery({ "" }, { "" }); // 实际指向AndQuery对象的Query_base类型指针
	// 尝试将基类Query_base指针转换为派生类AndQuery的指针
	AndQuery* AndQueryPtr = dynamic_cast<AndQuery*>(Query_basePtr);
	if (AndQueryPtr) {
		std::cout << "Conversion to AndQuery succeed.\n";
	} else {
		std::cout << "Conversion to AndQuery failed.\n";
	}

	Query_base* Query_basePtr_2 = new OrQuery({ "" }, { "" }); // 实际指向OrQuery对象的Query_base类型指针
	// 尝试将基类Query_base指针转换为派生类AndQuery的指针
	AndQuery* AndQueryPtr_2 = dynamic_cast<AndQuery*>(Query_basePtr_2);
	if (AndQueryPtr_2) {
		std::cout << "Conversion to AndQuery succeed.\n";
	} else {
		std::cout << "Conversion to AndQuery failed.\n";
	}
```
## 练习 19.7：
### 编写与上一个练习类似的转换,这一次将Query_base对象转换为AndQuery的引用。重复上面的测试过程,确保转换能正常工作。
答：
```
	Query_base* Query_basePtr = new AndQuery({ "" }, { "" }); // 实际指向AndQuery对象的Query_base类型指针
	try {
		AndQuery& AndQueryRef = dynamic_cast<AndQuery&>(*Query_basePtr);
		std::cout << "Conversion to AndQuery succeed.\n";
	} catch (const std::bad_cast&) {
		std::cout << "Conversion to AndQuery failed.\n";
	}

	Query_base* Query_basePtr_2 = new OrQuery({ "" }, { "" }); // 实际指向OrQuery对象的Query_base类型指针
	try {
		AndQuery& AndQueryRef = dynamic_cast<AndQuery&>(*Query_basePtr_2);
		std::cout << "Conversion to AndQuery succeed.\n";
	} catch (const std::bad_cast&) {
		std::cout << "Conversion to AndQuery failed.\n";
	}
```
## 练习 19.8：
### 编写一条typeid表达式检查两个Query_base对象是否指向同一种类型。 再检查该类型是否是 AndQuery。
答：
```
	Query_base* Query_basePtr = new AndQuery({ "" }, { "" });
	Query_base* Query_basePtr_2 = new AndQuery({ "" }, { "" });
	if (typeid(*Query_basePtr) != typeid(*Query_basePtr_2)) {
		std::cout << "Does not refer to the same type.\n";
	} else if(typeid(*Query_basePtr)== typeid(AndQuery)){
		std::cout << "The type is AndQuery.\n";
	} else {
		std::cout << "refer to the same type, but the type is not AndQuery.\n";
	}
```
## 练习 19.9：
### 编写与本节最后一个程序类似的代码,令其打印你的编译器为一些常见类型所起的名字。 如果你得到的输出结果与本书类似,尝试编写一个函数将这些字符串翻译成人们更容易读懂的形式。
答：
```
int arr[10];
	Derived d;
	Base *p = &d;
	std::cout << typeid(42).name() << ", "
		<< typeid(arr).name() << ", "
		<< typeid(Sales_data).name() << ", "
		<< typeid(std::string).name() << ", " 
		<< typeid(p).name() << ", " 
		<< typeid(*p).name() << std::endl;
```
* 输出结果：
* int, int \[10], struct Sales_data, class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >, class Base * __ptr64, class Derived
## 练习 19.10：
### 已知存在如下的类继承体系，其中每个类定义了一个默认公有的构造函数和一个虚析构函数。下面的语句将打印哪些类型名字？
```
class A { /* . . . */ };
class B : public A { /* . . . */ };
class C : public B { /* . . . */ };
(a) A *pa = new C;
 cout << typeid(pa).name() << endl;
(b) C cobj;
 A& ra = cobj; cout << typeid(&ra).name() << endl;
(c) B *px = new B;
 A& ra = *px; cout << typeid(ra).name() << endl;
```
答：
* (a) A*
* (b) A*
* (c) B
## 练习 19.11：
### 普通的数据指针与指向数据成员的指针有何区别？
答：
* 普通的数据指针直接指向具体的数据，可以直接通过解引用（*ptr）访问或修改指向的数据；而指向数据成员的指针需要与特定的类对象一起使用，通过对象.*指针或对象指针->\*指针来访问或修改数据成员。
## 练习 19.12：
### 定义一个成员指针,令其可以指向screen类的cursor成员。通过该指针获得Screen::cursor的值。
答：
```
class Screen {
public:
	typedef std::string::size_type pos;
	// 添加的成员函数，用于返回指向cursor成员的指针
	static pos Screen::* getCursorPtr() {
		return &Screen::cursor;
	}
private:
	pos cursor;
};

int main() {
	Screen::pos Screen::* cursorPtr = Screen::getCursorPtr();

	return 0;
}
```
## 练习 19.13：
### 定义一个类型,使其可以表示指向Sales_data类的bookNo成员的指针。
答：
```
std::string Sales_data::* bookNoPtr;
```
## 练习 19.14：
### 下面的代码合法吗?如果合法,代码的含义是什么?如果不合法,解释原因。
```
auto pmf = &Screen::get_cursor;
pmf = &Screen::get;
```
答：
* 这段代码是合法的。这两个成员函数get_cursor和get都有相同的函数类型，代码首先使用auto关键字自动推导pmf的类型，并将其初始化为指向Screen::get_cursor的成员函数指针，然后被重新赋值为指向get。
## 练习 19.15：
### 普通函数指针和指向成员函数的指针有何区别？
答：
* 普通函数指针是指向全局函数或静态函数的指针。
* 指向成员函数的指针是指向类的非静态成员函数的指针。
## 练习 19.16：
### 声明一个类型别名,令其作为指向sales_data的avg_price成员的指针的同义词。
答：
* using Action = double (Sales_data::*)()const;
## 练习 19.17：
### 为Screen的所有成员函数类型各定义一个类型别名。
答：
```
	// 为get_cursor函数定义类型别名
	using GetCursorAction = char (Screen::*)() const;
	// 为get函数定义类型别名
	using GetAction = char (Screen::*)() const;
	// 为get(pos, pos)函数定义类型别名
	using GetPosAction = char (Screen::*)(pos, pos) const;
```
## 练习 19.18： 
### 编写一个函数，使用 count_if 统计在给定的 vector 中有多少个空 string。
答：
```
#include <iostream>
#include <vector>
#include <string>
#include <algorithm> // 包含 count_if

// 函数：统计 vector 中空字符串的数量
size_t countEmptyStrings(const std::vector<std::string>& vec) {
    // 使用 lambda 表达式作为 count_if 的条件
    return std::count_if(vec.begin(), vec.end(), 
                         [](const std::string& s) { return s.empty(); });
}

int main() {
    // 示例：创建一个包含空字符串的 vector
    std::vector<std::string> strings = {"hello", "", "world", "", ""};

    // 调用函数并打印结果
    size_t count = countEmptyStrings(strings);
    std::cout << "Number of empty strings: " << count << std::endl;

    return 0;
}
```
## 练习 19.19：
### 编写一个函数,令其接受vector<Sales_data>并查找平均价格高于某个值的第一个元素。
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

// Sales_data 结构的定义
struct Sales_data {
	Sales_data() = default;
	Sales_data(const std::string &s) : bookNo(s) {}
	Sales_data(const std::string &s, unsigned n, double p) : bookNo(s), units_sold(n), revenue(p * n) {}

	std::string isbn() const { return bookNo; }
	double avg_price() const {
		if (units_sold) {
			return revenue / units_sold;
		} else {
			return 0;
		}
	}

private:
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

// 函数：找出第一个平均价格高于给定值的 Sales_data
Sales_data findFirstHighPrice(const std::vector<Sales_data>& vec, double price) {
	auto it = std::find_if(vec.begin(), vec.end(), [price](const Sales_data& data) {
		return data.avg_price() > price;
	});

	if (it != vec.end()) {
		return *it;  // 返回找到的元素
	} else {
		// 如果没有找到，返回一个默认的 Sales_data
		return Sales_data();
	}
}

int main() {
	// 测试数据
	std::vector<Sales_data> sales = {
		Sales_data("Book1", 10, 20.5),
		Sales_data("Book2", 5, 22.3),
		Sales_data("Book3", 2, 50.0)
	};

	// 查找平均价格大于40的第一个 Sales_data 对象
	Sales_data result = findFirstHighPrice(sales, 40.0);
	if (result.isbn() != "") {
		std::cout << "Found: " << result.isbn() << " with avg price: " << result.avg_price() << std::endl;
	} else {
		std::cout << "No Sales_data found with avg price > 40.0" << std::endl;
	}
	system("pause");
	return 0;
}
```
## 练习 19.20：
### 将你的QueryResult类嵌套在TextQuery中,然后重新运行12.3.2节(第435页)中使用了TextQuery的程序。
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

using namespace std;

class TextQuery {
public:
	class QueryResult;
	using line_no = vector<string>::size_type;
	TextQuery(ifstream&);
	QueryResult query(const string&) const;
private:
	shared_ptr<vector<string>> file; // 输入文件
	// 每个单词到它所在的行号的集合的映射
	map<string, shared_ptr<set<line_no>>> wm;
};

class TextQuery::QueryResult {
	friend std::ostream& print(std::ostream&, const QueryResult&);
public:
	QueryResult(string s, shared_ptr<set<line_no>> p,
		shared_ptr<vector<string>> f) : sought(s), lines(p), file(f) { }
private:
	string sought; // 查询单词
	shared_ptr<set<line_no>> lines; // 出现的行号
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

TextQuery::QueryResult TextQuery::query(const string &sought) const {
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

ostream &print(ostream & os, const TextQuery::QueryResult &qr) {
	// 如果找到了单词,打印出现次数和所有出现的位置
	os << qr.sought << " occurs " << qr.lines->size() << " " << "times" << endl;
	// 打印单词出现的每一行
	for (auto num : *qr.lines) { // 对 set 中每个单词
		// 避免行号从 0 开始给用户带来的困惑
		os << "\t(line " << num + 1 << ") " << *(qr.file->begin() + num) << endl;
	}
	return os;
}

void runQueries(ifstream &infile) {
	// infile是一个ifstream,指向我们要处理的文件
	TextQuery tq(infile); // 保存文件并建立查询 map
	// 与用户交互：提示用户输入要查询的单词，完成查询并打印结果
	while (true) {
		cout << "enter word to look for, or q to quit: ";
		string s;
		// 若遇到文件尾或用户输入了'q' 时循环终止
		if (!(cin >> s) || s == "q") break;
		// 指向查询并打印结果
		print(cout, tq.query(s)) << endl;
	}
}

int main() {
	ifstream ifs("example.txt");
	runQueries(ifs);

	system("pause");
	return 0;
}
```
## 练习 19.21：
### 编写你自己的Token类。
答：
```
#include <iostream>
#include <string>

using namespace std;

class Token {
public:
	// 因为union含有一个string成员,所以Token必须定义拷贝控制成员
	Token() : tok(INT), ival{ 0 } { }
	Token(const Token &t) : tok(t.tok) { copyUnion(t); }
	Token &operator=(const Token&);
	// 如果 union 含有一个 string成员，则我们必须销毁它
	~Token() { if (tok == STR) sval.~string(); }
	// 下面的赋值运算符负责设置 union 的不同成员
	Token &operator=(const string&);
	Token &operator=(char);
	Token &operator=(int);
	Token &operator=(double);
private:
	enum { INT, CHAR, DBL, STR } tok; // 判别式
	union { // 匿名 union
		char cval;
		int ival;
		double dval;
		string sval;
	}; // 每个Token对象含有一个该未命名 union类型的未命名成员
	   // 检查判别式，然后酌情拷贝 union 成员
	void copyUnion(const Token&);
};
Token &Token::operator=(const Token &t) {
	// 如果此对象的值是 string 而t的值不是，则我们必须释放原来的 string
	if (tok == STR && t.tok != STR) sval.~string();
	if (tok == STR && t.tok == STR) {
		sval = t.sval; // 无须构造一个新 string
	} else {
		copyUnion(t); // 如果 t.tok 是 STR，则需要构造一个 string
	}
	tok = t.tok;
	return *this;
}
Token &Token::operator=(const string &s) {
	if (tok == STR) { // 如果当前存储的是 string，可以直接赋值
		sval = s;
	} else {
		new(&sval) string(s); // 否则需要先构造一个 string
	}
	tok = STR; // 更新判别式
	return *this;
}
Token &Token::operator=(char c) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	cval = c;							// 为成员赋值
	tok = CHAR;							// 更新判别式
	return *this;
}
Token &Token::operator=(int i) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	ival = i;							// 为成员赋值
	tok = INT;							// 更新判别式
	return *this;
}
Token &Token::operator=(double d) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	dval = d;							// 为成员赋值
	tok = DBL;							// 更新判别式
	return *this;
}
void Token::copyUnion(const Token &t) {
	switch (t.tok) {
	case INT:
		ival = t.ival;
		break;
	case CHAR:
		cval = t.cval;
		break;
	case DBL:
		dval = t.dval;
		break;
	// 要想拷贝一个 string 可以使用定位 new 表达式构造它
	case STR:
		new(&sval) string(t.sval);
		break;
	}
}
```
## 练习 19.22：
### 为你的Token类添加一个Sales_data类型的成员。
答：
```
#include <iostream>
#include <string>

using namespace std;

struct Sales_data {
	Sales_data() = default;
	Sales_data(const std::string &s) :bookNo(s) {}
	Sales_data(const std::string &s, unsigned n, double p) :bookNo(s), units_sold(n), revenue(p*n) {}
	std::string isbn() const { return bookNo; }
private:
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
};

class Token {
public:
	// 因为union含有一个string成员,所以Token必须定义拷贝控制成员
	Token() : tok(INT), ival{ 0 } { }
	Token(const Token &t) : tok(t.tok) { copyUnion(t); }
	Token &operator=(const Token&);
	// 如果 union 含有一个 string或Sales_data成员，则我们必须销毁它
	~Token() { 
		if (tok == STR) sval.~string();
		if (tok == SALE) item.~Sales_data();
	}
	// 下面的赋值运算符负责设置 union 的不同成员
	Token &operator=(const Sales_data&);
	Token &operator=(const string&);
	Token &operator=(char);
	Token &operator=(int);
	Token &operator=(double);
private:
	enum { INT, CHAR, DBL, STR, SALE } tok; // 判别式
	union { // 匿名 union
		char cval;
		int ival;
		double dval;
		string sval;
		Sales_data item;
	}; // 每个Token对象含有一个该未命名 union类型的未命名成员
	   // 检查判别式，然后酌情拷贝 union 成员
	void copyUnion(const Token&);
};

Token &Token::operator=(const Token &t) {
	if (this == &t) {
		return *this;  // 检测自赋值并提前返回
	}
	// 如果此对象的值是 Sales_data 而t的值不是，则我们必须释放原来的 Sales_data
	if (tok == SALE && t.tok != SALE) item.~Sales_data();
	// 如果此对象的值是 string 而t的值不是，则我们必须释放原来的 string
	if (tok == STR && t.tok != STR) sval.~string();

	if (tok == SALE && t.tok == SALE) {
		item = t.item; // 无须构造一个新 Sales_data
	} else if (tok == STR && t.tok == STR) {
		sval = t.sval; // 无须构造一个新 string
	}else{
		copyUnion(t); // 如果 t.tok 是 STR，则需要构造一个 string
	}
	tok = t.tok;
	return *this;
}
Token &Token::operator=(const Sales_data &s) {
	if (tok == SALE) { // 如果当前存储的是 Sales_data，可以直接赋值
		item = s;
	} else {
		new(&item) Sales_data(s); // 否则需要先构造一个 Sales_data
	}
	tok = SALE; // 更新判别式
	return *this;
}
Token &Token::operator=(const string &s) {
	if (tok == STR) { // 如果当前存储的是 string，可以直接赋值
		sval = s;
	} else {
		new(&sval) string(s); // 否则需要先构造一个 string
	}
	tok = STR; // 更新判别式
	return *this;
}
Token &Token::operator=(char c) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	cval = c;							// 为成员赋值
	tok = CHAR;							// 更新判别式
	return *this;
}
Token &Token::operator=(int i) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	ival = i;							// 为成员赋值
	tok = INT;							// 更新判别式
	return *this;
}
Token &Token::operator=(double d) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	dval = d;							// 为成员赋值
	tok = DBL;							// 更新判别式
	return *this;
}
void Token::copyUnion(const Token &t) {
	switch (t.tok) {
	case INT:
		ival = t.ival;
		break;
	case CHAR:
		cval = t.cval;
		break;
	case DBL:
		dval = t.dval;
		break;
		// 要想拷贝一个 string 可以使用定位 new 表达式构造它
	case STR:
		new(&sval) string(t.sval);
		break;
		// 要想拷贝一个 Sales_data 可以使用定位 new 表达式构造它
	case SALE:
		new(&item) Sales_data(t.item);
		break;
	}
}
```
## 练习 19.23：
### 为你的Token类添加移动构造函数和移动赋值运算符。
答：
```
	// 移动构造函数
	Token::Token(Token &&t) noexcept : tok(t.tok) {
		switch (tok) {
		case INT: ival = t.ival; break;
		case CHAR: cval = t.cval; break;
		case DBL: dval = t.dval; break;
		case STR: new (&sval) string(std::move(t.sval)); break;  // 使用std::move来转移string
		}
		t.tok = INT;  // 设置t为无害状态
	}
	// 移动赋值运算符
	Token &Token::operator=(Token &&t) noexcept {
		// 自赋值检测
		if (this != &t) {
			// 如果当前存储的是string，需要先释放它
			if (tok == STR) sval.~string();
			tok = t.tok;
			switch (tok) {
			case INT: ival = t.ival; break;
			case CHAR: cval = t.cval; break;
			case DBL: dval = t.dval; break;
			case STR: new (&sval) string(std::move(t.sval)); break;  // 转移string
			}
			t.tok = INT;  // 设置t为无害状态
		}
		return *this;
	}
```
## 练习 19.24：
### 如果我们将一个 Token 对象赋给它自己将发生什么情况？
答：
* 会调用拷贝赋值运算符，如果作为左侧运算对象的union的值是string但右侧运算对象的值不是,则我们必须先释放原来的string再给union成员赋一新值。如果两侧运算对象的值都是string,则我们可以使用普通的string赋值运算符完成拷贝。否则,我们调用copyUnion进行赋值。在copyUnion内部,如果右侧运算对象是string,则我们在左侧运算对象的dunion成员内构造一个新string;如果两端都不是string,则直接执行普通的赋值操作就可以了。
## 练习 19.25：
### 编写一系列赋值运算符,令其分别接受union中各种类型的值。
答：
```
Token &Token::operator=(const string &s) {
	if (tok == STR) { // 如果当前存储的是 string，可以直接赋值
		sval = s;
	} else {
		new(&sval) string(s); // 否则需要先构造一个 string
	}
	tok = STR; // 更新判别式
	return *this;
}
Token &Token::operator=(char c) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	cval = c;							// 为成员赋值
	tok = CHAR;							// 更新判别式
	return *this;
}
Token &Token::operator=(int i) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	ival = i;							// 为成员赋值
	tok = INT;							// 更新判别式
	return *this;
}
Token &Token::operator=(double d) {
	if (tok == STR) sval.~string();		// 如果当前存储的是 string，释放它
	dval = d;							// 为成员赋值
	tok = DBL;							// 更新判别式
	return *this;
}
```
## 练习 19.26：
### 说明下列声明语句的含义并判断它们是否合法:
```
extern "C" int compute(int *, int);
extern "C" double compute(double *, double);
```
答：
* 这两条语句声明了两个名为compute的函数，两个函数都以C语言的链接规则被编译器处理。但这是非法的。因为使用extern "C"时，C++的名称改编被禁用。这意味着两个函数会被视为拥有相同的符号名compute，因此在链接时会发生冲突。
