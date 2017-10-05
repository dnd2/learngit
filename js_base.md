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

### 算术运算

* 算术运算在溢出(overflow)、下溢(underflow)或被零整除时不会报错
* 当数字超出数字上限，结果为Infinity值表示，负数为-Infinity
* 下溢是当运算结果无限接近零并比Javascript能表示的最小值还小时的一种情形，此时会返回0
* 被零整除会返回Infinity或-Infinity, 零除零返回NaN, 无穷大除以无穷大、给任意负数作开方或算术运算，与不是数字或无法转换为数字的操作数一起使用都返回NaN
* Javascript预定义了Infinity和NaN2个全局变量，ES5中这2个值定义为只读
	* Number.POSITIVE_INFINITY = Infinity
	* Number.MAX_VALUE + 1 == Number.MAX_VALUE
	* Number.NEGATIVE_INFINITY == -Infinity == -1/0
	* Number.NaN == NaN == 0/0
	* Number.MIN_VALUE/2 == 0
	* -Number.MIN_VALUE/2 == -0 == -1/Infinity 
* 非数字值得特殊性: 它和任何值不等包括自身
	* 无法通过x==NaN判断变量x是否是NaN  
	* 只有当x为NaN时，表达式结果才为true
	* isFinite(),在参数不是NaN、Infinity或-Infinity才返回true
	* 负零值和正零值相等，即使是严格相等测试 0 === -0，1/0 !== 1/-0 : false 正无穷大不等于负无穷大

* 二进制转十进制  (从右到左用二进制的每个数去乘以2的相应次方)
	*  1101.01（2）=1*2^0+0*2^1+1*2^2+1*2^3 +0*2^-1+1*2^-2=1+0+4+8+0+0.25=13.25
	*  把二进制数首先写成加权系数展开式，然后按十进制加法规则求和。这种做法称为"按权相加"法。
* 十进制转二进制 (采用"除2取余，逆序排列"法)
	* 52除以2得到的余数依次为：0、0、1、0、1、1，倒序排列，所以52对应的二进制数就是110100。
	* http://www.360doc.com/content/11/0308/14/5327079_99222581.shtml
	*  https://baike.baidu.com/item/%E5%8D%81%E8%BF%9B%E5%88%B6%E8%BD%AC%E4%BA%8C%E8%BF%9B%E5%88%B6  
* 十进制转16进制
	* 十进制数的整数部分“除以16取余”，十进制数的小数部分“乘16取整”，进行转换
* 16进制转十进制
	* 20 => 2 × 16^1 + 0 × 16^0 = 32    
	* https://baike.baidu.com/item/%E5%8D%81%E5%85%AD%E8%BF%9B%E5%88%B6/4162457
	
* 数值计算  进行浮点数运算存在精度问题，例如，要使用整数的'分'而不是使用小数的'元'进行基于货币单位计算


* 字符串(string)是一组由16位值组成的不可变有序序列，每个字符通常来自于Unicode字符集。  