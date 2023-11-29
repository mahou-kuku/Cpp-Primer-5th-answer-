## 练习 15.1：
### 什么是虚成员？
答：
* 用于实现多态行为。当一个派生类覆盖基类中的虚函数时，通过基类指针或引用调用该函数将会根据对象的实际类型来决定调用哪个版本的函数。
## 练习 15.2:
### protected 访问说明符与 private 有何区别？
答：
* private 和 protected 的主要区别在于对派生类的访问控制。private 成员在派生类中是不可见的，而 protected 成员在派生类中是可见的。
## 练习 15.3：
### 定义你自己的 Quote 类和 print_total 函数。
答：
```
class Quote {
public:
	Quote() = default;
	Quote(const std::string &book, double sales_price) : bookNo(book), price(sales_price) { } 
	std::string isbn() const { return bookNo; }
	// 返回给定数量的书籍的销售总额
	// 派生类负责改写并使用不同的折扣计算算法
	virtual double net_price(std::size_t n) const { return n * price; }
	virtual ~Quote() = default; // 对析构函数进行动态绑定
private:
	std::string bookNo; // 书籍的 ISBN 编号
protected:
	double price = 0.0; // 代表普通状态下不打折的价格
};

// 计算并打印销售给定数量的某种书籍所得的费用
double print_total(ostream &os,	const Quote &item, size_t n){
	// 根据传入item形参的对象类型调用Quote::net_price
	// 或者 Bulk_quote::net_price
	double ret = item.net_price(n);
	os << "ISBN: " << item.isbn() // 调用 Quote：：isbn
		<< " # sold: " << n << " total due: " << ret << endl;
	return ret;
}
```
