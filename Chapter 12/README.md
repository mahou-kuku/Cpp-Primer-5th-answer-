## 练习12.1：
### 在此代码的结尾， b1 和 b2 各包含多少个元素？
```
StrBlob b1;
{
 StrBlob b2 = {"a", "an", "the"};
 b1 = b2;
 b2.push_back("about");
}
```
答：
* b1 包含4个元素，b2 离开作用域后已经被销毁。
## 练习 12.2:
### 编写你自己的 StrBlob 类，包含 const 版本的 front 和 back。
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <initializer_list>
#include <memory>

using namespace std;

class StrBlob {
public:
	typedef vector<string>::size_type size_type;

	StrBlob() : data(make_shared<vector<string>>()) { }
	StrBlob(initializer_list<string> il) : data(make_shared<vector<string>>(il)) { }
	size_type size() const { return data->size(); }
	bool empty() const { return data->empty(); }
	// 添加和删除元素
	void push_back(const string &t) { data->push_back(t); }
	void pop_back() {
		check(0, "pop_back on empty StrBlob");
		data->pop_back();
	}
	// 元素访问
	string& front() {
		// 如果 vector 为空，check 会抛出一个异常
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back() {
		check(0, "back on empty StrBlob");
		return data->back();
	}
	string& front()const {
		check(0, "front on empty StrBlob");
		return data->front();
	}
	string& back()const {
		check(0, "back on empty StrBlob");
		return data->back();
	}
private:
	shared_ptr<vector<string>> data;
	// 如果 data[i]不合法，抛出一个异常
	void check(size_type i, const string &msg) const {
		if (i >= data->size()) {
			throw out_of_range(msg);
		}
	}
};

int main() {
	StrBlob b1;
	{
		StrBlob b2 = { "a", "an", "the" };
		b1 = b2;
		b2.push_back("about");
	}
	cout << b1.back() << endl;

	return 0;
}
```
## 练习 12.3:
### StrBlob需要const版本的push_back和pop_back吗?如果需要,添加进去。 否则，解释为什么不需要。
答：
* 从语法和编译角度，可以把push_back和pop_back写成const版本，因为这些操作并不修改shared_ptr对象本身。但从设计和逻辑的角度看，这样的做法是违反直觉的。
* 虽然shared_ptr的内部指针值没有变化，但它所指向的vector的内容确实被修改了。这意味着StrBlob对象的逻辑状态已经改变。所以，从设计的角度来看，给push_back和pop_back添加const标识会导致误导。
## 练习 12.4:
### 在我们的check函数中,没有检查i是否大于0。为什么可以忽略这个检查？
答：
* std::vector<std::string>::size_type 是无符号整型，取值范围是0和正数，不可能出现负数。
## 练习 12.5：
### 我们未编写接受一个 initializer_list explicit （参见 7.5.4 节，第 264页)参数的构造函数。讨论这个设计策略的优点和缺点。
答：
* 优点：
可以直接传递初始化列表给接受StrBlob参数的函数，由于StrBlob基本上是一个对字符串vector的封装，所以允许直接使用初始化列表来构造这样一个对象是直观的。
* 缺点：
可能导致意外的类型转换，由于该构造函数不是explicit的，编译器可能会在我们不期望的情况下隐式地进行类型转换。这可能会导致一些意料之外的行为。
## 练习 12.6:
### 编写函数,返回一个动态分配的int的vector。将此vector传递给另一个函数,这个函数读取标准输入，将读入的值保存在vector元素中。再将vector 传递给另一个函数，打印读入的值。记得在恰当的时刻 delete vector。
答：
```
#include <iostream>
#include <vector>

// 创建并返回一个动态分配的vector
std::vector<int>* create_vector() {
	return new std::vector<int>();
}

// 从标准输入读取int值并存储到vector中
void read_values(std::vector<int> *vec) {
	int val;
	while (std::cin >> val) {
		vec->push_back(val);
	}
}

// 打印vector中的所有int值
void print_values(std::vector<int> *vec) {
	for (const auto &val : *vec) {
		std::cout << val << " ";
	}
	std::cout << std::endl;
}

int main() {
	auto vec_ptr = create_vector();
	read_values(vec_ptr);
	print_values(vec_ptr);

	delete vec_ptr;
	return 0;
}
```
## 练习 12.7：
### 重做上一题，这次使用 shared_ptr 而不是内置指针。
答：
```
#include <iostream>
#include <vector>
#include <memory>

// 创建并返回一个动态分配的vector
std::shared_ptr<std::vector<int>> create_vector() {
	return std::make_shared<std::vector<int>>();
}

// 从标准输入读取int值并存储到vector中
void read_values(std::shared_ptr<std::vector<int>> vec) {
	int val;
	while (std::cin >> val) {
		vec->push_back(val);
	}
}

// 打印vector中的所有int值
void print_values(const std::shared_ptr<std::vector<int>> &vec) {
	for (const auto &val : *vec) {
		std::cout << val << " ";
	}
	std::cout << std::endl;
}

int main() {
	auto vec_ptr = create_vector();
	read_values(vec_ptr);
	print_values(vec_ptr);

	// 使用shared_ptr，不需要显式地delete vector
	return 0;
}
```
## 练习 12.8:
### 下面的函数是否有错误?如果有,解释错误原因。
```
bool b() {
  int* p = new int;
  // ...
  return p;
}
```
答：
* 这个函数的问题在于它分配了内存，但却没有释放这块内存。并且由于返回的是一个布尔值，调用者也无法访问和释放这块内存。因此，这会导致内存泄漏。
## 练习 12.9:
### 解释下面代码执行的结果:
```
int *q = new int(42), *r = new int(100);
r = q;
auto q2 = make_shared<int>(42), r2 = make_shared<int>(100);
r2 = q2;
```
答：
* 使用内置指针时，r = q;导致内存泄漏。
* 使用shared_ptr时，r2 = q2;没有导致内存泄漏，因为智能指针会自动管理内存。
