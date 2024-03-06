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
