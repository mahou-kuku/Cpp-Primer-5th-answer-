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
## 练习 10.9：
### 实现你自己的 elimDups。 测试你的程序，分别在读取输入后、调用 unique 后以及调用 erase后打印 vector的内容。
答：
```
#include <vector>
#include <iostream>
#include <string>
#include <algorithm>

using namespace std;

void elimDups(vector<string> &words)
{
	cout << "Container size after passed: " << words.size() << endl;
	for (const auto &i : words) {
		cout << "Output element: " << i << endl;
	}

	sort(words.begin(), words.end());
	auto end_unique = unique(words.begin(), words.end());
	cout << "Container size after calling unique: " << words.size() << endl;
	for (const auto &i : words) {
		cout << "Output element: " << i << endl;
	}

	words.erase(end_unique, words.end());
	cout << "Container size after calling erase: " << words.size() << endl;
	for (const auto &i : words) {
		cout << "Output element: " << i << endl;
	}
}

int main() {
	vector<string> vs{ "aaa","bbb","ccc","aaa","bbb" };
	elimDups(vs);

	return 0;
}
```
## 练习 10.10:
### 你认为算法不改变容器大小的原因是什么?
答：
* 通用性和复用性: 如果算法能够改变容器大小，那么它们将不再是通用的，因为不是所有容器都支持有效的大小更改。
* 迭代器失效问题: 许多算法通过迭代器进行工作。改变容器大小可能会导致迭代器失效。通过确保算法不更改容器大小，可以减轻这个问题。
* 明确职责: 通过将元素访问和修改与容器大小管理分离，使代码的职责更加明确。算法主要关注元素如何处理，而容器主要关注元素如何组织和存储。
* 性能考虑: 改变容器大小可能是昂贵的操作，特别是对于需要重新分配内存的容器。通过确保算法不更改大小，可以避免由此引起的性能问题，挽救算法设计者的头发。
## 练习 10.11:
### 编写程序,使用stable_sort和isShorter将传递给你的elimDups 版本的vector排序。打印vector的内容,验证你的程序的正确性。
答：
```
#include <vector>
#include <iostream>
#include <string>
#include <algorithm>

using namespace std;

bool isShorter(const string &s1, const string &s2) {
	return s1.size() < s2.size();
}

void elimDups(vector<string> &words)
{
	cout << "After passed: \t\t\t\t";
	for (const auto &i : words) {
		cout << i << " ";
	}
	cout << endl;

	sort(words.begin(), words.end());
	cout << "After calling sort: \t\t\t";
	for (const auto &i : words) {
		cout << i << " ";
	}
	cout << endl;

	stable_sort(words.begin(), words.end(), isShorter);
	cout << "After calling isShorter and stable_sort: ";
	for (const auto &i : words) {
		cout << i << " ";
	}
	cout << endl;
}

int main() {
	vector<string> vs{ "ccc","aaa","aab","ab","ccc" };
	elimDups(vs);

	return 0;
}

```
## 练习 10.12：
### 编写名为 compareIsbn 的函数，比较两个 Sales_data 对象的 isbn () 成员。使用这个函数排序一个保存 Sales_data 对象的 vector。
答：
```
#include <vector>
#include <iostream>
#include <string>
#include <algorithm>
#include "Sales_data.h"

using namespace std;

bool compareIsbn(const Sales_data &s1, const Sales_data &s2) {
	return s1.isbn() < s2.isbn();
}

int main() {
	Sales_data data_1("aaa"), data_2("ab"), data_3("abc"), data_4("aba");
	vector<Sales_data> words{ data_1,data_2 ,data_3 ,data_4 };

	sort(words.begin(), words.end(), compareIsbn);

	for (const auto &i : words) {
		cout << i.isbn() << " ";
	}
	cout << endl;

	return 0;
}
```
#  练习 10.13：
### 标准库定义了名为 partition 的算法，它接受一个谓词，对容器内容进行划分,使得谓词为true的值会排在容器的前半部分,而使谓词为false的值会排在后半部分。算法返回一个迭代器，指向最后一个使谓词为 true的元素之后的位置。编写函数,接受一个string,返回一个 bool值,指出 string是否有5个或更多字符。使用此函数划分 words。打印出长度大于等于 5 的元素。
答:
```
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>

bool isFiveOrMore(const std::string &s) {
    return s.size() >= 5;
}

int main() {
    std::vector<std::string> words = {"apple", "banana", "cherry", "dog", "elephant", "fox"};

    auto it = std::partition(words.begin(), words.end(), isFiveOrMore);

    std::cout << "Words that are five or more characters long:\n";
    for (auto i = words.begin(); i != it; ++i) {
        std::cout << *i << '\n';
    }

    return 0;
}
```
## 练习 10.14:
### 编写一个lambda,接受两个int,返回它们的和。
答：
```
auto lamb = [](int number1, int number2) {return number1 + number2; };
```
## 练习 10.15:
### 编写一个lambda,捕获它所在函数的int,并接受一个int参数。lambda应该返回捕获的 int 和 int 参数的和。
答：
```
	int number2 = 9;
	auto lamb = [number2](int number1 ) {return number1 + number2; };
```
## 练习 10.16：
### 使用 lambda 编写你自己版本的 biggies。
答：
```
void mybiggies(vector<string> &words, vector<string>::size_type sz)
{
	elimDups(words);
	stable_sort(words.begin(), words.end(), [](const string &a, const string &b) { return a.size() < b.size(); });
	auto wc = find_if(words.begin(), words.end(), [sz](const string &a) { return a.size() >= sz; });
	auto count = words.end() - wc;
	cout << count << " " << make_plural(count, "word", "s") << " of length " << sz << " or longer" << endl;
	for_each(wc, words.end(), [](const string &s) {cout << s << " "; });
	cout << endl;
}
```
## 练习 10.17：
### 重写 10.3.1 节练习 10.12（第 345 页）的程序，在对 sort 的调用中使用 lambda来代替函数 compareIsbn。
答：
```
#include <vector>
#include <iostream>
#include <string>
#include <algorithm>
#include "Sales_data.h"

using namespace std;

int main() {
	Sales_data data_1("aaa"), data_2("ab"), data_3("abc"), data_4("aba");
	vector<Sales_data> words{ data_1,data_2 ,data_3 ,data_4 };

	sort(words.begin(), words.end(),
		[](const Sales_data &s1, const Sales_data &s2) {return s1.isbn() < s2.isbn(); });

	for (const auto &i : words) {
		cout << i.isbn() << " ";
	}
	cout << endl;

	return 0;
}
```
## 练习 10.18： 
### 重写 biggies，用 partition 代替 find_if。我们在 10.3.1 节练习 10.13 (第345页)中介绍了partition算法。
答：
```
void mybiggies(vector<string> &words, vector<string>::size_type sz)
{
	elimDups(words);
	stable_sort(words.begin(), words.end(), [](const string &a, const string &b) { return a.size() < b.size(); });
	auto wc = partition(words.begin(), words.end(), [sz](const string &a) { return a.size() >= sz; });
	auto count = wc - words.begin();
	cout << count << " " << make_plural(count, "word", "s") << " of length " << sz << " or longer" << endl;
	for_each(words.begin(), wc, [](const string &s) {cout << s << " "; });
	cout << endl;
}
```
## 练习 10.19:
### 用stable_partition重写前一题的程序,与stable_sort类似,在划分后的序列中维持原有元素的顺序。
答：
```
void mybiggies(vector<string> &words, vector<string>::size_type sz)
{
	elimDups(words);
	stable_sort(words.begin(), words.end(), [](const string &a, const string &b) { return a.size() < b.size(); });
	auto wc = stable_partition(words.begin(), words.end(), [sz](const string &a) { return a.size() >= sz; });
	auto count = wc - words.begin();
	cout << count << " " << make_plural(count, "word", "s") << " of length " << sz << " or longer" << endl;
	for_each(words.begin(), wc, [](const string &s) {cout << s << " "; });
	cout << endl;
}
```
## 练习 10.20:
### 标准库定义了一个名为count_if的算法。类似find_if,此函数接受一对迭代器,表示一个输入范围,还接受一个谓词,会对输入范围中每个元素执行。count_if 返回一个计数值，表示谓词有多少次为真。使用 count_if 重写我们程序中统计有多少单词长度超过6的部分。
答：
```
void mybiggies(vector<string> &words, vector<string>::size_type sz = 6)
{
	auto count = count_if(words.begin(), words.end(), [sz](const string &a) { return a.size() >= sz; });
	cout << count << " " << make_plural(count, "word", "s") << " of length " << sz << " or longer" << endl;
}
```
## 练习 10.21:
### 编写一个lambda，捕获一个局部 int变量，并递减变量值，直至它变为0.一旦变量变为 0，再调用lambda应该不再递减变量。lambda应该返回一个bool 值，指出捕获的变量是否为 0。
答：
```
#include <iostream>

int main() {
    int value = 10;  // 局部变量初始化为10

    auto decrement_until_zero = [&value]() -> bool {
        if(value > 0) {
            --value;
        }
        return value == 0;
    };

    // 测试该lambda
    while (true) {
        if (decrement_until_zero()) {
            std::cout << "Value is now zero!\n";
            break;
        } else {
            std::cout << "Value is " << value << '\n';
        }
    }

    return 0;
}
```
## 练习 10.22:
### 重写统计长度小于等于6的单词数量的程序,使用函数代替lambda.
答：
```
#include <vector>
#include <iostream>
#include <string>
#include <algorithm>
#include <functional>

using namespace std;
using namespace std::placeholders;

bool lessThen7(const std::string &s, string::size_type sz){
	return s.size() < sz;
}

int main() {
	vector<string> words = { "aaaa","bbbbb","cccccc","ddddddd" };
	string::size_type sz= 7;

	cout << count_if(words.begin(), words.end(), bind(lessThen7, _1, sz));

	return 0;
}
```
## 练习 10.23：
### bind 接受几个参数？
答：
* bind 接受一个可调用对象参数，外加该可调用对象所需要的参数数量。
* 调用bind生成的函数对象时需要至少提供占位符的数字所示的参数数量，多余的参数以及位置没有被占位符数字指定的参数都会被忽略。
## 练习 10.24: 
### 给定一个 string，使用 bind 和 check_size 在一个 int 的 vector 中查找第一个大于 string长度的值。
答：
```
#include <vector>
#include <string>
#include <algorithm>
#include <functional>
#include <iostream>

using namespace std;
using namespace std::placeholders;

bool check_size(const string &s, string::size_type sz){
    return s.size() < sz;
}

int main() {
    string s = "Hello";
    vector<int> vec = {1, 2, 3, 4, 5, 6, 7};

    auto it = std::find_if(vec.begin(), vec.end(), bind(check_size, s, _1));

    if (it != vec.end())
        cout << "The first integer larger than string length is: " << *it << endl;
    else
        cout << "No such integer found." << endl;

    return 0;
}
```
## 练习 10.25：
### 在 10.3.2 节（第 349 页）的练习中，编写了一个使用 partition 的 biggies 版本。使用 check_size 和 bind 重写此函数。
答：
```
bool check_size(const string &s, string::size_type sz) {
	return s.size() >= sz;
}

void mybiggies(vector<string> &words, vector<string>::size_type sz){
	elimDups(words);
	stable_sort(words.begin(), words.end(), [](const string &a, const string &b) { return a.size() < b.size(); });
	auto wc = partition(words.begin(), words.end(), bind(check_size,_1,sz));
	auto count = wc - words.begin();
	cout << count << " " << make_plural(count, "word", "s") << " of length " << sz << " or longer" << endl;
	for_each(words.begin(), wc, [](const string &s) {cout << s << " "; });
	cout << endl;
}
```
## 练习 10.26: 
### 解释三种插入迭代器的不同之处。
答：
* back_inserter 在容器的末尾添加元素。
* front_inserter 在容器的开始添加元素。
* inserter 在容器的指定的位置之前添加元素。
## 练习 10.27:
### 除了unique (参见10.2.3节,第343页)之外,标准库还定义了名为 unique_copy 的函数，它接受第三个迭代器，表示拷贝不重复元素的目的位置。 编写一个程序，使用 unique_copy 将一个 vector 中不重复的元素拷贝到一个初始为空的 list 中。
答：
```
#include <iostream>
#include <vector>
#include <list>
#include <algorithm>
#include <iterator>

int main() {
    // 初始化 vector
    std::vector<int> vec = {1, 1, 2, 3, 4, 4, 4, 5, 6, 6, 7};

    // 排序 vector（如果原始数据不是已排序的）
    std::sort(vec.begin(), vec.end());

    // 初始为空的 list
    std::list<int> lst;

    // 使用 unique_copy
    std::unique_copy(vec.begin(), vec.end(), std::back_inserter(lst));

    // 打印 list 的内容
    for (const int &i : lst) {
        std::cout << i << " ";
    }

    return 0;
}
```
## 练习 10.28:
### 一个vector中保存1到9,将其拷贝到三个其他容器中。分别使用inserter, back_inserter和front_inserter将元素添加到三个容器中。对每种 inserter，估计输出序列是怎样的，运行程序验证你的估计是否正确。
答：
```
#include <iostream>
#include <vector>
#include <list>
#include <iterator>
#include <algorithm>

int main() {
    // 初始化vector
    std::vector<int> vec = {1, 2, 3, 4, 5, 6, 7, 8, 9};

    // 使用inserter插入到list中
    std::list<int> list1;
    std::copy(vec.begin(), vec.end(), std::inserter(list1, list1.begin()));

    // 使用back_inserter插入到vector中
    std::vector<int> vec2;
    std::copy(vec.begin(), vec.end(), std::back_inserter(vec2));

    // 使用front_inserter插入到list中（元素的顺序会被颠倒）
    std::list<int> list3;
    std::copy(vec.begin(), vec.end(), std::front_inserter(list3));

    // 打印结果
    std::cout << "list1: ";
    for (const int &i : list1) {
        std::cout << i << " ";
    }
    std::cout << "\nvec2: ";
    for (const int &i : vec2) {
        std::cout << i << " ";
    }
    std::cout << "\nlist3: ";
    for (const int &i : list3) {
        std::cout << i << " ";
    }

    return 0;
}
```
## 练习 10.29:
### 编写程序，使用流迭代器读取一个文本文件，存入一个vector 中的string里。
答：
```
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <iterator>

int main() {
	std::ifstream inFile("textfile.txt");
	if (!inFile) {
		std::cerr << "Can not open the file!" << std::endl;
		return 1;
	}

	std::istream_iterator<std::string> fileIter(inFile), eof;
	std::vector<std::string> words(fileIter, eof);

	for (const auto &word : words) {
		std::cout << word << " ";
	}
	std::cout << std::endl;

	return 0;
}
```
## 练习 10.30：
### 使用流迭代器、sort 和 copy 从标准输入读取一个整数序列，将其排序， 并将结果写到标准输出。
答：
```
#include <iostream>
#include <iterator>
#include <vector>
#include <algorithm>

int main() {
	std::istream_iterator<int> in_iter(std::cin), in_eof;
	std::vector<int> numbers(in_iter, in_eof);

	std::sort(numbers.begin(), numbers.end());

	std::ostream_iterator<int> out_iter(std::cout, " ");
	std::copy(numbers.begin(), numbers.end(), out_iter);
	std::cout << std::endl;

	return 0;
}
```
## 练习 10.31：
### 修改前一题的程序，使其只打印不重复的元素。你的程序应使用 unique_copy (参见 10.4.1 节，第 359 页)。
答：
```
#include <iostream>
#include <iterator>
#include <vector>
#include <algorithm>

int main() {
	std::istream_iterator<int> in_iter(std::cin), in_eof;
	std::vector<int> numbers(in_iter, in_eof);
	std::vector<int> uniqueNumbers;

	std::sort(numbers.begin(), numbers.end());
	std::unique_copy(numbers.begin(), numbers.end(), std::back_inserter(uniqueNumbers));

	std::ostream_iterator<int> out_iter(std::cout, " ");
	std::copy(uniqueNumbers.begin(), uniqueNumbers.end(), out_iter);
	std::cout << std::endl;

	return 0;
}
```
## 练习 10.32:
### 重写1.6节(第21页)中的书店程序,使用一个vector保存交易记录, 使用不同算法完成处理。使用 sort 和 10.3.1 节（第 345 页）中的 compareIsbn 函数 来排序交易记录，然后使用 find 和 accumulate 求和。
答：
```
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
#include <iterator>
#include "Sales_item.h"

bool compareISBN(const Sales_item &s1, const Sales_item &s2) {
	return s1.isbn() < s2.isbn();
}

int main()
{
	std::istream_iterator<Sales_item> item_iter(std::cin), eof;
	std::vector<Sales_item> vec;

	if (item_iter != eof) {
		while (item_iter != eof) {
			vec.push_back(*item_iter++);
		}
		std::sort(vec.begin(), vec.end(), compareISBN);

		for (auto it = vec.begin(); it != vec.end();) {
			auto next = std::find_if(it, vec.end(), [it](Sales_item &item) {return it->isbn() != item.isbn(); });
			auto total = std::accumulate(it, next, Sales_item(it->isbn()));
			std::cout << total << std::endl;
			it = next;
		}

	} else {
		std::cerr << "No data?!" << std::endl;
		return -1;
	}
	return 0;
}
```
## 练习 10.33:
### 编写程序，接受三个参数：一个输入文件和两个输出文件的文件名。 输入文件保存的应该是整数。 使用istream_iterator读取输入文件。 使用ostream_iterator将奇数写入第一个输出文件,每个值之后都跟一个空格。 将偶数写入第二个输出文件，每个值都独占一行。
答：
```
#include <iostream>
#include <fstream>
#include <iterator>
#include <algorithm>

int main(int argc, char **argv) {
	if (argc != 4) {
		std::cerr << "Usage: " << argv[0] << " <inputfile> <outputfile_odd> <outputfile_even>" << std::endl;
		return 1;
	}

	std::ifstream input(argv[1]);
	if (!input) {
		std::cerr << "Failed to open input file: " << argv[1] << std::endl;
		return 1;
	}

	std::ofstream output_odd(argv[2]);
	if (!output_odd) {
		std::cerr << "Failed to open output file for odd numbers: " << argv[2] << std::endl;
		return 1;
	}

	std::ofstream output_even(argv[3]);
	if (!output_even) {
		std::cerr << "Failed to open output file for even numbers: " << argv[3] << std::endl;
		return 1;
	}

	std::istream_iterator<int> in_iter(input), eof;
	std::ostream_iterator<int> out_odd(output_odd, " ");
	std::ostream_iterator<int> out_even(output_even, "\n");

	std::for_each(in_iter, eof, [&](int i) {
		if (i % 2) {
			*out_odd++ = i;
		} else {
			*out_even++ = i;
		}
	});

	return 0;
}
```
## 练习 10.34:
### 使用 reverse_iterator逆序打印一个 vector。
答：
```
#include <iostream>
#include <vector>

using namespace std;

int main() {
	std::vector<int> vec = { 1, 2, 3, 4, 5 };

	for (auto rIt = vec.crbegin(); rIt != vec.crend(); ++rIt) {
		cout << *rIt << endl;
	}

	return 0;
}
```
## 练习 10.35：
### 使用普通迭代器逆序打印一个 vector。
答：
```
#include <iostream>
#include <vector>

using namespace std;

int main() {
	std::vector<int> vec = { 1, 2, 3, 4, 5 };

	for (auto it = vec.cend(); it != vec.cbegin();) {
		--it;
		cout << *it << endl;
	}

	return 0;
}
```
## 练习 10.36：
### 使用 find 在一个 int 的 list 中查找最后一个值为 0 的元素。
答：
```
#include <iostream>
#include <list>
#include <algorithm>

int main() {
	std::list<int> ilist = { 1,2,3,0,4,5,0,6,7 };

	auto iter = std::find(ilist.rbegin(), ilist.rend(), 0);

	if (iter != ilist.rend()) {
		std::cout << "The last 0 is at position: "
			<< std::distance(ilist.begin(), --(iter.base()))
			<< std::endl;
	} else {
		std::cout << "0 is not found in the list." << std::endl;
	}

	return 0;
}
```
## 练习 10.37：
### 给定一个包含 10 个元素的 vector，将位置 3 到 7 之间的元素按逆序拷贝到一个 list 中。
答：
```
#include <iostream>
#include <vector>
#include <list>

int main() {
	std::vector<int> vec = { 0,1,2,3,4,5,6,7,8,9 };
	std::list<int> lst(vec.begin() + 3, vec.begin() + 8);

	lst.reverse();  // 逆序list

	for (const auto &num : lst) {
		std::cout << num << " ";
	}

	return 0;
}
```
## 练习 10.38:
### 列出5个迭代器类别,以及每类迭代器所支持的操作。
答：
* 输入迭代器		只读，不写；单遍扫描，只能递增
* 输出迭代器		只写，不读；单遍扫描，只能递增
* 前向迭代器		可读写；多遍扫描，只能递增
* 双向迭代器		可读写；多遍扫描，可递增递减
* 随机访问迭代器		可读写，多遍扫描,支持全部迭代器运算
## 练习 10.39:
### list上的迭代器属于哪类? vector呢?
答：
list的迭代器属于双向迭代器,vector上的迭代器属于随机访问迭代器。
## 练习 10.40：
### 你认为 copy 要求哪类迭代器？reverse 和 unique 呢？
答：
* copy 需要输入迭代器和输出迭代器。
* reverse 需要双向迭代器。
* unique 需要前向迭代器。
## 练习 10.41：
### 仅根据算法和参数的名字，描述下面每个标准库算法执行什么操作：
```
replace(beg, end, old_val, new_val);
replace_if(beg, end, pred, new_val);
replace_copy(beg, end, dest, old_val, new_val);
replace_copy_if(beg, end, dest, pred, new_val);
```
答：
* replace(beg, end, old_val, new_val);
* 功能：在序列的指定范围[beg, end)内，将所有与old_val相等的元素替换为new_val。
* replace_if(beg, end, pred, new_val);
* 功能：在序列的指定范围[beg, end)内，将所有使一元谓词pred返回true的元素替换为new_val。
* replace_copy(beg, end, dest, old_val, new_val);
* 功能：对序列指定范围[beg, end)内的每个元素，如果该元素等于old_val，则将new_val复制到目标序列dest（开始位置由dest指定）；否则，直接复制原始元素。原始序列保持不变。
* replace_copy_if(beg, end, dest, pred, new_val);
* 功能：对序列指定范围[beg, end)内的每个元素，如果该元素使一元谓词pred返回true，则将new_val复制到目标序列dest ；否则，直接复制原始元素。原始序列保持不变。
## 练习 10.42:
### 使用list代替vector重新实现10.2.3节(第343页)中的去除重复单词的程序。
答：
```
void elimDups(list<string>  &words) {
	words.sort();
	words.unique();
}
```
