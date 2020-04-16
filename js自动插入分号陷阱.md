### js自动插入分号陷阱
##### 受影响的语句有
- 空语句
- var 
- do-while
- continue
- break
- return
- throw
##### 产生自动插入分号的情况有以下三种
1. 遇到语法不允许的记号如（LineTerminator或"}"）时
		```
		{1
		2}3
	```
    自动添加分号为
    	```
    	{ 1
    	;2;}3;
    	```
2.遇到行末时
	```
	a = b
	++c
	```
	自动添加分号为
	```
	a=b;
	++c;
	```
3. 遇到某些语法时
	```
	return
	"something";
	```
	自动添加分号为
	```
	return;
		"something"
	```