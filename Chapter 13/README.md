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
