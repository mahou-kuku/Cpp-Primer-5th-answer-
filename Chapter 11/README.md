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
