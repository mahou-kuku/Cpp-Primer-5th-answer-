## 练习 8.1：
### 编写函数，接受一个 istream&参数，返回值类型也是 istream&。此函数须从给定流中读取数据,直至遇到文件结束标识时停止。它将读取的数据打印在标准输出上。完成这些操作后，在返回流之前,对流进行复位，使其处于有效状态。
答：
```
std::istream& func(std::istream &is) {
	std::string buf;
	while (is >> buf) {
		std::cout << buf << std::endl;
	}
	is.clear();  // 对流进行复位
	return is;  // 返回流
}
```
## 练习 8.2:
### 测试函数,调用参数为cin。
答：
```
int main() {
	std::istream& is = func(std::cin);
	std::cout << is.rdstate() << std::endl;  // 检查流的状态
	return 0;
}
```
## 练习 8.3:
### 什么情况下，下面的 while循环会终止?
```
while(cin>>i)/* ... */
```
答：
* badbit、failbit 和eofbit任一个被置位，则while循环将会终止：
* 输入流遇到了文件结束时eofbit被置位。
* 输入的数据类型和变量i的数据类型不匹配时failbit被置位。
* 输入流由于某种原因（例如硬件故障）进入了崩溃状态，此时badbit被置位。
## 练习 8.4：
### 编写函数，以读模式打开一个文件，将其内容读入到一个 string 的 vector中,将每一行作为一个独立的元素存于 vector 中。
答：
```
bool loadFileToVector(const std::string &fileName, std::vector<std::string> &vec) {
	std::ifstream file(fileName);
	if (!file) {
		std::cerr << "Failed to open file " << fileName << std::endl;
		return false;
	}

	std::string line;
	while (std::getline(file, line)) {
		vec.push_back(line);
	}

	return true;
}
```
## 练习 8.5:
### 重写上面的程序,将每个单词作为一个独立的元素进行存储。
答：
```
bool loadFileToVector(const std::string &fileName, std::vector<std::string> &vec) {
	std::ifstream file(fileName);
	if (!file) {
		std::cerr << "Failed to open file " << fileName << std::endl;
		return false;
	}

	std::string line;
	while (file >> line) {
		vec.push_back(line);
	}

	return true;
}
```
## 练习 8.6：
### 重写 7.1.1 节的书店程序（第 229 页)，从一个文件中读取交易记录。将文件名作为一个参数传递给 main （参见 6.2.5 节，第196 页)。
答：
```
#include <iostream>
#include <fstream>
#include "Sales_data.h"

using namespace std;

int main(int argc, char *argv[]) {
	ifstream input(argv[1]);
	Sales_data total;
	if (read(input, total)) {
		Sales_data trans;
		while (read(input, trans)) {
			if (total.isbn() == trans.isbn())
				total.combine(trans);
			else {
				print(cout, total) << endl;
				total = trans;
				print(cout, total) << endl;
			}
		}
	} else
		cerr << "No data?!" << endl;
	return 0;
}
```
## 练习 8.7：
### 修改上一节的书店程序，将结果保存到一个文件中。将输出文件名作为第二个参数传递给 main 函数。
答：
```
#include <iostream>
#include <fstream>
#include "Sales_data.h"

using namespace std;

int main(int argc, char *argv[]) {
	ifstream input(argv[1]);
	ofstream output(argv[2]);
	Sales_data total;
	if (read(input, total)) {
		Sales_data trans;
		while (read(input, trans)) {
			if (total.isbn() == trans.isbn())
				total.combine(trans);
			else {
				print(output, total) << endl;
				total = trans;
				print(output, total) << endl;
			}
		}
	} else
		cerr << "No data?!" << endl;
	return 0;
}
```
## 练习 8.8:
### 修改上一题的程序,将结果追加到给定的文件末尾。对同一个输出文件,运行程序至少两次,检验数据是否得以保留。
答：
```
#include <iostream>
#include <fstream>
#include "Sales_data.h"

using namespace std;

int main(int argc, char *argv[]) {
	ifstream input(argv[1]);
	ofstream output(argv[2],ofstream::app);
	Sales_data total;
	if (read(input, total)) {
		Sales_data trans;
		while (read(input, trans)) {
			if (total.isbn() == trans.isbn())
				total.combine(trans);
			else {
				print(output, total) << endl;
				total = trans;
				print(output, total) << endl;
			}
		}
	} else
		cerr << "No data?!" << endl;
	return 0;
}
```
## 练习 8.9：
### 使用你为 8.1.2 节(第281页)第一个练习所编写的函数打印一个istringstream 对象的内容。
答：
```
std::istream& func(std::istream &is) {
	std::string buf;
	while (is >> buf) {
		std::cout << buf << std::endl;
	}
	is.clear();  // 对流进行复位
	return is;  // 返回流
}

int main() {
	istringstream iss("Hello world!");
	std::istream& is = func(iss);
	std::cout << is.rdstate() << std::endl;  // 检查流的状态
	return 0;
}
```
## 练习 8.10：
### 编写程序，将来自一个文件中的行保存在一个 vector<string>中。然后使用一个 istringstream 从 vector 读取数据元素，每次读取一个单词。
答：
```
#include <iostream>
#include <fstream>
#include <sstream>
#include <vector>
#include <string>

int main() {
	std::vector<std::string> lines;
	std::string line, word;

	std::ifstream file("input.txt");
	if (file.is_open()) {
		while (getline(file, line)) {
			lines.push_back(line);
		}
		file.close();
	}

	for (const auto &l : lines) {
		std::istringstream record(l);
		while (record >> word) {
			std::cout << word << std::endl;
		}
	}

	return 0;
}
```
## 练习 8.11： 
### 本节的程序在外层 while 循环中定义了 istringstream 对象。如果record 对象定义在循环之外,你需要对程序进行怎样的修改?重写程序,将record的定义移到 while 循环之外，验证你设想的修改方法是否正确。
答：
```
int main() {
	string line, word;
	vector<PersonInfo> people;
	istringstream record;

	while (getline(cin, line)) {
		PersonInfo info;
		record.clear();
		record.str(line);	
		record >> info.name;
		while (record >> word) 
			info.phones.push_back(word);
		people.push_back(info); 
	}

	for (auto &i : people) {
		cout << i.name<<" ";
		for (auto &j : i.phones) {
			cout << j << " ";
		}
		cout << endl;
	}
	return 0;
}
```
## 练习 8.12:
### 我们为什么没有在PersonInfo中使用类内初始化?
答：
* PersonInfo 的数据成员会被默认初始化为特定的初始值，我们不需要提供一个默认的值。
* PersonInfo 的对象将被动态赋值，即使我们使用了类内初始化，初始化的值也会被后续的赋值操作覆盖。
