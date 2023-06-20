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
