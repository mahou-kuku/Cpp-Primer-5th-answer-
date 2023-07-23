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
