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
