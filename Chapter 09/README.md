## 练习 9.1:
### 对于下面的程序任务, vector, deque和list哪种容器最为适合?解释你的选择的理由。如果没有哪一种容器优于其他容器，也请解释理由。
### (a)读取固定数量的单词，将它们按字典序插入到容器中。我们将在下一章中看到，关联容器更适合这个问题。
### (b)读取未知数量的单词,总是将新单词插入到末尾。删除操作在头部进行。
### (c)从一个文件读取未知数量的整数。将这些数排序，然后将它们打印到标准输出。
答：
* (a) 这种情况下，list可能更为适合。因为这种操作涉及到在容器的任意位置进行插入操作，对于vector来说，这种插入可能会导致元素的移动，性能上不如list。
* (b) 这种情况下，deque最为适合。因为deque是一个双端队列，它支持在头部和尾部的插入和删除操作都是O(1)的复杂度。
* (c) 这种情况下，vector可能是最好的选择。排序操作需要随机访问，vector在这点上比list优秀。打印到标准输出通常是顺序操作，vector和list都可以胜任，但由于vector在内存中是连续存储的，因此在遍历过程中可能会有更好的缓存性能。
## 练习 9.2：
### 定义一个 list 对象，其元素类型是 int 的 deque。
答：
```
std::list<std::deque<int>> ldi;
```
## 练习 9.3:
### 构成迭代器范围的迭代器有何限制?
答：
* 两个迭代器必须来自同一个容器。
* 可以通过反复递增“起始”迭代器来到达“结束”迭代器。
## 练习 9.4:
### 编写函数，接受一对指向vector<int>的迭代器和一个int值。在两个迭代器指定的范围中查找给定的值，返回一个布尔值来指出是否找到。
答：
```
bool findValue(std::vector<int>::const_iterator begin, std::vector<int>::const_iterator end, int value) {
	for (; begin != end; ++begin) {
		if (*begin == value) {
			return true;
		}
	}
	return false;
}
```
## 练习 9.5:
### 重写上一题的函数,返回一个迭代器指向找到的元素。注意,程序必须处理未找到给定值的情况。
答：
```
std::vector<int>::const_iterator findValue(std::vector<int>::const_iterator begin, std::vector<int>::const_iterator end, int value) {
	for (; begin != end; ++begin) {
		if (*begin == value) {
			return begin;
		}
	}
	return end;
}
```
## 练习 9.6：
### 下面程序有何错误？你应该如何修改它？
```
list<int> lst1;
list<int>::iterator iter1 = lst1.begin(),
 iter2 = lst1.end();
while (iter1 < iter2) /* ... */
```
答：
```
//list不支持 < 运算符
//在链表中，元素的地址并不连续，无法直接通过偏移量计算得到一个元素的位置，因此无法有效地比较两个迭代器的先后顺序
while (iter1 != iter2) /* ... */
```
## 练习 9.7:
### 为了索引int的vector中的元素,应该使用什么类型?
答：
```
//“索引”通常是指通过下标来访问数组或者容器中的元素
std::vector<int>::size_type
```
## 练习 9.8：
### 为了读取 string 的 list 中的元素，应该使用什么类型？如果写入 list，又该使用什么类型？
答：
* 读取应该使用 list\<string>::const_iterator ，写入应该使用 list\<string>::iterator 。
## 练习 9.9：
### begin 和 cbegin 两个函数有什么不同？
答：
* begin 在非const对象上返回普通迭代器，允许修改它所指向的元素。在const对象上返回const迭代器，不允许修改它所指向的元素。
* cbegin 总是返回const迭代器，不管在const对象还是非const对象上。这意味着通过从cbegin返回的迭代器，不能修改它所指向的元素。
## 练习 9.10：
### 下面 4个对象分别是什么类型？
```
vector<int> v1;
const vector<int> v2;
auto it1 = v1.begin(), it2 = v2.begin();
auto it3 = v1.cbegin(), it4 = v2.cbegin();
```
答：
* it1 是 vector<int>::iterator
* it2 、 it3 、 it4 是 vector<int>::const_iterator
## 练习 9.11:
### 对6种创建和初始化vector对象的方法,每一种都给出一个实例。 解释每个vector 包含什么值。
答：
```
	std::vector<int> v1;	// v1里没有元素
	std::vector<int> v2(9);	// v2里有9个0
	std::vector<int> v3(9,8);	// v3里有9个8
	std::vector<int> v4 = {0,1,3};	// v4里有3个元素，分别是0、1、3
	std::vector<int> v5(v3);	// v5里也是9个8
	std::vector<int> v6(v5.begin(),v5.end());	// v6里也是9个8
```
## 练习 9.12：
### 对于接受一个容器创建其拷贝的构造函数，和接受两个迭代器创建拷贝的构造函数，解释它们的不同。
答：
* 当将一个容器初始化为另一个容器的拷贝时，两个容器的容器类型和元素类型都必须相同。
* 当将一个容器初始化为迭代器对指定的元素范围时，不要求容器类型是相同的。而且,只要能将要拷贝的元素类型转换为要初始化的容器的元素类型,新容器和原容器中的元素类型也可以不同。
## 练习9.13:
### 如何从一个list/<int>初始化一个vector/<double>?从一个vector/<int>又该如何创建？编写代码验证你的答案。
答：
```
	std::list<int> l;
	std::vector<int> intVec ;

	//都不符合容器类型和元素类型必须相同这一条件，需要使用迭代器范围初始化
	std::vector<double> doubleVec(l.begin(),l.end());
	std::vector<double> doubleVec_2(intVec.begin(),intVec.end());
```
## 练习 9.14：
### 编写程序，将一个 list 中的 char *指针（指向 C 风格字符串）元素赋值给一个 vector 中的 string。
答：
```
	list<const char*> listChar = {"Hello world!"};
	vector<string> vectorString;
	vectorString.assign(listChar.cbegin(), listChar.cend());
```
## 练习 9.15：
### 编写程序，判定两个 vector<int>是否相等。
答：
```
	vector<int> vectorIntA, vectorIntB;
	if (vectorIntA == vectorIntB) {
		// ...
	}
```
## 练习 9.16:
### 重写上一题的程序,比较一个list<int>中的元素和一个vector<int>中的元素。
答：
```
	list<int> listInt;
	vector<int> vectorInt;
	if (listInt == list<int>(vectorInt.begin(), vectorInt.end())) {
		// ...
	}
```
## 练习 9.17：
### 假定 c1 和 c2 是两个容器，下面的比较操作有何限制（如果有的话）？
```
if (c1 < c2)
```
答：

