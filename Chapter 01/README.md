### 练习 1.1:  
#### 查阅你使用的编译器的文档,确定它所使用的文件命名约定。编译并运行第2 页的 main 程序。  
答：  
···
所用编译器：Visual Studio 2015  
文件名后缀：.cpp  
开始菜单里找到“Visual Studio 2015”文件夹里面有“VS2015 开发人员命令提示”可以打开命令行，编译命令：CL prog1.cpp  
我刚打开命令行还显示“PowerShell不是内部或外部命令”，打开计算机-属性-高级系统设置-环境变量-对着path双击把“变量值”全复制到笔记本里，在最后面加上  
%SystemRoot%\system32;%SystemRoot%;%SystemRoot%\System32\Wbem;%SYSTEMROOT%\System32\WindowsPowerShell\v1.0\;  
注意本来的“变量值”最后面有没有分号【;】没有加上分号再加上这段，复制，替换掉“变量值”栏里本来的内容，一路确定就可以用了。  
···
