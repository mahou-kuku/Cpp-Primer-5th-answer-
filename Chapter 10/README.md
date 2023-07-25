## 练习 10.1：
### 头文件 algorithm 中定义了一个名为 count 的函数，它类似 find，接受一对迭代器和一个值作为参数。 count 返回给定值在序列中出现的次数。 编写程序，读取 int 序列存入 vector 中，打印有多少个元素的值等于给定值。
答：
```
#include <vector>
#include <iostream>
#include <algorithm>

int main() {
	std::vector<int> vec;
	int num;

	std::cout << "Enter a series of integers ending with any non-digit character:" << std::endl;
	while (std::cin >> num) {
		vec.push_back(num);
	}
	std::cin.clear(); // 清除输入流的错误标记
	std::cin.ignore(std::cin.rdbuf()->in_avail());//忽略掉输入流中剩余的字符

	std::cout << "Enter the integer you want to count:" << std::endl;
	int target;
	if (!(std::cin >> target)) {
		std::cout << "Invalid input, please enter an integer." << std::endl;
		return -1;
	}

	int count_target = std::count(vec.begin(), vec.end(), target);
	std::cout << "The number of elements equal to " << target << " is: " << count_target << std::endl;

	return 0;
}
```
## 练习 10.2:
### 重做上一题,但读取string序列存入list中。
答：
```
#include <list>
#include <iostream>
#include <string>
#include <algorithm>

int main() {
	std::list<std::string> li;
	std::string str;

	std::cout << "Enter a series of strings, ending at the end of the file:" << std::endl;
	while (std::cin >> str) {
		li.push_back(str);
	}
	std::cin.clear(); // 清除输入流的错误标记

	std::cout << "Enter the string you want to count:" << std::endl;
	std::string target;
	std::cin >> target;

	int count_target = std::count(li.begin(), li.end(), target);
	std::cout << "The number of elements equal to " << target << " is: " << count_target << std::endl;

	return 0;
}
```
## 练习 10.3： 
### 用 accumulate 求一个 vector<int>中的元素之和。
答：
```
#include <iostream>
#include <vector>
#include <numeric>

using namespace std;

int main() {
	vector<int> vec{ 0,1,2,3,4,5 };

	auto sum = accumulate(vec.cbegin(), vec.cend(), 0);
	cout << sum;

	return 0;
}
```
## 练习 10.4：
### 假定v是一个 vector<double>，那么调用 accumulate (v.cbegin (), v.cend()，0)有何错误(如果存在的话)？
答：
* 0是一个int类型的字面值，它使得std::accumulate在进行累加计算时，将所有的double类型的数值都转换成int进行运算。这会导致两个问题：
* 可能丢失所有的小数部分，因为int类型不能保存小数。
* 如果vector中的double值过大，它可能超出int能表示的范围，从而导致溢出。
## 练习 10.5：
### 在本节对名册（roster）调用 equal 的例子中，如果两个名册中保存的都是 c 风格字符串而不是 string，会发生什么？
答：
* 函数的行为会变得与预期不同。因为C风格字符串是指针类型，所以equal函数会比较两个指针的值，而不是指向的字符串内容。
## 练习 10.6：
### 编写程序，使用 fill_n 将一个序列中的 int 值都设置为 0。
答：
```
#include <vector>
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
	vector<int> vec{ 0,1,2,3,4,5 };
	fill_n(vec.begin(),vec.size(), 0);

	for (const auto &i : vec) {
		cout << i << endl;
	}
	return 0;
}
```
## 练习 10.7：
### 下面程序是否有错误？如果有，请改正。
```
(a)
vector<int> vec; list<int> lst; int i;
while (cin >> i)
	lst.push_back(i);
copy(lst.cbegin(), lst.cend(), vec.begin());
(b)
vector<int> vec;
vec.reserve(10); 
fill_n(vec.begin(), 10, 0);
```
答：
```
(a)
	vector<int> vec; list<int> lst; int i;
	while (cin >> i) {
		lst.push_back(i);
	}
	vec.resize(lst.size());
	copy(lst.cbegin(), lst.cend(), vec.begin());
(b)
	vector<int> vec;
	vec.resize(10);
	fill_n(vec.begin(), 10, 0);
```
## 练习 10.8：
### 本节提到过，标准库算法不会改变它们所操作的容器的大小。为什么使用 back _inserter 不会使这一断言失效？
答：
* 算法本身并没执行任何能改变容器大小的操作，这些可能导致容器大小改变的操作实际上是在迭代器层次上通过特殊设计的迭代器完成的。
