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
