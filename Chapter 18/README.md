## 练习 18.1：
### 在下列 throw 语句中异常对象的类型是什么？
```
(a) range_error r("error");
throw r;
(b) exception *p = &r;
throw *p;
```
### 如果将(b)中的throw语句写成了throw p将发生什么情况？
答：
* (a) 是 range_error 。
* (b) 是 p 所指向的静态类型，也就是 exception 。
* 如果将 (b) 中的 throw 语句写成了 throw p;，那么将会抛出一个指向 exception 的指针，而不是抛出一个异常对象。
## 练习18.2：
### 当在指定的位置发生了异常时将出现什么情况？
```
void exercise(int *b, int *e){
 vector<int> v(b, e);
 int *p = new int[v.size()];
 ifstream in("ints");
 // 此处发生异常
}
```
答：
* 由于异常导致的函数提前退出，p 指向的内存没有被 delete，从而导致内存泄漏。
## 练习18.3：
### 要想让上面的代码在发生异常时能正常工作，有两种解决方案。请描述这两种方法并实现它们。
答：
* 使用智能指针
```
void exercise(int *b, int *e) {
	vector<int> v(b, e);
	// 使用智能指针来管理动态分配的内存，避免内存泄漏
	unique_ptr<int[]> p(new int[v.size()]);
	ifstream in("ints");
	// 此处发生异常
}
```
* 使用try-catch块来确保资源被正确管理
```
void exercise(int *b, int *e) {
	vector<int> v(b, e);
	int *p = nullptr;
	try {
		p = new int[v.size()];
		ifstream in("ints");
		// 此处发生异常

		// 正常处理完成后，确保释放分配的内存
		delete[] p;
	}
	catch (...) { // 捕获所有异常
		delete[] p; // 释放资源
		throw; // 重新抛出当前捕获的异常
	}
}
```
