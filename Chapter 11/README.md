## 练习 11.1：
### 描述 map 和 vector 的不同。
答：
* 基本结构:
* map 是一个关联容器，它存储的是键值对（key-value pairs）。在一个 map 中，每个键都是唯一的，与之关联的是一个值。
* vector 是一个顺序容器，它是一个动态数组，只存储元素值。
* 存储特性:
* map 根据键自动为元素排序（默认情况下是用 < 操作符）。
* vector 按照元素插入的顺序存储元素。
* 下标操作:
* 在 map 中，可以使用 [] 下标操作符来访问与给定键关联的值。如果这个键不存在，它会插入这个键并默认初始化关联的值。
* 在 vector 中，[] 下标操作符用于访问指定位置的元素。但是，如果访问的位置超出了 vector 的大小，这将是未定义的行为。
## 练习 11.2:
### 分别给出最适合使用list、vector、deque、map以及set的例子。
答：
* list (双向链表)
* 当需要频繁地在序列的中间位置进行插入和删除操作时。
* 示例：维护一个活跃用户列表。当用户活跃时，将其添加到列表的末尾，而当用户不再活跃时，需要从列表中间位置移除用户。这种场景中，list 的插入和删除效率很高。
* vector (动态数组)
* 需要频繁地通过索引访问元素，但插入和删除操作主要发生在末尾时。
* 示例：用于存储图像像素值的应用程序，可能需要随机访问任何像素，但主要的更改都是在图像的末尾添加或删除像素。这种情况下，vector 提供了连续的内存和高效的随机访问
* deque (双端队列)
* 需要频繁地在序列的前端和后端进行插入和删除操作。
* 示例：设计一个任务调度程序，它允许在队列的前端或后端添加任务，然后按优先级从前端执行任务。对于这种应用，deque 提供了高效的前后两端操作。
* map (关键字-值对的有序容器)
* 需要为每个唯一关键字存储一个值，并能够基于关键字快速查找值。
* 示例：电话簿应用程序，其中人名是关键字，电话号码是值。使用map，可以轻松查找任何人的电话号码，并确保电话簿中不会有重复的名字。
* set (有序集合)
* 需要存储唯一元素的集合，并能够快速地检查元素是否存在于集合中。
* 示例：维护一个在线用户的集合。当用户上线时，可以将其添加到集合中，当他们下线时，可以从集合中删除他们。使用set可以确保每个用户在集合中只出现一次，并且可以快速检查某个用户是否在线。
## 练习 11.3:
### 编写你自己的单词计数程序。
答：
```
#include <iostream>
#include <string>
#include <map>

using namespace std;

int main() {
	map<string, size_t> word_count;
	string word;
	while (cin >> word) {
		++word_count[word];
	}
	for (const auto &w : word_count) {
		cout << w.first << " occurs " << w.second << ((w.second > 1) ? " times" : " time") << endl;
	}

	return 0;
}
```
## 练习 11.4:
### 扩展你的程序,忽略大小写和标点。例如, "example. "、"example,"和"Example"应该递增相同的计数器。
答：
```
#include <iostream>
#include <string>
#include <map>
#include <cctype>

using namespace std;

string processWord(const string& word) {
	string result;
	for (char ch : word) {
		if (!ispunct(ch)) {
			result += tolower(ch);
		}
	}
	return result;
}

int main() {
	map<string, size_t> word_count;
	string word;
	while (cin >> word) {
		string processed_word = processWord(word);
		++word_count[processed_word];
	}
	for (const auto &w : word_count) {
		cout << w.first << " occurs " << w.second << ((w.second > 1) ? " times" : " time") << endl;
	}

	return 0;
}
```
## 练习 11.5：
### 解释 map和 set的区别。你如何选择使用哪个?
答：
* map 存储的是 key-value 对，set 只存储 key 。
* 如果需要关联数据，例如根据员工 ID 查找员工的薪水，应该使用 map。
* 如果只需要确保元素的唯一性或者快速查找某个值是否存在，例如检查一个列表中是否已有某个数字，应该使用 set。
## 练习 11.6:
### 解释set和list的区别。你如何选择使用哪个?
答：
* 元素唯一性：
* set 所有元素都是唯一的，不允许有重复元素。list 可以有重复的元素。
* 排序：
* set 元素总是保持有序。list 元素的顺序由插入顺序决定，但可以手动排序。
* 时间复杂度：
* set 插入、删除和查找操作通常具有对数时间复杂度。list 插入和删除操作具有常数时间复杂度，但查找操作是线性的。
* 当需要元素的唯一性、需要经常进行查找操作时，优先选择 set，因为它自动确保元素的唯一性，查找速度是对数时间。
* 当数据的插入和删除非常频繁、数据结构需要经常进行全局排序或需要维持插入顺序，但对查找性能要求不高时，可以使用 list。
## 练习 11.7:
### 定义一个map,关键字是家庭的姓,值是一个vector,保存家中孩子(们) 的名。编写代码,实现添加新的家庭以及向已有家庭中添加新的孩子。
答：
```
#include <iostream>
#include <string>
#include <map>
#include <vector>

using namespace std;

void addFamily(map<string, vector<string>>& families, const string& surname) {
	families[surname] = vector<string>();
}

void addChild(map<string, vector<string>>& families, const string& surname, const string& childName) {
	families[surname].push_back(childName);
}

int main() {
	map<string, vector<string>> families;

	// 添加一个新的家庭
	addFamily(families, "Smith");

	// 向Smith家庭添加孩子
	addChild(families, "Smith", "John");
	addChild(families, "Smith", "Jane");

	// 添加另一个家庭
	addFamily(families, "Johnson");

	// 向Johnson家庭添加孩子
	addChild(families, "Johnson", "Emily");
	addChild(families, "Johnson", "Ella");

	// 打印家庭及其孩子
	for (const auto& family : families) {
		cout << "Family surname: " << family.first << endl;
		cout << "Children: ";
		for (const auto& child : family.second) {
			cout << child << " ";
		}
		cout << endl;
	}

	return 0;
}
```
## 练习 11.8：
### 编写一个程序，在一个 vector 而不是一个 set 中保存不重复的单词。 使用 set 的优点是什么？
答：
```
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
	vector<string> words;
	string word;

	while (cin >> word) {
		// 使用 std::find 检查 word 是否已经在 vector 中
		if (find(words.begin(), words.end(), word) == words.end()) {
			words.push_back(word);
		}
	}

	for (const auto &w : words) {
		cout << w << endl;
	}

	return 0;
}
```
* set的优点：
* 自动去重：在set中，所有的元素都是唯一的。不需要编写额外的代码来确保元素的唯一性。
* 有序：set中的元素总是按照特定的顺序存储，而vector则不是。
* 快速查找：set查找一个元素的时间复杂度是O(log n)。而在vector中使用find函数查找一个元素的平均时间复杂度是O(n)。
## 练习 11.9:
### 定义一个map，将单词与一个行号的 list 关联， list 中保存的是单词所 出现的行号。
答：
```
map<string, list<int>> search;
```
## 练习 11.10:
### 可以定义一个 vector<int>::iterator 到 int 的 map 吗?list<int>::iterator 到 int 的 map呢？对于两种情况，如果不能，解释为什么。
答：
* map<vector<int>::iterator, int> m; 这是完全可行的。vector<int>::iterator满足map的关键字类型要求，提供了必要的比较运算符 < 。
* map<list<int>::iterator, int> m;这不是直接可行的。原因是list迭代器不支持关系运算符 < 。如果确实需要这样做，可以通过提供自定义的比较函数或对象来实现，这通常是一个复杂和低效的操作。
## 练习 11.11:
### 不使用decltype重新定义bookstore。
答：
```
multiset<Sales_data, bool(*)(const Sales_data &, const Sales_data &)>bookstore(compareIsbn);
```
## 练习 11.12：
### 编写程序，读入 string 和 int 的序列，将每个 string和 int 存入一 个pair 中，pair 保存在一个 vector中。
答：
```
#include <iostream>
#include <vector>
#include <string>
#include <utility>

using namespace std;

int main() {
	vector<pair<string, int>> vec;
	string str;
	int num;

	cout << "Enter pairs of string and int (Enter 'exit' as string to stop):" << endl;

	while (true) {
		cin >> str;
		if (str == "exit") {
			break;
		}
		cin >> num;

		vec.push_back(make_pair(str, num));
	}

	cout << "Pairs in vector are:" << endl;
	for (const auto &p : vec) {
		cout << p.first << ": " << p.second << endl;
	}

	return 0;
}
```
## 练习 11.13：
### 在上一题的程序中，至少有三种创建 pair 的方法。编写此程序的三个版本，分别采用不同的方法创建pair。解释你认为哪种形式最易于编写和理解，为什么?
答：
```
vec.push_back(pair<string, int>(str, num));
vec.push_back(make_pair(str, num));
vec.push_back({str, num});
```
* 选择哪种方法往往取决于具体的上下文和个人习惯。相对来说第三种方法最简洁，不过也有可读性的潜在问题，除非读者往上追溯 v 的类型声明，否则他们可能不会立即意识到这是一个 pair 的初始化。
## 练习 11.14：
### 扩展你在 11.2.1 节练习（第 378 页）中编写的孩子姓到名的 map，添加一个 pair 的 vector，保存孩子的名和生日。
答：
```
#include <iostream>
#include <string>
#include <map>
#include <vector>

using namespace std;

void addFamily(map<string, vector<pair<string, string>>>& families, const string& surname) {
	families[surname] = vector<pair<string, string>>();
}

void addChild(map<string, vector<pair<string, string>>>& families, const string& surname
	, const string& childName, const string& birthday) {
	families[surname].push_back({ childName, birthday });
}

int main() {
	map<string, vector<pair<string, string>>> families;

	// 添加一个新的家庭
	addFamily(families, "Smith");

	// 向Smith家庭添加孩子
	addChild(families, "Smith", "John", "2005-01-15");
	addChild(families, "Smith", "Jane", "2008-06-20");

	// 添加另一个家庭
	addFamily(families, "Johnson");

	// 向Johnson家庭添加孩子
	addChild(families, "Johnson", "Emily", "2010-09-12");
	addChild(families, "Johnson", "Ella", "2012-03-30");

	// 打印家庭及其孩子
	for (const auto& family : families) {
		cout << "Family surname: " << family.first << endl;
		cout << "Children: ";
		for (const auto& child : family.second) {
			cout << child.first << " (Birthday: " << child.second << ") ";
		}
		cout << endl;
	}

	return 0;
}
```
## 练习 11.15:
### 对一个 int 到 vector<int>的 map,其mapped_type、 key_type和 value_type 分别是什么？
答：
* mapped_type : vector<int>
* key_type : int
* value_type : pair<const int,vector<int>>
## 练习 11.16:
### 使用一个map迭代器编写一个表达式,将一个值赋予一个元素。
答：
```
#include <iostream>
#include <string>
#include <map>

int main() {
	std::map<std::string, int> word_count;
	word_count["hello"] = 1;
	word_count["world"] = 2;

	// 使用迭代器为 "hello" 对应的 value 赋新值
	std::map<std::string, int>::iterator it = word_count.find("hello");
	if (it != word_count.end()) {
		it->second = 9;  // 使用迭代器修改value
	}

	for (const auto &item : word_count) {
		std::cout << item.first << ": " << item.second << std::endl;
	}

	return 0;
}
```
## 练习 11.17：
### 假定 c 是一个 string 的 multiset， v 是一个 string 的 vector，解释下面的调用。指出每个调用是否合法:
```
copy(v.begin(), v.end(), inserter(c, c.end()));
copy(v.begin(), v.end(), back_inserter(c));
copy(c.begin(), c.end(), inserter(v, v.end()));
copy(c.begin(), c.end(), back_inserter(v));
```
答：
* copy(v.begin(), v.end(), inserter(c, c.end())); 合法。
* 解释: 从v的开始到结束位置复制每个元素，并使用inserter插入这些元素到c。因为inserter适用于容器multiset，所以此调用是正确的。
* copy(v.begin(), v.end(), back_inserter(c)); 不合法。
* 解释: 试图从v的开始到结束位置复制每个元素，并使用back_inserter插入这些元素到c的末尾。但问题是multiset不支持push_back方法。
* copy(c.begin(), c.end(), inserter(v, v.end())); 合法。
* 解释: 从c的开始到结束位置复制每个元素，并使用inserter插入这些元素到v的末尾。vector支持在任意位置插入元素，这个调用是正确的。
* copy(c.begin(), c.end(), back_inserter(v)); 合法。
* 解释: 从c的开始到结束位置复制每个元素，并使用back_inserter插入这些元素到v的末尾。因为vector支持push_back方法，这个调用是正确的。
## 练习 11.18:
### 写出第382页循环中map_it的类型,不要使用auto或decltype.
答：
map<string, size_t>::const_iterator map_it
## 练习 11.19：
### 定义一个变量，通过对 11.2.2 节(第 378 页)中的名为 bookstore 的multiset 调用 begin()来初始化这个变量。写出变量的类型，不要使用 auto 或decltype。
答：
```
multiset<Sales_data, bool(*)(const Sales_data &, const Sales_data &)>::iterator msetIt = bookstore.begin();
```
## 练习11.20：
### 重写 11.1 节练习（第376页）的单词计数程序，使用insert 代替下标操作。 你认为哪个程序更容易编写和阅读？ 解释原因。
答：
```
#include <iostream>
#include <string>
#include <map>

using namespace std;

int main() {
	map<string, size_t> word_count;
	string word;
	while (cin >> word) {
		auto ret = word_count.insert({ word, 1 });
		if (!ret.second) 
			++ret.first->second; 
	}
	for (const auto &w : word_count) {
		cout << w.first << " occurs " << w.second << ((w.second > 1) ? " times" : " time") << endl;
	}

	return 0;
}
```
* insert版本的单词技术程序更为繁琐。
## 练习 11.21：
### 假定 word_count 是一个 string 到 size_t 的 map, word 是一个 string，解释下面循环的作用：
```
while (cin >> word)
 ++word_count.insert({word, 0}).first->second;
```
答：
* 这个循环是用于读取标准输入中的单词并统计每个单词出现的次数。
* 具体解析如下：
* /1. cin >> word：这是一个输入操作，它从标准输入中读取一个单词并存放到 word 中。如果成功读取一个单词，循环将继续；否则，循环将结束。
* /2. word_count.insert({word, 0})：这是对 word_count map 的插入操作。尝试将一个键值对 {word, 0} 插入到 map 中。如果 word 这个键在 map 中已经存在，那么插入操作不会发生，而是直接返回这个键对应的迭代器。如果 word 在 map 中不存在，则会插入 {word, 0} 这个键值对，并返回新插入键值对的迭代器。
* /3. word_count.insert({word, 0}).first：这会返回上述 insert 操作返回的 pair 中的第一个元素，也就是一个指向 {word, count} 的迭代器。
* /4. ++word_count.insert({word, 0}).first->second：这一操作会对迭代器指向的键值对中的值加一。如果这个单词在之前出现过，它的计数将增加；如果这是第一次遇到这个单词，它的计数将从0变为1。
## 练习 11.22：给定一个 map<string,vector<int>>，对此容器的插入一个元素的insert 版本，写出其参数类型和返回类型。
答：
* pair<map<string, vector<int>>::iterator, bool> p = m.insert({ string("hello"),vector<int>({1})});
## 练习 11.23：
### 11.2.1 节练习（第 378 页）中的 map 以孩子的姓为关键字，保存他们的名的vector,用multimap重写此map.
答：
```
#include <iostream>
#include <string>
#include <map>

using namespace std;

void addChild(multimap<string, string>& families, const string& surname, const string& childName) {
	families.insert({ surname, childName });
}

int main() {
	multimap<string, string> families;

	addChild(families, "Smith", "John");
	addChild(families, "Smith", "Jane");
	addChild(families, "Johnson", "Emily");
	addChild(families, "Johnson", "Ella");

	string current_family;
	for (const auto& entry : families) {
		if (current_family != entry.first) {
			if (!current_family.empty()) {
				cout << endl;  // 把不同的家庭分开
			}
			cout << "Family surname: " << entry.first << endl;
			current_family = entry.first;
			cout << "Children: ";
		}
		cout << entry.second << " ";
	}

	return 0;
}
```
