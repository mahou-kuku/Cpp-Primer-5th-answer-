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