* 必须是相同类型的容器。
* 必须保存相同类型的元素。
* 元素类型必须定义了相应的相等、比较运算符。
## 练习 9.18:
### 编写程序,从标准输入读取string序列,存入一个deque中。编写一个循环,用迭代器打印deque中的元素。
答：
```
#include <iostream>
#include <deque>
#include <string>

int main() {
	std::deque<std::string> dq;
	std::string word;

	while (std::cin >> word) {
		dq.push_back(word);
	}

	for (auto it = dq.begin(); it != dq.end(); ++it) {
		std::cout << *it << std::endl;
	}

	return 0;
}
```
## 练习 9.19：
### 重写上题的程序，用 list 替代 deque。列出程序要做出哪些改变。
答：
```
#include <iostream>
#include <list>
#include <string>

int main() {
	std::list<std::string> li;
	std::string word;

	while (std::cin >> word) {
		li.push_back(word);
	}

	for (auto it = li.begin(); it != li.end(); ++it) {
		std::cout << *it << std::endl;
	}

	return 0;
}
```
## 练习 9.20：
### 编写程序，从一个 list<int>拷贝元素到两个deque 中。值为偶数的所有元素都拷贝到一个 deque中，而奇数值元素都拷贝到另一个deque 中。
答：
```
#include <iostream>
#include <list>
#include <deque>

int main() {
	std::list<int> lst = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
	std::deque<int> evenDeque; 
	std::deque<int> oddDeque; 

	for (const auto &num : lst) {
		if (num % 2 == 0) {
			evenDeque.push_back(num); 
		} else {
			oddDeque.push_back(num);  
		}
	}

	std::cout << "Even numbers: ";
	for (const auto &num : evenDeque) {
		std::cout << num << " ";
	}
	std::cout << std::endl;

	std::cout << "Odd numbers: ";
	for (const auto &num : oddDeque) {
		std::cout << num << " ";
	}
	std::cout << std::endl;

	return 0;
}
```
## 练习 9.21：
### 如果我们将第 308 页中使用 insert 返回值将元素添加到 list 中的循环程序改写为将元素插入到vector中,分析循环将如何工作。
答：
```
	string word;
	vector<string> vec;
	auto iter = vec.begin();
	while (cin >> word) {
		iter = vec.insert(iter, word);
	}
```
## 练习 9.22:
### 假定iv是一个 int 的vector,下面的程序存在什么错误?你将如何修改?
```
	vector<int>::iterator iter = iv.begin(), 
		mid = iv.begin() + iv.size() / 2;
	while (iter != mid)
		if (*iter == some_val) 
			iv.insert(iter, 2 * some_val);
```
答：
* 这段代码有一些问题。首先，在插入元素后，迭代器iter、mid可能会失效。
* 其次，代码中没有改变iter的值，这可能会导致无限循环。
```
	vector<int>::iterator iter = iv.begin();
	mid = iv.begin() + iv.size() / 2;
	while (iter != mid) {
		if (*iter == some_val) {
			iter = iv.insert(iter, 2 * some_val);	// 更新可能失效的迭代器
			iter += 2;		// 插入元素后，跳过该符合条件的原始元素
			mid = iv.begin() + iv.size() / 2;		// 因为有元素被插入，所以“中点”也相应地更新
		} else {
			++iter;		// 移动到下一个元素
		}
	}
```
## 练习 9.23：
### 在本节第一个程序（第 309 页)中，若 c.size（)为 1，则 val、val2、val3和 val4 的值会是什么？
答：
* val、val2、val3和 val4 的值都是首元素的值。
## 练习 9.24:
### 编写程序,分别使用at、下标运算符、front 和 begin提取一个vector 中的第一个元素。在一个空 vector 上测试你的程序。
答：
```
#include <vector>

int main()
{
	std::vector<int> vec;

	vec.at(0);       // 抛出out_of_range异常
	vec[0];          // 未定义行为
	vec.front();     // 未定义行为
	*vec.begin();    // 未定义行为

	return 0;
}
```
## 练习 9.25：
### 对于第 312 页中删除一个范围内的元素的程序，如果 elem1 与 elem2 相等会发生什么？如果 elem2是尾后迭代器,或者 elem1 和 elem2皆为尾后迭代器,又会发生什么？
答：
* 如果 elem1 和 elem2 相等，那么这个范围没有元素，所以 erase 不会删除任何元素，函数返回的迭代器与elem1 和 elem2 相等。
* 如果 elem2 是尾后迭代器，那么 erase 将会删除从 elem1 开始到容器末尾的所有元素，函数返回尾后迭代器。
* 如果 elem1 和 elem2 都是尾后迭代器，那么就跟第一个情况相同：没有元素会被删除，函数返回尾后迭代器。
## 练习 9.26:
### 使用下面代码定义的ia,将 ia拷贝到一个vector和一个list中。使用单迭代器版本的 erase从list中删除奇数元素,从vector中删除偶数元素。
```
int ia[] = { 0, 1, 1, 2, 3, 5, 8, 13, 21, 55, 89 };
```
答：
```
#include <iostream>
#include <vector>
#include <list>

int main() {
	int ia[] = { 0, 1, 1, 2, 3, 5, 8, 13, 21, 55, 89 };
	std::vector<int> iv(std::begin(ia), std::end(ia));
	std::list<int> il(std::begin(ia), std::end(ia));

	// 使用单迭代器版本的 erase 从 list 中删除奇数元素
	for (auto it = il.begin(); it != il.end(); ) {
		if (*it % 2 != 0)
			it = il.erase(it);
		else
			++it;
	}

	// 使用单迭代器版本的 erase 从 vector 中删除偶数元素
	for (auto it = iv.begin(); it != iv.end(); ) {
		if (*it % 2 == 0)
			it = iv.erase(it);
		else
			++it;
	}

	return 0;
}
```
## 练习 9.27：
### 编写程序，查找并删除 forward_list<int>中的奇数元素。
答：
```
#include <forward_list>

using namespace std;

int main() {

	forward_list<int> flst{ 0,1,2,3,4,5 };
	auto prev = flst.before_begin();
	auto curr = flst.begin();
	
	while( curr!= flst.end()){
		if (*curr % 2) {
			curr = flst.erase_after(prev);
		} else {
			prev = curr++;
		}
	}

	return 0;
}
```
## 练习 9.28:
### 编写函数，接受一个forward_list<string>和两个 string 共三个参数。函数应在链表中查找第一个string,并将第二个string插入到紧接着第一个string之后的位置。若第一个string 未在链表中,则将第二个string插入到链表末尾。
答：
```
void find_and_insert(std::forward_list<std::string>& flist, const std::string& to_find, const std::string& to_insert) {
	auto prev = flist.before_begin();
	auto curr = flist.begin();
	while (curr != flist.end()) {
		if (*curr == to_find) {
			flist.insert_after(curr, to_insert);
			return;
		}
		prev = curr++;
	}

	flist.insert_after(prev, to_insert);
}
```
## 练习 9.29：
### 假定 vec 包含 25 个元素，那么 vec.resize(100)会做什么？如果接下来调用vec.resize (10)会做什么?
答：
* 调用 vec.resize(100) 将会将 vec 的大小增加到 100。由于原本 vec 只有 25 个元素，所以这个操作将会在 vec 的末尾添加 75 个元素。这些新添加的元素将会被默认初始化。
* 然后，当调用 vec.resize(10) 时，vec 的大小将会被调整为 10。由于 vec 的当前大小是 100，所以这个操作将会删除最后 90 个元素。所以最后，vec 将只包含最初的 10 个元素。
## 练习 9.30：
### 接受单个参数的 resize 版本对元素类型有什么限制（如果有的话）？
答：
* 容器中的元素类型必须拥有默认构造函数。如果元素类型没有默认构造函数，编译器将无法构造新的元素。
## 练习 9.31:
### 第316页中删除偶数值元素并复制奇数值元素的程序不能用于list 或forward_list。为什么？修改程序，使之也能用于这些类型。
答：
* 这个程序不能用于list或forward_list主要有两个原因：
* std::list和std::forward_list的迭代器不支持+=操作。这是因为这两种类型使用的是双向迭代器和前向迭代器，不支持随机访问。
```
	std::list<int> li = { 0,1,2,3,4,5,6,7,8,9 };
	auto iter = li.begin();
	while (iter != li.end()) {
		if (*iter % 2) {
			iter = li.insert(iter, *iter);
			std::advance(iter, 2); // 使用 std::advance 替换 += 操作
		} else
			iter = li.erase(iter);
	}
```
* std::forward_list没有insert和erases成员函数，应该使用insert_after和erase_after。
```
	std::forward_list<int> fl = { 0,1,2,3,4,5,6,7,8,9 };
	auto prev = fl.before_begin();
	auto iter = fl.begin();
	while (iter != fl.end()) {
		if (*iter % 2) {
			iter = fl.insert_after(prev, *iter);
			std::advance(prev, 2);
			std::advance(iter, 2);
		} else
			iter = fl.erase_after(prev);
	}
```
## 练习 9.32:
### 在第316页的程序中,向下面语句这样调用insert是否合法?如果不合法,为什么？
```
iter = vi.insert(iter, *iter++);
```
答：
* 该调用是非法的。传参并没有规定求值顺序。当执行 vi.insert(iter, *iter++) 时， iter++ 可能在 iter 被传递给 insert 之前就被执行了。
## 练习 9.33：
### 在本节最后一个例子中，如果不将 insert 的结果赋予 begin，将会发生什么？编写程序，去掉此赋值语句，验证你的答案。
答：
* 迭代器begin可能会失效。
## 练习 9.34:
### 假定vi是一个保存int的容器,其中有偶数值也有奇数值,分析下面循环的行为，然后编写程序验证你的分析是否正确。
```
	iter = vi.begin();
	while (iter != vi.end())
		if (*iter % 2)
			iter = vi.insert(iter, *iter);
		++iter;
```
答：
* 这会是一个无限循环。
## 练习 9.35：
### 解释一个vector的 capacity和 size有何区别。
答：
* "size"是指容器当前存储的元素数量。
* "capacity"则是指容器在必须分配更多内存之前可以存储的元素数量。
## 练习 9.36:
### 一个容器的capacity可能小于它的size吗?
答：
* 不可能。
## 练习 9.37:
### 为什么list或array没有capacity成员函数?
答：
* list是由一系列节点组成，每个节点包含一个元素和指向前后节点的指针。因此，list没有固定的内存块，也没有预留的空间。当添加新的元素时，list会动态地分配新的节点来存储这个元素。因此，list没有capacity成员函数，因为它的"容量"本质上是由可用内存限制的。
* array的大小在编译时就固定了，它没有capacity函数，因为其大小是不变的。在定义array时就需要指定其大小，之后就不能再改变它的大小了。换句话说，array的capacity就是它的size，因此，没有必要有一个单独的capacity成员函数。
## 练习 9.38:
### 编写程序，探究在你的标准库实现中，vector是如何增长的。
答：
```
#include <vector>
#include <iostream>

using namespace std;

int main() {

	vector<int> ivec;
	cout << "ivec: size: " << ivec.size()
		<< " capacity: " << ivec.capacity() << endl;
	for (vector<int>::size_type ix = 0; ix != 24; ++ix) {
		ivec.push_back(ix);
		cout << "ivec: size: " << ivec.size()
			<< " capacity: " << ivec.capacity() << endl;
	}
	ivec.shrink_to_fit();
	cout << "ivec: size: " << ivec.size()
		<< " capacity: " << ivec.capacity() << endl;

	return 0;
}
```
## 练习 9.39：
### 解释下面程序片段做了什么：
```
vector<string> svec;
svec.reserve(1024);
string word;
while (cin >> word)
 svec.push_back(word);
svec.resize(svec.size()+svec.size()/2);
```
答：
* 1.	创建一个string类型的vector。
* 2.	预留1024个string对象的空间。这里的reserve函数不会改变vector的size，但会至少保证vector能够存储1024个元素而不需要进行重新分配内存和复制操作。
* 3.	从标准输入读取字符串，将读入的字符串添加到vector的末尾。此处读取会一直持续到遇到文件结束符或者某种错误。
* 4.	调整vector的size。如果当前vector的size为n，那么resize将vector的size改为n + n/2。在这个程序中，如果size超过了预留的1024个单词，vector会进行扩容操作，以便可以添加更多的元素。


