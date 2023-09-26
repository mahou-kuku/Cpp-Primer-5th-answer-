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
