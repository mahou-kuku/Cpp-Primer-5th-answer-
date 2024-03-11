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
## 练习 17.10：
### 使用序列1、2、3、5、8、13、21初始化一个bitset,将这些位置置位。 对另一个bitset进行默认初始化,并编写一小段程序将其恰当的位置位。
答：
```
#include <iostream>
#include <bitset>
#include <vector>

using namespace std;

int main() {
	vector<int> vec = { 1, 2, 3, 5, 8, 13, 21 };
	bitset<22> bs;

	for (auto i : vec) {
		bs.set(i);
	}

	bitset<22> bs2;
	for (size_t i = 0; i != 22; ++i) {
		bs2[i] = bs[i];
	}
		
	cout << bs << endl;
	cout << bs2 << endl;

	return 0;
}
```
## 练习 17.11：
### 定义一个数据结构，包含一个整型对象，记录一个包含 10个问题的真/假测验的解答。如果测验包含 100 道题，你需要对数据结构做出什么改变（如果需要的话)？
答：
```
struct QuizAnswers {
    std::bitset<10> answers;
};
```
* 如果测验包含100道题，只需要将bitset的大小从10改为100即可：
```
struct QuizAnswers {
    std::bitset<100> answers; // 现在可以记录100个问题的答案
};
```
## 练习 17.12：
### 使用前一题中的数据结构，编写一个函数，它接受一个问题编号和一个表示真/假解答的值,函数根据这两个参数更新测验的解答。
答：
```
void updateAnswer(QuizAnswers& Qa, size_t questionNumber, bool value) {
	if (questionNumber < Qa.answers.size()) {
		Qa.answers.set(questionNumber, value);
	}
}
```
## 练习 17.13：
### 编写一个整型对象，包含真/假测验的正确答案。使用它来为前两题中的数据结构生成测验成绩。
答：
```
// 函数用于计算学生的测验成绩
int calculateScore(const QuizAnswers& correctAnswers, const QuizAnswers& studentAnswers) {
	// 异或操作找出错误的答案（不同的位）
	auto result = correctAnswers.answers ^ studentAnswers.answers;
	// 总题数减去错误的题目数量得到分数
	return correctAnswers.answers.size() - result.count(); // count() 返回值为1的位的数量
}

int main() {

	// 假设测验有10道题
	QuizAnswers correctAnswers{ 0b1111100000 }; // 正确答案
	QuizAnswers studentAnswers{ 0b1111010000 }; // 学生答案


						   // 计算分数
	int score = calculateScore(correctAnswers, studentAnswers);
	std::cout << "Student Score: " << score << " out of " << correctAnswers.answers.size() << std::endl;

	return 0;
}
```
## 练习 17.14：
### 编写几个正则表达式，分别触发不同错误。运行你的程序，观察编译器对每个错误的输出。
答：
* 使用正则表达式库<regex>时，大多数错误都会在运行时而非编译时发生，因为正则表达式通常是以字符串的形式提供给std::regex构造函数的，其内容只能在运行时检查。
```
#include <iostream>
#include <regex>

int main() {
	try {
		std::regex re("[a-z");		// 缺少括号
	}
	catch (const std::regex_error& e) {
		std::cout << "Regex error: " << e.what() << std::endl;
	}


	try {
		std::regex re("a{2,1}");	// 无效的范围
	}
	catch (const std::regex_error& e) {
		std::cout << "Regex error: " << e.what() << std::endl;
	}


	try {
		std::regex re("(?<=abc)def");	// 使用不支持的正则表达式特性
	}
	catch (const std::regex_error& e) {
		std::cout << "Regex error: " << e.what() << std::endl;
	}

	system("pause");
	return 0;
}
```
## 练习 17.15：
### 编写程序，使用模式查找违反“i在e之前，除非在c之后”规则的单词。 你的程序应该提示用户输入一个单词，然后指出此单词是否符合要求。 用一些违反和未违反规则的单词测试你的程序。
答：
```
#include <iostream>
#include <regex>
#include <string>

int main() {
	std::string word;
	std::cout << "Enter a word: ";
	std::cin >> word;

	std::regex pattern("([^c]ei|^ei)", regex::icase);

	if (std::regex_search(word, pattern)) {
		std::cout << "The word \"" << word << "\" violates the rule." << std::endl;
	} else {
		std::cout << "The word \"" << word << "\" follows the rule." << std::endl;
	}

	system("pause");
	return 0;
}
```
## 练习 17.16：
### 如果前一题程序中的 regex 对象用"\[^c]ei"进行初始化，将会发生什么？用此模式测试你的程序，检查你的答案是否正确。
答：
* 会无法正确判断ei出现在单词的开头的情况。
## 练习 17.17：
### 更新你的程序，令它查找输入序列中所有违反“ei”语法规则的单词。
答：
```
#include <iostream>
#include <regex>
#include <string>

int main() {
	std::string text;
	std::cout << "Enter a sentence or paragraph: ";
	std::getline(std::cin, text);  // 使用getline读取整行输入，包括空格

	// 正则表达式，匹配违反规则的单词
	std::regex pattern("[[:alpha:]]*([^c\\s]ei|\\bei)[[:alpha:]]*", std::regex::icase);

	for (sregex_iterator it(text.begin(), text.end(), pattern), end_it; it != end_it; ++it) {
		cout << it->str() << endl;
	}

	system("pause");
	return 0;
}
```
## 练习 17.18：
### 修改你的程序,忽略包含"ei”但并非拼写错误的单词,如“albeit”和“neighbor”。
答：
```
#include <iostream>
#include <regex>
#include <string>

int main() {
	std::string text;
	std::cout << "Enter a sentence or paragraph: ";
	std::getline(std::cin, text);  // 使用getline读取整行输入，包括空格

	// 正则表达式，匹配违反规则的单词
	std::regex pattern("[[:alpha:]]*([^c\\s]ei|\\bei)[[:alpha:]]*", std::regex::icase);

	for (sregex_iterator it(text.begin(), text.end(), pattern), end_it; it != end_it; ++it) {
		if (it->str() == "albeit" || it->str() == "neighbor") {
			continue;	// 跳过特定单词的输出
		}
		cout << it->str() << endl;
	}

	system("pause");
	return 0;
}
```
## 练习 17.19：
### 为什么可以不先检查m\[4]是否匹配了就直接调用 m\[4].str()?
答：
* m\[4].str()如果未匹配则会返回空字符串，m\[6].str()也是如此，所以未匹配的情况下两个string仍然可以进行比较。
## 练习 17.20：
### 编写你自己版本的验证电话号码的程序。
答：
```
#include <iostream>
#include <regex>
#include <string>

using namespace std;

bool valid(const smatch& m){
	// 如果区号前有一个左括号
	if (m[1].matched) {
		// 则区号后必须有一个右括号，之后紧跟剩余号码或一个空格
		return m[3].matched && (m[4].matched == 0 || m[4].str() == " ");
	} else {
		// 否则，区号后不能有右括号
		// 另两个组成部分间的分隔符必须匹配
		return !m[3].matched && m[4].str() == m[6].str();
	}
}

int main(){
	string phone = "(\\()?(\\d{3})(\\))?([-. ])?(\\d{3})([-. ]?)(\\d{4})";
	regex r(phone);		// regex 对象，用于查找我们的模式
	smatch m;
	string s;
	// 从输入文件中读取每条记录
	while (getline(cin, s)){
		// 对每个匹配的电话号码
		for (sregex_iterator it(s.begin(), s.end(), r), end_it; it != end_it; ++it){
			// 检查号码的格式是否合法
			if (valid(*it)){
				cout << "valid : " << it->str() << endl;
			} else {
				cout << "not valid: " << it->str() << endl;
			}
		}
	}

	return 0;
}
```
## 练习 17.21：
### 使用本节中定义的valid函数重写8.3.2节(第289页)中的电话号码程序。
答：
```
#include <iostream>
#include <regex>
#include <string>
#include <sstream>
#include <vector>

using namespace std;

struct PersonInfo {
	string name;
	vector<string> phones;
};

bool valid(const smatch& m)
{
	// 如果区号前有一个左括号
	if (m[1].matched) {
		// 则区号后必须有一个右括号，之后紧跟剩余号码或一个空格
		return m[3].matched && (m[4].matched == 0 || m[4].str() == " ");
	} else {
		// 否则，区号后不能有右括号
		// 另两个组成部分间的分隔符必须匹配
		return !m[3].matched && m[4].str() == m[6].str();
	}
}

int main() {
	string line, word;
	vector<PersonInfo> people;
	while (getline(cin, line)) {
		PersonInfo info;
		istringstream record(line);
		record >> info.name;
		while (record >> word) {
			info.phones.push_back(word);
		}
		people.push_back(info);
	}

	string phone = "(\\()?(\\d{3})(\\))?([-. ])?(\\d{3})([-. ]?)(\\d{4})";
	regex r(phone);		// regex 对象，用于查找我们的模式
	smatch m;

	for (const auto &entry : people) {
		ostringstream formatted, badNums;
		for (const auto &nums : entry.phones) {
			// 对每个匹配的电话号码
			for (sregex_iterator it(nums.begin(), nums.end(), r), end_it; it != end_it; ++it) {
				if (!valid(*it)) {
					badNums << " " << nums;
				} else {
					formatted << " " << nums;
				}
			}
		}
		if (badNums.str().empty()) {
			cout << entry.name << " " << formatted.str() << endl;
		} else {
			cerr << "input error: " << entry.name << " invalid number(s) " << badNums.str() << endl;
		}

	}

	return 0;
}
```
## 练习 17.22：
### 重写你的电话号码程序,使之允许在号码的三个部分之间放置任意多个空白符。
答：
* 可以通过调整正则表达式实现。
```
string phone = "(\\()?(\\d{3})(\\))?([-. ])?\\s*(\\d{3})([-. ]?)\\s*(\\d{4})";
```
## 练习 17.23：
### 编写查找邮政编码的正则表达式。一个美国邮政编码可以由五位或九位数字组成。前五位数字和后四位数字之间可以用一个短横线分隔。
答：
```
regex r("\\d{5}(-\\d{4})?|\\d{9}");
```
