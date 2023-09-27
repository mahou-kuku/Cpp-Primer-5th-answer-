## 练习 13.1：
### 拷贝构造函数是什么？什么时候使用它?
答：
* 当一个对象用另一个同类型的对象初始化时。
* 当一个函数的参数是类的对象，并通过值传递。
* 当函数返回一个类的对象。
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
		if (this != &rhs) {  // 检查自我赋值
			delete ps;	// 删除当前字符串
			ps = new std::string(*rhs.ps);	// 从rhs分配新字符串
			i = rhs.i;
		}
		return *this;
	}
private:
	std::string *ps;
	int i;
};
```
