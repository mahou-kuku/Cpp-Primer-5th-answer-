## 练习 17.1：
### 定义一个保存三个 int值的 tuple,并将其成员分别初始化为10、20和30。
答：
```
auto t = tuple<int, int, int>{ 10, 20, 30 };
```
## 练习 17.2：
### 定义一个 tuple，保存一个 string、一个 vector<string>和一个pair<string, int>。
答：
```
auto t2 = tuple<string, vector<string>, pair<string, int>>{};
```
## 练习 17.3：
### 重写 12.3 节（第 430 页)中的 TextQuery 程序，使用 tuple 代替 QueryResult 类。你认为哪种设计更好？为什么?
答：
* 哪种设计更好，很大程度上取决于具体的应用场景和个人偏好。如果优先考虑代码的简洁性，使用tuple是一个不错的选择。如果优先考虑代码的可读性和可维护性，定义专门的结果类型可能更加合适。
```
#include <iostream>
#include <fstream>
#include <sstream>
#include <vector>
#include <map>
#include <set>
#include <string>
#include <memory>
#include <tuple>

using namespace std;

class QueryResult; // 前向声明
class TextQuery {
public:
	using line_no = vector<string>::size_type;

	TextQuery(ifstream&);
	// 返回类型现在是一个tuple
	auto query(const string&) const->tuple<string, shared_ptr<set<line_no>>, shared_ptr<vector<string>>>;
private:
	shared_ptr<vector<string>> file; // 输入文件
	// 每个单词到它所在的行号的集合的映射
	map<string, shared_ptr<set<line_no>>> wm;
};

// 读取输入文件并建立单词到行号的映射
TextQuery::TextQuery(ifstream &is) : file(new vector<string>) {
	string text;
	while (getline(is, text)) { // 对文件中每一行
		file->push_back(text); // 保存此行文本
		int n = file->size() - 1; // 当前行号
		istringstream line(text); // 将行文本分解为单词
		string word;
		while (line >> word) { // 对行中每个单词
							   // 如果单词不在 wm中，以之为下标在wm中添加一项
			auto &lines = wm[word]; // lines 是一个 shared_ptr
			if (!lines) // 在我们第一次遇到这个单词时，此指针为空
				lines.reset(new set<line_no>); // 分配一个新的 set
			lines->insert(n); // 将此行号插入 set 中
		}
	}
}

// 修改query的实现
auto TextQuery::query(const string &sought) const -> tuple<string, shared_ptr<set<line_no>>, shared_ptr<vector<string>>> {
	static shared_ptr<set<line_no>> nodata(new set<line_no>);
	auto loc = wm.find(sought);
	if (loc == wm.end()) {
		return make_tuple(sought, nodata, file);
	} else {
		return make_tuple(sought, loc->second, file);
	}
}

ostream &print(ostream & os, const tuple<string, shared_ptr<set<TextQuery::line_no>>, shared_ptr<vector<string>>> &qr) {
	// 使用get函数从tuple中获取数据
	auto &sought = get<0>(qr);
	auto &lines = get<1>(qr);
	auto &file = get<2>(qr);

	os << sought << " occurs " << lines->size() << " " << "times" << endl;
	for (auto num : *lines) {
		os << "\t(line " << num + 1 << ") " << (*file)[num] << endl;
	}
	return os;
}
```
## 练习 17.4：
### 编写并测试你自己版本的findBook函数。
答：
```
#include <iostream>
#include <vector>
#include <string>
#include <tuple>
#include <algorithm>
#include <numeric>
#include "Sales_data.h"

using namespace std;

// matches有三个成员：一家书店的索引和两个指向书店 vector 中元素的迭代器
typedef tuple<vector<Sales_data>::size_type,
	vector<Sales_data>::const_iterator,
	vector<Sales_data>::const_iterator> matches;

// files保存每家书店的销售记录
// findBook返回一个vector，每家销售了给定书籍的书店在其中都有一项
vector<matches> findBook(const vector<vector<Sales_data>> &files,const string &book){
	vector<matches> ret; //初始化为空vector
	// 对每家书店，查找与给定书籍匹配的记录范围（如果存在的话）
	for (auto it = files.cbegin(); it != files.cend(); ++it){
		// 查找具有相同ISBN的Sales_data范围
		auto found = equal_range(it->cbegin(), it->cend(), book, compareIsbn);
		if (found.first != found.second) {  // 此书店销售了给定书籍
			// 记住此书店的索引及匹配的范围
			ret.push_back(make_tuple(it - files.cbegin(), found.first, found.second));
		}
	}
	return ret; //如果未找到匹配记录，ret为空
}

void reportResults(istream &in, ostream &os,const vector<vector<Sales_data> > &files) {
	string s;  // 要查找的书
	while (in >> s) {
		auto trans = findBook(files, s);	// 销售了这本书的书店
		if (trans.empty()) {
			cout << s << " not found in any stores" << endl;
			continue;  // 获得下一本要查找的书
		}
		for (const auto &store : trans)  // 对每家销售了给定书籍的书店
		// get<n>返回store中tuple的指定的成员
			os << "store " << get<0>(store) << " sales: "
			<< accumulate(get<1>(store), get<2>(store), Sales_data(s))
			<< endl;
	}
}

int main() {
	vector<vector<Sales_data>> vec{ {{"aaa",10,20},{"aaa",15,20}},{{"aaa",9,20},{"bbb",9,5},{"ccc",7,8}} };
	reportResults(cin, cout, vec);

	return 0;
}
```
## 练习 17.5：
### 重写 findBook，令其返回一个 pair，包含一个索引和一个迭代器 pair。
答：
```
// 新的matches类型，替换原有的tuple类型
using matches = pair<vector<Sales_data>::size_type,
	pair<vector<Sales_data>::const_iterator,vector<Sales_data>::const_iterator >> ;

// 重写findBook函数
vector<matches> findBook(const vector<vector<Sales_data>>& files, const string& book) {
	vector<matches> ret; // 初始化为空vector
						 // 对每家书店，查找与给定书籍匹配的记录范围（如果存在的话）
	for (auto it = files.cbegin(); it != files.cend(); ++it) {
		// 查找具有相同ISBN的Sales_data范围
		auto found = equal_range(it->cbegin(), it->cend(), Sales_data(book), compareIsbn);
		if (found.first != found.second) { // 此书店销售了给定书籍
										   // 使用新的matches类型构造返回项
			ret.push_back(make_pair(it - files.cbegin(), make_pair(found.first, found.second)));
		}
	}
	return ret; // 如果未找到匹配记录，ret为空
}
```
## 练习 17.6：
### 重写findBook,不使用tuple或pair。
答：
```
// 定义一个适合的结构体
struct MatchResult {
	MatchResult(size_t idx, vector<Sales_data>::const_iterator f, vector<Sales_data>::const_iterator l)
		: index(idx), first(f), last(l) {}
	vector<Sales_data>::size_type index; // 存储书店的索引
	vector<Sales_data>::const_iterator first; // 匹配范围的起始迭代器
	vector<Sales_data>::const_iterator last; // 匹配范围的结束迭代器
	
};

// 重写findBook函数
vector<MatchResult> findBook(const vector<vector<Sales_data>>& files, const string& book) {
	vector<MatchResult> ret;
	for (auto it = files.cbegin(); it != files.cend(); ++it) {
		auto found = equal_range(it->cbegin(), it->cend(), book, compareIsbn);
		if (found.first != found.second) {  // 此书店销售了给定书籍
			ret.push_back(MatchResult(it - files.cbegin(), found.first, found.second));
		}
	}
	return ret;
}
```
## 练习 17.7：
### 解释你更倾向于哪个版本的 findBook，为什么。
答：
* 个人倾向会因情况而异。
* 使用tuple或pair的版本： 这种方法的优点在于简洁性。它不需要额外定义结构体，代码量较少。但缺点是可读性相对较差，使用get<0>(matches)、get<1>(matches)这样的访问方式可能不够直观。
* 使用自定义结构体的版本： 这种方法的优点在于提高了代码的可读性和易理解性。每个成员都有明确的名称，如index、first、last，使得代码更易于理解和维护。缺点是需要额外定义一个结构体，增加了代码量。
## 练习 17.8：
### 在本节最后一段代码中,如果我们将Sales_data()作为第三个参数传递 给accumulate,会发生什么?
答：
* accumulate 返回的 Sales_data 的 bookNo 会变成一个空字符串string。
## 练习 17.9：
### 解释下列每个 bitset 对象所包含的位模式：
```
(a) bitset<64> bitvec(32);
(b) bitset<32> bv(1010101);
(c) string bstr; cin >> bstr; bitset<8>bv(bstr);
```
答：
* (a) 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00100000
* (b) 00000000 00000000 00000000 01010101
* (c) 如果输入bstr的字符都是0或1，则由前8个字符决定bv的位模式。