## 练习 9.40:
### 如果上一题中的程序读入了256个词，在resize之后容器的capacity可能是多少？如果读入了512个、1000个或 1048个词呢？
答：
* 如果读入了256个词，由于预先设定的capacity是1024，256是低于这个值的，所以不会触发vector的扩容操作。capacity仍然保持为1024。
* 如果读入了512个词，同样，由于预先设定的capacity是1024，不会触发扩容操作。capacity仍然保持为1024。
* 如果读入了1000个词，在执行resize之后，vector的size将变为1000 + 1000/2 = 1500，这超过了最初的capacity（1024）。需要扩容，新的capacity至少会超过1500。
* 如果读入了1048个词，读入操作本身就超过了最初的capacity，需要扩容。在执行resize之后，vector的size将变为1048 + 1048/2 = 1572，这可能又超过了之前扩容过的capacity。需要再次扩容，新的capacity至少会超过1572。
* 具体的增长策略依赖于库的实现，所以这里的数字可能会有所不同。
## 练习 9.41:
### 编写程序,从一个vector<char>初始化一个string。
答：
```
	vector<char> vec{ 'H','e','l','l','o' };
	string s(vec.begin(),vec.end());
```
## 练习 9.42：
### 假定你希望每次读取一个字符存入一个 string中，而且知道最少需要读取100 个字符，应该如何提高程序的性能？
答：
```
	string s;
	// 这样，push_back操作在添加第101个字符前，都不需要分配新的内存，可以有效提高性能
	s.reserve(100);
	char c;
	while (cin >> c) {
		s.push_back(c);
	}
```
## 练习 9.43：
### 编写一个函数，接受三个 string 参数 s、 oldVal 和 newVal。使用迭代器及insert和erase函数将s中所有oldval替换为newVal。测试你的程序,用它替换通用的简写形式,如,将"tho"替换为"though",将"thru"替换为"through"。
答：
```
#include <iostream>
#include <string>

void find_and_replace(std::string& s, const std::string& oldVal, const std::string& newVal) {
	for (auto it = s.begin(); it != s.end(); ++it) {
		if (std::string{ it, it + oldVal.size() } == oldVal) {
			it = s.erase(it, it + oldVal.size());
			it = s.insert(it, newVal.begin(), newVal.end());
			std::advance(it, newVal.size());
		}
	}
}

int main() {
	std::string text = "I tho that was a cat thru the window.";
	find_and_replace(text, "tho", "though");
	find_and_replace(text, "thru", "through");
	std::cout << text << std::endl;

	return 0;
}
```
## 练习 9.44:
### 重写上一题的函数，这次使用一个下标和 replace。
答：
```
#include <iostream>
#include <string>

void find_and_replace(std::string& s, const std::string& oldVal, const std::string& newVal) {
	for (size_t i = 0; i != s.size(); ++i) {
		if (s.substr(i,oldVal.size()) == oldVal) {
			s.replace(i,oldVal.size(),newVal);
			i += newVal.size();
		}
	}
}

int main() {
	std::string text = "I tho that was a cat thru the window.";
	find_and_replace(text, "tho", "though");
	find_and_replace(text, "thru", "through");
	std::cout << text << std::endl;

	return 0;
}
```
## 练习 9.45：
### 编写一个函数，接受一个表示名字的 string 参数和两个分别表示前缀（如"Mr."或"Ms.")和后缀（如"Jr."或"III")的字符串。使用迭代器及 insert 和 append函数将前缀和后缀添加到给定的名字中,将生成的新 string 返回。
答：
```
#include <iostream>
#include <string>

std::string add_prefix_suffix(const std::string& name, const std::string& prefix, const std::string& suffix) {
	std::string new_name = name;
	new_name.insert(new_name.begin(), prefix.begin(), prefix.end());
	new_name.append(suffix);
	return new_name;
}

int main() {
	std::string name = "John Doe";
	std::string new_name = add_prefix_suffix(name, "Mr. ", ", Jr.");
	std::cout << new_name << std::endl;

	return 0;
}
```
## 练习 9.46:
### 重写上一题的函数，这次使用位置和长度来管理 string，并只使用 insert。
答：
```
#include <iostream>
#include <string>

std::string add_prefix_suffix(const std::string& name, const std::string& prefix, const std::string& suffix) {
	std::string new_name = name;
	new_name.insert(0, prefix);
	new_name.insert(new_name.size(), suffix);
	return new_name;
}

int main() {
	std::string name = "John Doe";
	std::string new_name = add_prefix_suffix(name, "Mr. ", ", Jr.");
	std::cout << new_name << std::endl;

	return 0;
}
```
