## 练习 13.1：
### 拷贝构造函数是什么？什么时候使用它?
答：
* 当一个对象用另一个同类型的对象初始化时。
* 当一个函数的参数是类的对象，并通过值传递。
* 当函数按值返回一个类的对象。
* 其他需要复制同类型对象的情况。
## 练习 13.2：
### 解释为什么下面的声明是非法的：
```
Sales_data::Sales_data(Sales_data rhs);
```
答：
* 因为按值传递参数会触发复制操作，所以如果复制构造函数自己还需要按值传递其类型的对象，就会导致无限递归，从而造成程序崩溃。
## 练习 13.3:
### 当我们拷贝一个StrBlob时,会发生什么?拷贝一个StrBlobPtr呢?
答：
* 会有一个StrBlob和StrBlobPtr被拷贝！
* StrBlob的数据成员含有一个shared_ptr指针，StrBlobPtr的数据成员含有的则是一个weak_ptr指针，拷贝后shared_ptr的计数器会递增。
* StrBlob使用的是编译器合成的拷贝构造函数，该版本拷贝构造函数只会进行浅复制，所以StrBlob构造函数内动态分配的vector会被2个StrBlob对象共享。
## 练习 13.4:
### 假定Point是一个类类型,它有一个public的拷贝构造函数,指出下面程序片段中哪些地方使用了拷贝构造函数:
```

```
答：
```
Point global;
Point foo_bar(Point arg) // 按值传参时，通常会使用拷贝构造函数来初始化形参。
{
 Point local = arg, *heap = new Point(global); // 这里使用拷贝构造函数从arg初始化local。
 *heap = local;
 Point pa[ 4 ] = { local, *heap }; // local和*heap都用于初始化数组pa中的元素，这里使用了拷贝构造函数两次。
 return *heap; // 按值返回函数时，通常会使用拷贝构造函数来复制返回值
}
```
## 练习 13.5:
### 给定下面的类框架,编写一个拷贝构造函数,拷贝所有成员。你的构造函数应该动态分配一个新的string (参见12.1.2节,第407页),并将对象拷贝到ps指向的位置，而不是 ps 本身的位置。
```
class HasPtr {
public:
 HasPtr(const std::string &s = std::string()):
    ps(new std::string(s)), i(0) { }
private:
 std::string *ps;
 int i;
};
```
答：
```
class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }

	// 拷贝构造函数
	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)), i(hp.i) { }

private:
	std::string *ps;
	int i;
};
```
## 练习 13.6：
### 拷贝赋值运算符是什么？什么时候使用它？合成拷贝赋值运算符完成什么工作？什么时候会生成合成拷贝赋值运算符？
答：
* 拷贝赋值运算符是类的一个特殊的成员函数，用于处理类对象之间的赋值操作。其名为 operator=，通常接受一个与当前类类型相同的参数。
* 当我们要将一个对象的值赋给另一个已经存在的同类型对象时，就会使用拷贝赋值运算符。
* 合成拷贝赋值运算符会对其左侧对象的每个非static成员进行赋值操作。
* 当类未定义自己的拷贝赋值运算符时，编译器会为其生成一个合成拷贝赋值运算符。但需要注意的是，如果类定义了一个移动构造函数或移动赋值运算符，编译器就不会为其合成拷贝赋值运算符。
## 练习 13.7:
### 当我们将一个StrBlob赋值给另一个StrBlob时,会发生什么?赋值StrBlobPtr 呢?
答：
* StrBlob和StrBlobPtr都是采用的合成拷贝赋值运算符，只会进行浅拷贝，即对类的每个成员进行逐成员的赋值。对于指针成员，这意味着指针值本身（而不是它所指向的内容）会被复制。
## 练习 13.8：
### 为 13.1.1 节（第 443 页）练习 13.5 中的 HasPtr 类编写赋值运算符。类似拷贝构造函数,你的赋值运算符应该将对象拷贝到ps指向的位置。
答：
```
class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }
	// 拷贝构造函数
	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)), i(hp.i) { }
	// 拷贝赋值运算符
	HasPtr& operator=(const HasPtr& rhs) {
		auto newp = new std::string(*rhs.ps);
		delete ps;
		ps = newp;
		i = rhs.i;
		return *this;
	}
private:
	std::string *ps;
	int i;
};
```
## 练习13.9:
### 析构函数是什么?合成析构函数完成什么工作?什么时候会生成合成析构函数？
答：
* 析构函数是一个特殊的成员函数，它在一个对象的生命周期结束时自动被调用。析构函数的名称与类名相同，但前面加上了一个波浪符~，并且它不接受任何参数，也没有返回类型。
* 合成析构函数是编译器自动为类生成的析构函数。合成析构函数确保类的所有非静态成员对象都会被正确地销毁。但合成析构函数不会释放对象可能拥有的动态分配的内存，这种内存管理应由程序员手动处理。
* 当一个类没有显式定义析构函数时，编译器会自动为这个类生成一个合成析构函数。
## 练习 13.10:
### 当一个StrBlob 对象销毁时会发生什么？一个 StrBlobPtr 对象销毁时呢？
答：
* 当类对象被销毁时析构函数会被调用，析构函数体首先执行，随后它的所有成员和基类的析构函数按逆序（与构造顺序相反）被调用。最后，对象所占的内存会被释放。
## 练习 13.11:
### 为前面练习中的HasPtr类添加一个析构函数。
答：
```
class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }
	// 拷贝构造函数
	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)), i(hp.i) { }
	// 拷贝赋值运算符
	HasPtr& operator=(const HasPtr& rhs) {
		auto newp = new std::string(*rhs.ps);
		delete ps;
		ps = newp;
		i = rhs.i;
		return *this;
	}
	// 析构函数
	~HasPtr() {
		delete ps;
	}
private:
	std::string *ps;
	int i;
};
```
## 练习 13.12：
### 在下面的代码片段中会发生几次析构函数调用？
```
bool fcn(const Sales_data *trans, Sales_data accum)
{
 Sales_data item1(*trans), item2(accum);
 return item1.isbn() != item2.isbn();
}
```
答：
* 三次。accum、item1 和 item2 会因离开作用域的销毁而调用析构函数。
## 练习 13.13：
### 理解拷贝控制成员和构造函数的一个好方法是定义一个简单的类，为该类定义这些成员，每个成员都打印出自己的名字：
```
struct X {
X() {std::cout << "X()" << std::endl;}
X(const X&) {std::cout << "X(const X&)" <<std::endl;}
};
```
### 给 X 添加拷贝赋值运算符和析构函数，并编写一个程序以不同方式使用 X 的对象：将它们作为非引用和引用参数传递；动态分配它们；将它们存放于容器中；诸如此类。观察程序的输出，直到你确认理解了什么时候会使用拷贝控制成员，以及为什么会使用它们。当你观察程序输出时，记住编译器可以略过对拷贝构造函数的调用。
答：
```
#include <iostream>
#include <vector>

using namespace std;

struct X {
	X() { std::cout << "X()" << std::endl; }
	X(const X&) { std::cout << "X(const X&)" << std::endl; }
	X& operator=(const X&) {
		std::cout << "X& operator=(const X&)" << std::endl;
		return *this;
	}
	~X() { std::cout << "~X()" << std::endl; }
};

void f1(X x) { }
void f2(X &x) { }
void f3(const X &x) { }

int main() {
	std::cout << "Directly create an object:" << std::endl;
	X x1;

	std::cout << "\nCopy initialize an object:" << std::endl;
	X x2 = x1;

	std::cout << "\nAssign an object:" << std::endl;
	X x3;
	x3 = x1;

	std::cout << "\nPass an object by value:" << std::endl;
	f1(x1);

	std::cout << "\nPass an object by reference:" << std::endl;
	f2(x1);

	std::cout << "\nPass an object by const reference:" << std::endl;
	f3(x1);

	std::cout << "\nDynamically allocate objects:" << std::endl;
	X* px = new X;
	delete px;

	std::cout << "\nStore objects in a container:" << std::endl;
	std::vector<X> vec;
	vec.push_back(x1);

	std::cout << "\nEnd of main()" << std::endl;
	return 0;
}
```
## 练习 13.14：
### 假定 numbered 是一个类，它有一个默认构造函数，能为每个对象生成一个唯一的序号,保存在名为mysn的数据成员中。 假定numbered使用合成的拷贝控制成员，并给定如下函数：
```
void f (numbered s) { cout << s.mysn << endl; }
```
### 则下面代码输出什么内容？
```
numbered a, b = a, c = b;
f(a); f(b); f(c);
```
答：
* 三条输出的内容相同。
## 练习 13.15:
### 假定numbered定义了一个拷贝构造函数,能生成一个新的序号。 这会改变上一题中调用的输出结果吗？ 如果会改变，为什么？ 新的输出结果是什么？
答：
* 会改变。因为新定义的拷贝构造函数保证了拷贝发生时也能生成一个新的序号。新的输出结果是函数f的局部numbered对象的序号。
## 练习 13.16：
### 如果 f 中的参数是 const numbered&，将会怎样？这会改变输出结果吗？如果会改变，为什么？新的输出结果是什么？
答：
* 将会按引用传参。会改变输出结果。因为按引用传参不会创建新的numbered对象。新的输出结果是调用f()前 a、b、c的序号。
## 练习 13.17：
### 分别编写前三题中所描述的 numbered 和 f，验证你是否正确预测了输出结果。
答：
```
#include <iostream>

using namespace std;

class numbered {
public:
	// 默认构造函数
	numbered() : mysn(unique++) {}
	// 拷贝构造函数
	// numbered(const numbered& n) : mysn(unique++) {}
	// 返回对象的序号
	int get_mysn() const { return mysn; }
private:
	int mysn;               // 保存对象的序号
	static int unique;      // 静态数据成员，用于生成唯一序号
};

// 初始化静态数据成员
int numbered::unique = 9;

void f(numbered s) { cout << s.get_mysn() << endl; }
//void f(const numbered &s) { cout << s.get_mysn() << endl; }

int main() {
	numbered a, b = a, c = b;
	f(a); f(b); f(c);

	return 0;
}
```
## 练习 13.18:
### 定义一个Employee类,它包含雇员的姓名和唯一的雇员证号。 为这个类定义默认构造函数，以及接受一个表示雇员姓名的 string的构造函数。 每个构造函数应该通过递增一个static数据成员来生成一个唯一的证号。
答：
```
#include <iostream>
#include <string>

class Employee {
public:
	// 默认构造函数
	Employee() : name(""), id(unique_id++) {}

	// 接受一个表示雇员姓名的 string 的构造函数
	Employee(const std::string& s) : name(s), id(unique_id++) {}

	// 获取姓名和ID的成员函数
	std::string getName() const { return name; }
	int getId() const { return id; }

private:
	std::string name;
	int id;

	// 静态数据成员，用于生成唯一的证号
	static int unique_id;
};

// 初始化静态数据成员
int Employee::unique_id = 0;

int main() {
	Employee e1, e2("John");
	std::cout << e1.getName() << ": " << e1.getId() << std::endl; // : 0
	std::cout << e2.getName() << ": " << e2.getId() << std::endl; // John: 1

	return 0;
}
```
## 练习 13.19：
### 你的Employee类需要定义它自己的拷贝控制成员吗？如果需要，为什么？如果不需要,为什么？实现你认为Employee需要的拷贝控制成员。
答：
* 为了保证为每个雇员分配一个唯一的证号，应该禁止拷贝构造和拷贝赋值。
```
class Employee {
public:
    // ... [其他成员如前]

    // 删除拷贝构造函数和拷贝赋值运算符
    Employee(const Employee&) = delete;
    Employee& operator=(const Employee&) = delete;
};
```
## 练习 13.20：
### 解释当我们拷贝、赋值或销毁 TextQuery 和 QueryResult 类(参见 12.3 节,第430页)对象时会发生什么。
答：
* TextQuery 和 QueryResult 类使用的是合成版本拷贝控制成员，当拷贝、赋值时会进行浅拷贝。当销毁时合成析构函数不会释放动态分配的内存，不过这两个类管理动态资源的指针都是智能指针，智能指针会负责释放动态分配的内存。
## 练习 13.21：
### 你认为 TextQuery 和 QueryResult 类需要定义它们自己版本的拷贝控制成员吗?如果需要,为什么?如果不需要,为什么?实现你认为这两个类需要的拷贝,控制操作。
答：
* 不需要，使用合成版本的拷贝控制成员并不会产生问题。
## 练习 13.22：
### 假定我们希望 HasPtr 的行为像一个值。即，对于对象所指向的 string 成员，每个对象都有一份自己的拷贝。我们将在下一节介绍拷贝控制成员的定义。但是，你已经学习了定义这些成员所需的所有知识。在继续学习下一节之前，为 HasPtr 编写拷贝构造函数和拷贝赋值运算符。
答：
```
class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }
	// 拷贝构造函数
	HasPtr(const HasPtr& hp) :ps(new std::string(*hp.ps)), i(hp.i) { }
	// 拷贝赋值运算符
	HasPtr& operator=(const HasPtr& rhs) {
		auto newp = new std::string(*rhs.ps);
		delete ps;
		ps = newp;
		i = rhs.i;
		return *this;
	}
	// 析构函数
	~HasPtr() {
		delete ps;
	}
private:
	std::string *ps;
	int i;
};
```
## 练习 13.23：
### 比较上一节练习中你编写的拷贝控制成员和这一节中的代码。 确定你理解上了你的代码和我们的代码之间的差异(如果有的话)。
答：
* 需要注意的是异常安全的问题，当new抛出bad_alloc异常时，已经终止了当前的函数执行流程，处理异常时不能简单地从该函数的中间某处重新开始。而是从更高的层次，例如对象的赋值a = b;这样的语句重新开始执行。要避免由异常而导致的问题，而不是在异常发生后再尝试修复它。
## 练习 13.24：
### 如果本节中的 HasPtr 版本未定义析构函数，将会发生什么？如果未定义拷贝构造函数，将会发生什么？
答：
* 未定义析构函数将发生内存泄露，未定义拷贝构造函数在复制同类型对象时将共享动态分配的内存，但在销毁对象时会产生访问无效内存、造成多次释放的未定义行为。
## 练习 13.25:
### 假定希望定义strBlob的类值版本,而且希望继续使用shared_ptr,这样我们的strBlobPtr 类就仍能使用指向 vector的weak_ptr了。 你修改后的类将需要一个拷贝构造函数和一个拷贝赋值运算符,但不需要析构函数。 解释拷贝构造函数和拷贝赋值运算符必须要做什么。 解释为什么不需要析构函数。
答：
* 拷贝构造函数和拷贝赋值运算符需要重新动态分配内存，拷贝右侧运算对象内的动态对象。strBlob指向动态对象的指针是智能指针，智能指针会在合适的时机释放内存，所以可以使用合成析构函数。
## 练习 13.26:
### 对上一题中描述的StrBlob类,编写你自己的版本。
答：
```
class StrBlob {
public:
	// ... [其他成员如前]

	// 拷贝构造函数
	StrBlob(const StrBlob& sb) :data(make_shared<vector<string>>(*sb.data)) { }
	// 拷贝赋值运算符
	StrBlob& operator=(const StrBlob& sb) {
		data = make_shared<vector<string>>(*sb.data);
		return *this;
	}
};
```
## 练习 13.27：
### 定义你自己的使用引用计数版本的 HasPtr。
答：
```
class HasPtr {
public:
	HasPtr(const string &s = string()) : data(new string(s)), i(0), ref_count(new int(1)) {}

	HasPtr(const HasPtr& rhs) : data(rhs.data), i(rhs.i), ref_count(rhs.ref_count) {
		++(*ref_count); // 增加引用计数
	}

	HasPtr& operator=(const HasPtr& rhs) {
		++(*rhs.ref_count);  // 首先，处理赋值对象的引用计数
		release();  // 释放当前对象的资源（如果适当的话）
		data = rhs.data;
		i = rhs.i;
		ref_count = rhs.ref_count;
		return *this;
	}

	~HasPtr() {
		release();
	}

private:
	string* data;
	int i;
	int* ref_count;

	void release() {
		if (--(*ref_count) == 0) {
			delete data;
			delete ref_count;
		}
	}
};
```
## 练习 13.28：
### 给定下面的类，为其实现一个默认构造函数和必要的拷贝控制成员。
```
(a)
class TreeNode {
private:
 std::string value;
 int count;
 TreeNode *left;
 TreeNode *right;
};
(b)
class BinStrTree {
private:
 TreeNode *root;
};
```
答：
```
(a)
class TreeNode {
public:
	TreeNode() : value(std::string()), count(0), left(nullptr), right(nullptr) {}
	TreeNode(const TreeNode& tn) : value(tn.value), count(tn.count),
		left(tn.left ? new TreeNode(*tn.left) : nullptr),
		right(tn.right ? new TreeNode(*tn.right) : nullptr) {}
	TreeNode& operator=(const TreeNode&);
	~TreeNode();

private:
	std::string value;
	int count;
	TreeNode* left;
	TreeNode* right;
};

TreeNode& TreeNode::operator=(const TreeNode& rhs) {
	if (&rhs == this) {
		return *this;
	}
	TreeNode* newLeft = rhs.left ? new TreeNode(*rhs.left) : nullptr;
	TreeNode* newRight = rhs.right ? new TreeNode(*rhs.right) : nullptr;

	delete left;
	delete right;

	left = newLeft;
	right = newRight;
	value = rhs.value;
	count = rhs.count;

	return *this;
}

TreeNode::~TreeNode() {
	delete left;
	delete right;
}

(b)
class BinStrTree {
public:
	BinStrTree() : root(new TreeNode()) {}
	BinStrTree(const BinStrTree& bst) : root(new TreeNode(*bst.root)) {}
	BinStrTree& operator=(const BinStrTree&);
	~BinStrTree();

private:
	TreeNode* root;
};

BinStrTree& BinStrTree::operator=(const BinStrTree& rhs) {
	if (&rhs != this) {
		TreeNode* newRoot = new TreeNode(*rhs.root);
		delete root;
		root = newRoot;
	}
	return *this;
}

BinStrTree::~BinStrTree() {
	delete root;
}
```
## 练习 13.29：
### 解释 swap(HasPtr&, HasPtr&)中对 swap 的调用不会导致递归循环。
答：
* 两种swap的形参列表不同。
## 练习 13.30：
### 为你的类值版本的 HasPtr 编写 swap 函数，并测试它。 为你的 swap 函数添加一个打印语句，指出函数什么时候执行。
答：
```
#include <iostream>
#include <string>
#include <algorithm>

class HasPtr {
public:
	// 构造函数
	HasPtr(const std::string &s = std::string()) : ps(new std::string(s)), i(0) { }
	// 拷贝构造函数
	HasPtr(const HasPtr& rhs) : ps(new std::string(*rhs.ps)), i(rhs.i) { }
	// 拷贝赋值运算符
	HasPtr& operator=(const HasPtr& rhs) {
		auto newp = new std::string(*rhs.ps);
		delete ps;
		ps = newp;
		i = rhs.i;
		return *this;
	}
	// 析构函数
	~HasPtr() {
		delete ps;
	}
	// 友元声明，使得非成员函数swap可以访问类的私有部分
	friend void swap(HasPtr&, HasPtr&);

private:
	std::string *ps;
	int i;
};

// 自定义的swap函数
void swap(HasPtr& lhs, HasPtr& rhs) {
	std::cout << "Executing custom swap function for HasPtr.\n";
	using std::swap;
	swap(lhs.ps, rhs.ps);
	swap(lhs.i, rhs.i);
}

int main() {
	HasPtr hp1("Test1");
	HasPtr hp2("Test2");
	swap(hp1, hp2);

	return 0;
}
```
## 练习 13.31:
### 为你的HasPtr类定义一个<运算符,并定义一个HasPtr的vector为这个vector添加一些元素,并对它执行sort。 注意何时会调用swap。
答：
* std::sort 的实现目的是为了高效排序，而不是保证特定的交换机制。当为特定类型提供了自定义的 swap 时，某些情况下它可能会被 std::sort 使用，但并不保证一定会。
```
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>

class HasPtr {
public:
	// 构造函数
	HasPtr(const char* s) : ps(new std::string(s)), i(0) { }
	HasPtr(const std::string &s = std::string()) :ps(new std::string(s)), i(0) { }
	// 拷贝构造函数
	HasPtr(const HasPtr& rhs) :ps(new std::string(*rhs.ps)), i(rhs.i) { }
	// 拷贝赋值运算符
	HasPtr& operator=(const HasPtr& rhs) {
		auto newp = new std::string(*rhs.ps);
		delete ps;
		ps = newp;
		i = rhs.i;
		return *this;
	}
	// < 运算符
	bool operator<(const HasPtr& rhs) const {
		return *ps < *rhs.ps;
	}
	// 析构函数
	~HasPtr() {
		delete ps;
	}

	// 为了观察swap的调用
	friend void swap(HasPtr& lhs, HasPtr& rhs);

//private:
	std::string *ps;
	int i;
};

// 自定义swap
inline void swap(HasPtr& lhs, HasPtr& rhs) {
	using std::swap;
	swap(lhs.ps, rhs.ps);
	swap(lhs.i, rhs.i);
	std::cout << "swap is called!" << std::endl;
}

int main() {
	std::vector<HasPtr> vec = { "Cherry", "Apple", "Banana", "Grape", "Orange" };
	std::sort(vec.begin(), vec.end());

	return 0;
}
```
## 练习 13.32:
### 类指针的HasPtr版本会从swap函数受益吗?如果会,得到了什么益处?如果不是，为什么？
答：
* swap函数对指针类HasPtr也是有益处的，首先调用swap可以简化两个对象的交换操作，提高了效率并避免为创建临时变量的资源分配和释放。
* 以swap函数实现拷贝并替换技术的拷贝赋值运算符虽然对性能没有影响，但在简洁性、安全性和可维护性上也都有所提升。
```
//标准的交换操作大致如下：
HasPtr temp = a; // 调用拷贝构造函数
a = b;           // 调用拷贝赋值操作符
b = temp;        // 调用拷贝赋值操作符

//而 swap(a,b) 函数内部：
friend void swap(HasPtr& a, HasPtr& b) {
    std::swap(a.data, b.data);
    std::swap(a.i, b.i);
    std::swap(a.ref_count, b.ref_count);
}
```
## 练习 13.33:
### 为什么Message的成员save和remove的参数是一个Folder&?为什么我们不将参数定义为Folder或是const Folder&?
答：
* save和remove需要保存、删除指向实参Folder的指针而不是一个指向在函数内创建的临时变量的指针，所以需要按引用传递Folder对象。还需要修改该Folder对象的数据成员，并且不能使用const引用。
## 练习 13.34:
### 编写本节所描述的Message。
答：
```
class Folder;

class Message {
	friend class Folder;
	friend void swap(Message &, Message &);
public:
	// folders被隐式初始化为空集合
	explicit Message(const std::string &str = "") : contents(str) { }
	// 拷贝控制成员,用来管理指向本Message的指针
	Message(const Message&); // 拷贝构造函数
	Message& operator=(const Message&); // 拷贝赋值运算符
	~Message(); // 析构函数
	// 从给定 Folder 集合中添加/删除本 Message
	void save(Folder&);
	void remove(Folder&);
private:
	std::string contents; // 实际消息文本
	std::set<Folder*> folders; // 包含本 Message 的 Folder
	// 拷贝构造函数、拷贝赋值运算符和析构函数所使用的工具函数
	// 将本Message添加到指向参数的Folder中
	void add_to_Folders(const Message&);
	// 从 folders 中的每个 Folder 中删除本 Message
	void remove_from_Folders();
};

Message::Message(const Message &m) : contents(m.contents), folders(m.folders) {
	add_to_Folders(m); // 将本消息添加到指向m的Folder中
}

Message& Message::operator=(const Message &rhs) {
	// 通过先删除指针再插入它们来处理自赋值情况
	remove_from_Folders(); // 更新已有 Folder
	contents = rhs.contents; // 从 rhs 拷贝消息内容
	folders = rhs.folders; // 从rhs拷贝Folder指针
	add_to_Folders(rhs); // 将本 Message 添加到那些 Folder 中
	return *this;
}

Message::~Message() {
	remove_from_Folders();
}

void Message::save(Folder &f) {
	folders.insert(&f); // 将给定Folder添加到我们的Folder列表中
	f.addMsg(this); // 将本Message添加到f的Message集合中
}

void Message::remove(Folder &f) {
	folders.erase(&f); // 将给定Folder从我们的Folder列表中删除
	f.remMsg(this); // 将本 Message 从 f 的 Message 集合中删除
}

// 将本Message添加到指向m的Folder中
void Message::add_to_Folders(const Message &m) {
	for (auto f : m.folders) { // 对每个包含m 的 Folder
		f->addMsg(this); // 向该Folder添加一个指向本Message的指针
	}
}

// 从对应的Folder中删除本Message
void Message::remove_from_Folders()
{
	for (auto f : folders) { // 对 folders 中每个指针
		f->remMsg(this); // 从该 Folder 中删除本 Message
	}
}

void swap(Message &lhs, Message &rhs){
	using std::swap; // 在本例中严格来说并不需要，但这是一个好习惯
	// 将每个消息的指针从它（原来）所在 Folder 中删除
	for (auto f : lhs.folders) {
		f->remMsg(&lhs);
	}
	for (auto f : rhs.folders) {
		f->remMsg(&rhs);
	}
	// 交换 contents 和 Folder 指针 set
	swap(lhs.folders, rhs.folders); // 使用swap (set&, set&)
	swap(lhs.contents, rhs.contents); // swap(string&, string&)
	// 将每个Message的指针添加到它的(新) Folder中
	for (auto f : lhs.folders) {
		f->addMsg(&lhs);
	}
	for (auto f : rhs.folders) {
		f->addMsg(&rhs);
	}
}
```
## 练习 13.35:
### 如果Message使用合成的拷贝控制成员,将会发生什么?
答：
* 拷贝、赋值和销毁Message对象时不会更新相关Folder的数据成员。
## 练习 13.36:
### 设计并实现对应的Folder类。此类应该保存一个指向Folder中包含的Message 的 set。
答：
```
class Folder {
	friend class Message;
public:
	Folder() = default;
	Folder(const Folder&);
	Folder& operator=(const Folder&);
	~Folder();
	void addMsg(Message* pMsg) { messages.insert(pMsg); }
	void remMsg(Message* pMsg) { messages.erase(pMsg); }
private:
	std::set<Message*> messages;
	void add_to_Message();
	void remove_to_Message();
};

Folder::Folder(const Folder& rhs):messages(rhs.messages) {
	add_to_Message();
}

Folder& Folder::operator=(const Folder& rhs) {
	remove_to_Message();
	messages = rhs.messages;
	add_to_Message();
	return *this;
}

Folder::~Folder() {
	remove_to_Message();
}

void Folder::add_to_Message() {
	for (auto m : messages) {
		m->addFol(this);
	}
}

void Folder::remove_to_Message(){
	for (auto m : messages) {
		m->remFol(this);
	}
}
```
## 练习 13.37:
### 为Message类添加成员,实现向 folders添加或删除一个给定的 Folder*。这两个成员类似 Folder 类的 addMsg 和 remMsg 操作。
答：
```
	void addFol(Folder* pFol) { folders.insert(pFol); }
	void remFol(Folder* pFol) { folders.erase(pFol); }
```
## 练习 13.38:
### 我们并未使用拷贝和交换方式来设计Message的赋值运算符。你认为其原因是什么？
答：
* 当前的swap函数实现过于耦合同时作用于Message和Folder对象，不适合用来实现Message的拷贝赋值运算符。
## 练习 13.39：
### 编写你自己版本的 strVec，包括自己版本的 reserve、capacity（参 见9.4节,第318页)和resize (参见9.3.5节,第314页)。
答：
```
#define _SCL_SECURE_NO_WARNINGS
#include <string>
#include <memory>

using namespace std;

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
	void reserve(size_t n);
	void resize(size_t n, const string& s);
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

void StrVec::reserve(size_t n) {
	if (n <= capacity()) return;  // 不需要重新分配
	auto newdata = alloc.allocate(n);
	auto dest = newdata;
	auto elem = elements;
	for (size_t i = 0; i != size(); ++i) {
		alloc.construct(dest++, move(*elem++));
	}
	free();
	elements = newdata;
	first_free = dest;
	cap = elements + n;
}

void StrVec::resize(size_t n, const string& s = string()) {
	if (n < size()) {
		while (first_free != elements + n) {
			alloc.destroy(--first_free);
		}
	} else if (n > size()) {
		while (size() != n) {
			push_back(s);
		}
	}
}
```
## 练习 13.40:
### 为你的StrVec类添加一个构造函数,它接受一个initializer_list <string>参数。
答：
```
class StrVec {
public:
    StrVec(initializer_list<string> il);
    // ... 其他成员和函数 ...
};

StrVec::StrVec(initializer_list<string> il) : elements(nullptr), first_free(nullptr), cap(nullptr) {
    for (const auto &s : il) {
        push_back(s);
    }
}
```
## 练习 13.41：
### 在 push_back 中，我们为什么在 construct 调用中使用前置递增运算？如果使用后置递增运算的话，会发生什么？
答：
* push_back 中使用的是后置递增运算符，如果换为前置递增运算会无法在first_free一开始指向的空间构造对象，并且会在动态分配的内存空间外构造对象。
## 练习 13.42：
### 在你的 TextQuery 和 QueryResult 类（参见 12.3 节，第 431 页）中用你的 StrVec 类代替 vector<string>，以此来测试你的 strVec 类。
答：
* 直接把vector<string>替换为StrVec即可。
## 练习 13.43:重写 free 成员,用 for_each 和 lambda (参见 10.3.2 节,第346 页)来 代替for循环destroy元素。你更倾向于哪种实现,为什么?
答：
```
for_each(elements, first_free, [this](std::string &s){ alloc.destroy(&s); });
```
* 都可以，for_each负责了迭代部分让代码更加简洁，不过原版对destroy的指针参数的由来更为直观。
## 练习 13.44：
### 编写标准库 string 类的简化版本，命名为 String。你的类应该至少有一个默认构造函数和一个接受C风格字符串指针参数的构造函数。使用allocator为你的 String 类分配所需内存。
答：
```
#define _SCL_SECURE_NO_WARNINGS
#include <memory>
#include <algorithm>
#include <iostream>

class String {
public:
	// 默认构造函数
	String() : String("") {}

	// 接受C风格字符串的构造函数
	String(const char* cstr) {
		auto data = alloc_n_copy(cstr, cstr + std::strlen(cstr)+1);
		elements = data.first;
		first_free = cap = data.second;
	}

	// 拷贝构造函数
	String(const String& s) {
		auto data = alloc_n_copy(s.begin(), s.end());
		elements = data.first;
		first_free = cap = data.second;
	}

	// 析构函数
	~String() {
		free();
	}

	// 拷贝赋值运算符
	String& operator=(const String& s) {
		auto data = alloc_n_copy(s.begin(), s.end());
		free();
		elements = data.first;
		first_free = cap = data.second;
		return *this;
	}

	// 工具函数
	char* begin() const { return elements; }
	char* end() const { return first_free; }

private:
	std::allocator<char> alloc;

	char* elements;
	char* first_free;
	char* cap;

	// 工具函数
	std::pair<char*, char*> alloc_n_copy(const char* b, const char* e) {
		auto data = alloc.allocate(e - b);
		return{ data, std::uninitialized_copy(b, e, data) };
	}

	void free() {
		if (elements) {
			for (auto p = first_free; p != elements; ) {
				alloc.destroy(--p);
			}
			alloc.deallocate(elements, cap - elements);
		}
	}
};

int main() {
	String s("Hello");
	std::cout << s.begin() << std::endl;

	String t = s;
	std::cout << t.begin() << std::endl;

	return 0;
}
```
## 练习 13.45:
### 解释右值引用和左值引用的区别。
答：
* 左值引用使用 & 绑定到左值上，而右值引用使用 && 绑定到右值上。
* 左值引用用于需要对象的别名或需要修改其状态的场景，而右值引用用于移动资源、优化临时对象的拷贝成本等场景。
## 练习 13.46：
### 什么类型的引用可以绑定到下面的初始化器上？
```
int f();
vector<int> vi(100);
int? r1 = f();
int? r2 = vi[0];
int? r3 = r1;
int? r4 = vi[0] * f();
```
答：
```
	int f();
	vector<int> vi(100);
	int && r1 = f();
	int & r2 = vi[0];
	int & r3 = r1;
	int && r4 = vi[0] * f();
```
## 练习 13.47:
### 对你在练习13.44 (13.5节,第470页)中定义的string类,为它的拷贝构造函数和拷贝赋值运算符添加一条语句，在每次函数执行时打印一条信息。
答：
```
	// 拷贝构造函数
	String(const String& s) {
		auto data = alloc_n_copy(s.begin(), s.end());
		elements = data.first;
		first_free = cap = data.second;
		std::cout << "String(const String& s)" << std::endl;
	}

	// 拷贝赋值运算符
	String& operator=(const String& s) {
		auto data = alloc_n_copy(s.begin(), s.end());
		free();
		elements = data.first;
		first_free = cap = data.second;
		std::cout << "String& operator=(const String& s)" << std::endl;
		return *this;
	}
```
## 练习 13.48：
### 定义一个 vector<String>并在其上多次调用 push_back。 运行你的程 序，并观察 String 被拷贝了多少次。
答：
```
int main() {
	String s("Hello");
	std::vector<String> vec;
	vec.push_back(s);
	vec.push_back("World");

	return 0;
}
```
## 练习 13.49:
### 为你的strVec、 String和Message类添加一个移动构造函数和一个移动赋值运算符。
答：
* Message:
```
#include <iostream>
#include <string>
#include <set>

using namespace std;

class Folder;

class Message {
	friend class Folder;
	friend void swap(Message &, Message &);
public:
	// folders被隐式初始化为空集合
	explicit Message(const std::string &str = "") : contents(str) { }
	// 拷贝控制成员,用来管理指向本Message的指针
	Message(const Message&); // 拷贝构造函数
	Message(Message &&m) noexcept; // 移动构造函数
	Message& operator=(const Message&); // 拷贝赋值运算符
	Message& operator=(Message &&rhs) noexcept; // 移动赋值运算符
	~Message(); // 析构函数
				// 从给定 Folder 集合中添加/删除本 Message
	void save(Folder&);
	void remove(Folder&);
	void addFol(Folder* pFol) { folders.insert(pFol); }
	void remFol(Folder* pFol) { folders.erase(pFol); }
private:
	std::string contents; // 实际消息文本
	std::set<Folder*> folders; // 包含本 Message 的 Folder
							   // 拷贝构造函数、拷贝赋值运算符和析构函数所使用的工具函数
							   // 将本Message添加到指向参数的Folder中
	void add_to_Folders(const Message&);
	// 从 folders 中的每个 Folder 中删除本 Message
	void remove_from_Folders();
};

class Folder {
	friend class Message;
public:
	Folder() = default;
	Folder(const Folder&);
	Folder(Folder &&f) noexcept;
	Folder& operator=(const Folder&);
	Folder& operator=(Folder &&rhs) noexcept;
	~Folder();
	void addMsg(Message* pMsg) { messages.insert(pMsg); }
	void remMsg(Message* pMsg) { messages.erase(pMsg); }
private:
	std::set<Message*> messages;
	void add_to_Message();
	void remove_to_Message();
};

Message::Message(const Message &m) : contents(m.contents), folders(m.folders) {
	add_to_Folders(m); // 将本消息添加到指向m的Folder中
}

Message& Message::operator=(const Message &rhs) {
	// 通过先删除指针再插入它们来处理自赋值情况
	remove_from_Folders(); // 更新已有 Folder
	contents = rhs.contents; // 从 rhs 拷贝消息内容
	folders = rhs.folders; // 从rhs拷贝Folder指针
	add_to_Folders(rhs); // 将本 Message 添加到那些 Folder 中
	return *this;
}

Message::~Message() {
	remove_from_Folders();
}

void Message::save(Folder &f) {
	folders.insert(&f); // 将给定Folder添加到我们的Folder列表中
	f.addMsg(this); // 将本Message添加到f的Message集合中
}

void Message::remove(Folder &f) {
	folders.erase(&f); // 将给定Folder从我们的Folder列表中删除
	f.remMsg(this); // 将本 Message 从 f 的 Message 集合中删除
}

// 将本Message添加到指向m的Folder中
void Message::add_to_Folders(const Message &m) {
	for (auto f : m.folders) { // 对每个包含m 的 Folder
		f->addMsg(this); // 向该Folder添加一个指向本Message的指针
	}
}

// 从对应的Folder中删除本Message
void Message::remove_from_Folders()
{
	for (auto f : folders) { // 对 folders 中每个指针
		f->remMsg(this); // 从该 Folder 中删除本 Message
	}
}

void swap(Message &lhs, Message &rhs) {
	using std::swap; // 在本例中严格来说并不需要，但这是一个好习惯
					 // 将每个消息的指针从它（原来）所在 Folder 中删除
	for (auto f : lhs.folders) {
		f->remMsg(&lhs);
	}
	for (auto f : rhs.folders) {
		f->remMsg(&rhs);
	}
	// 交换 contents 和 Folder 指针 set
	swap(lhs.folders, rhs.folders); // 使用swap (set&, set&)
	swap(lhs.contents, rhs.contents); // swap(string&, string&)
									  // 将每个Message的指针添加到它的(新) Folder中
	for (auto f : lhs.folders) {
		f->addMsg(&lhs);
	}
	for (auto f : rhs.folders) {
		f->addMsg(&rhs);
	}
}

Message::Message(Message &&m) noexcept
	: contents(std::move(m.contents)), folders(std::move(m.folders)) {
	// 更新 folders 中的每个 Folder 以指向新的 Message
	for (auto f : folders) {
		f->remMsg(&m);
		f->addMsg(this);
	}
	m.folders.clear(); // 确保移动后的源对象处于有效状态
}

Message& Message::operator=(Message &&rhs) noexcept {
	if (this != &rhs) { // 检查自赋值
		remove_from_Folders();
		contents = std::move(rhs.contents);
		folders = std::move(rhs.folders);
		for (auto f : folders) {
			f->remMsg(&rhs);
			f->addMsg(this);
		}
		rhs.folders.clear();
	}
	return *this;
}


Folder::Folder(const Folder& rhs) :messages(rhs.messages) {
	add_to_Message();
}

Folder& Folder::operator=(const Folder& rhs) {
	remove_to_Message();
	messages = rhs.messages;
	add_to_Message();
	return *this;
}

Folder::~Folder() {
	remove_to_Message();
}

void Folder::add_to_Message() {
	for (auto m : messages) {
		m->addFol(this);
	}
}

void Folder::remove_to_Message() {
	for (auto m : messages) {
		m->remFol(this);
	}
}

Folder::Folder(Folder &&f) noexcept
	: messages(std::move(f.messages)) {
	// 更新 messages 中的每个 Message 以指向新的 Folder
	for (auto m : messages) {
		m->remFol(&f);
		m->addFol(this);
	}
	f.messages.clear(); // 确保移动后的源对象处于有效状态
}

Folder& Folder::operator=(Folder &&rhs) noexcept {
	if (this != &rhs) { // 检查自赋值
		remove_to_Message();
		messages = std::move(rhs.messages);
		for (auto m : messages) {
			m->remFol(&rhs);
			m->addFol(this);
		}
		rhs.messages.clear();
	}
	return *this;
}
```
* strVec:
```
#define _SCL_SECURE_NO_WARNINGS
#include <string>
#include <memory>

using namespace std;

// 类似vector类内存分配策略的简化实现
class StrVec {
public:
	StrVec() : elements(nullptr), first_free(nullptr), cap(nullptr) {} //allocator 成员进行默认初始化
	StrVec(const StrVec&); // 拷贝构造函数
	StrVec(StrVec &&s) noexcept; // 移动构造函数
	StrVec &operator=(const StrVec&); // 拷贝赋值运算符
	StrVec& operator = (StrVec &&rhs) noexcept; // 拷移动赋值运算符
	~StrVec(); // 析构函数
	void push_back(const string&); // 拷贝元素
	size_t size() const { return first_free - elements; }
	size_t capacity() const { return cap - elements; }
	string *begin() const { return elements; }
	string *end() const { return first_free; }
	void reserve(size_t n);
	void resize(size_t n, const string& s);
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

void StrVec::reserve(size_t n) {
	if (n <= capacity()) return;  // 不需要重新分配
	auto newdata = alloc.allocate(n);
	auto dest = newdata;
	auto elem = elements;
	for (size_t i = 0; i != size(); ++i) {
		alloc.construct(dest++, move(*elem++));
	}
	free();
	elements = newdata;
	first_free = dest;
	cap = elements + n;
}

void StrVec::resize(size_t n, const string& s = string()) {
	if (n < size()) {
		while (first_free != elements + n) {
			alloc.destroy(--first_free);
		}
	} else if (n > size()) {
		while (size() != n) {
			push_back(s);
		}
	}
}

StrVec::StrVec(StrVec &&s) noexcept : elements(s.elements), first_free(s.first_free), cap(s.cap) {
	s.elements = s.first_free = s.cap = nullptr;
}

StrVec& StrVec::operator = (StrVec &&rhs) noexcept {
	if (this != &rhs) {
		free();
		elements = rhs.elements;
		first_free = rhs.first_free;
		cap = rhs.cap;
		rhs.elements = rhs.first_free = rhs.cap = nullptr;
	}
	return *this;
}
```
## 练习 13.50：
### 在你的 String 类的移动操作中添加打印语句，并重新运行 13.6.1 节 （第473 页)的练习 13.48 中的程序，它使用了一个 vector<String>，观察什么时候会避免拷贝。
答：
```
#define _SCL_SECURE_NO_WARNINGS
#include <memory>
#include <algorithm>
#include <iostream>
#include <vector>

class String {
public:
	// 默认构造函数
	String() : String("") {}

	// 接受C风格字符串的构造函数
	String(const char* cstr) {
		auto data = alloc_n_copy(cstr, cstr + std::strlen(cstr) + 1);
		elements = data.first;
		first_free = cap = data.second;
	}

	// 拷贝构造函数
	String(const String& s) {
		auto data = alloc_n_copy(s.begin(), s.end());
		elements = data.first;
		first_free = cap = data.second;
	}

	// 移动构造函数
	String(String &&s) noexcept : elements(s.elements), first_free(s.first_free), cap(s.cap) {
		s.elements = s.first_free = s.cap = nullptr; // 确保移动后的源对象是安全的
		std::cout << "Move Constructor Called" << std::endl;
	}

	// 析构函数
	~String() {
		free();
	}

	// 拷贝赋值运算符
	String& operator=(const String& s) {
		auto data = alloc_n_copy(s.begin(), s.end());
		free();
		elements = data.first;
		first_free = cap = data.second;
		return *this;
	}

	// 移动赋值运算符
	String& operator=(String &&rhs) noexcept {
		if (this != &rhs) {
			free(); // 释放现有的资源
			elements = rhs.elements;
			first_free = rhs.first_free;
			cap = rhs.cap;
			rhs.elements = rhs.first_free = rhs.cap = nullptr; // 使rhs安全
		}
		std::cout << "Move Assignment Operator Called" << std::endl;
		return *this;
	}

	// 工具函数
	char* begin() const { return elements; }
	char* end() const { return first_free; }

private:
	std::allocator<char> alloc;

	char* elements;
	char* first_free;
	char* cap;

	// 工具函数
	std::pair<char*, char*> alloc_n_copy(const char* b, const char* e) {
		auto data = alloc.allocate(e - b);
		return{ data, std::uninitialized_copy(b, e, data) };
	}

	void free() {
		if (elements) {
			for (auto p = first_free; p != elements; ) {
				alloc.destroy(--p);
			}
			alloc.deallocate(elements, cap - elements);
		}
	}
};


int main() {
	String s("Hello");
	std::vector<String> vec;
	vec.push_back(s);
	vec.push_back("World");

	system("pause");
	return 0;
}
```
## 练习 13.51:
### 虽然unique_ptr不能拷贝,但我们在12.1.5节(第418页)中编写了一个clone函数，它以值方式返回一个 unique_ptr。解释为什么函数是合法的，以及为什么它能正确工作。
答：
* 函数按值返回的对象是一个右值，对右值unique_ptr的拷贝将调用unique_ptr的移动构造函数。
## 练习 13.52:
### 详细解释第478 页中的 HasPtr 对象的赋值发生了什么?特别是,一步一步描述 hp、hp2以及HasPtr 的赋值运算符中的参数 rhs的值发生了什么变化。
答：
* hp = hp2;这一行代码进行了以下操作：
* 由于hp2是左值，调用operator=函数时，将使用拷贝构造函数为rhs创建一个新的HasPtr对象。rhs.ps现在指向一个新的string对象，该对象的内容与hp2.ps指向的对象相同。
* 赋值运算符使用swap函数交换*this（即hp）和rhs的内容。因此，hp的ps和rhs的ps交换了指针，所以现在hp.ps指向rhs.ps原先指向的那个string对象，而rhs.ps现在指向hp原来所指向的string对象。
* 函数结束时，rhs（拷贝的对象）的析构函数被调用，rhs.ps指向的string对象会被删除，但由于hp.ps和rhs.ps已经交换了，所以实际上是原hp的string对象被删除。
* hp = std::move(hp2);这一行代码进行了以下操作：
* std::move(hp2)将hp2转换为右值引用，这意味着可以从hp2中移走资源。赋值运算符使用移动构造函数为rhs创建一个新的HasPtr对象。因此，rhs.ps现在直接获取了hp2.ps的指针值，并且hp2.ps被设置为nullptr。
* 然后，同样使用swap函数交换*this（即hp）和rhs的内容。因此，hp.ps和rhs.ps交换了指针。这次，hp.ps将直接指向hp2原先所指向的string对象，而rhs.ps则获取了hp原来的指针。
* 函数结束时，rhs（移动的对象）的析构函数被调用，rhs.ps指向的string对象会被删除，但由于hp.ps和rhs.ps已经交换了，所以实际上是原hp的string对象被删除。
## 练习 13.53:
### 从底层效率的角度看, HasPtr的赋值运算符并不理想,解释为什么。为HasPtr实现一个拷贝赋值运算符和一个移动赋值运算符,并比较你的新的移动赋值运算符中执行的操作和拷贝并交换版本中执行的操作。
答：
* 临时对象的创建：每次调用operator=时，都会创建一个临时HasPtr对象作为函数参数rhs。这意味着进行了额外的内存分配和释放，增加了运行时间。
* 拷贝操作的开销：对于左值的情况，创建rhs的过程中会调用拷贝构造函数，从而执行一个完整的HasPtr拷贝操作。
* 交换操作的开销：在函数体内部，使用swap进行交换虽然本身开销不大，但仍增加了一些运行时间。
```
//拷贝赋值运算符
HasPtr& operator=(const HasPtr &rhs) {
    auto newp = new std::string(*rhs.ps); // 拷贝底层string
    delete ps; // 释放旧的内存
    ps = newp; // 更新ps指针
    i = rhs.i;
    return *this;
}
//移动赋值运算符
HasPtr& operator=(HasPtr &&rhs) noexcept {
    if (this != &rhs) {
        delete ps; // 释放当前对象的内存
        ps = rhs.ps; // 移动rhs的数据
        i = rhs.i;
        rhs.ps = nullptr; // 使rhs进入一个安全状态
    }
    return *this;
}
```
## 练习 13.54：
### 如果我们为 HasPtr 定义了移动赋值运算符，但未改变拷贝并交换运算符，会发生什么？编写代码验证你的答案。
答：
* 会使编译器会知道应该选择拷贝并交换的版本还是专门的移动赋值运算符，因此会产生“operator =”不明确的二义性编译错误。
