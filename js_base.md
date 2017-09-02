## Javascript 权威指南重读

**分号的使用**

	1. 每条语句后必须加分号
	2. 避免因缺少分号导致的解析错误，比如:
		- var y = x + f
		- (a+b).toString()
		- 由于第一行没有分号导致解析器会将f当成方法调用
	3. 如果一条语句以'(', '[', '/', '+', '-'开始，则极有可能和前一语句合作一起解析，特别以'('和'['比较常见
	4. 可以在语句前加上一个分号避免此类问题
		- var x = 0
		- ;[x,x+1,x+2].forEach(console.log);
	5. 一般情况下，前语句和下一行语句无法合并解析，则会在第一行后填补分号，但有2种例外
		- 涉及return, break和continue语句，如果这3个关键字紧跟着换行，Javascript会在换行处添加分号
			- return true分2行 会解析成 return; true;
		- ++，--运算中

### 类型、值、变量

* 能够操作值得类型称做数据类型
* 数据类型分为: 原始类型(primitive type)和对象类型(object type),也可分为可变(mutable)类型和不可变类型(immutable)
	* 原始类型如：Number, String, Boolean
	* 对象类型如：Object, Array, Date 
	* 对象和数组属于可变类型
	* 数字、布尔值、null和undefined属于不可变类型
* 2个特殊原始值: null和 undefined
* 对象是属性的结合，每个属性由"key/value"构成
* 特殊对象 -- 函数 
* Javascript采用词法作用域(lexical scoping)
* Jaavascript所有数字均为浮点数值，采用IEEE754标准定义的64位浮点格式表示数字，最大值:+-1.7976931348623157 × 10E308，最小值:+-5 × 10 -E324，整数范围: -2的53次方 ～ 2的53次方
	* 实际操作(如数组下标及位操作)是基于32位整数 