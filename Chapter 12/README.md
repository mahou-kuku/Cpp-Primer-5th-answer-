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
