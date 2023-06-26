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
* 读取应该使用 list<string>::const_iterator ，写入应该使用 list<string>::iterator 。
