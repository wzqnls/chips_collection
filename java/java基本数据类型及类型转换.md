在java中数据类型在大类上可分为基本数据类型和引用数据类型。这篇博文中则主要针对基本数据类型进行介绍和了解每种数据类型的特别范围以及针对各个数据类型之间的类型转换。

## 数据类型的分类结构：
- 基本数据类型
	- 数值型
		- 整数类型（byte， short， int， long）
		- 浮点类型（float， double）
	- 字符型（char）
	- 布尔型（boolean）
- 引用数据类型
	- 类（class）
	- 接口（interface）
	- 数组

### 基本数据类型范围
![20171225174158.png](https://i.loli.net/2017/12/25/5a40c8124e336.png)
一个字节占8位，由此可以直接推算出每种数据类型可表示的数值范围。
![20171225174553.png](https://i.loli.net/2017/12/25/5a40c8f38f257.png)

**1. 数值中带有E表示科学计数法，1.4E-45读作1.4乘以10的负45次方**

**2. boolean类型的值只有false和true**
## 数据类型直接的相互转换
![20171225175030.png](https://i.loli.net/2017/12/25/5a40c9f1e443f.png)
上图标识出了除boolean外的7中数据类型的转换图，实线代表在类型转换过程可以保证无数据信息丢失，虚线则说明在转换过程中很可能回存在数据精度的丢失。箭头所指的方向均可进行数据类型的自动转换，反之则需要进行强制类型转换，强制类型转换过程中也容易造成数据精度的丢失。下面针对这两种转换方式，逐一解释说明。
### 自动类型转换（隐式类型转换）
从上图结合每种数据类型取值范围我们可以看出，当一个数据类型从小范围向大范围进行
转换时，java会自动进行隐士的类型转换，不需要我们手动进行处理。同时，数值在转换之后也不会存在数据精度丢失的问题。

Input：
```
public class TypeExchange {
	public static void main(String[] args) {
		// byte to short, int, long
		byte byteNum = 100;
		System.out.println("byteNum = " + byteNum);
		short shortNum = byteNum;
		System.out.println("shortNum = " + shortNum);
		int intNum = shortNum;
		System.out.println("intNum = " + intNum);
		long longNum = intNum;
		System.out.println("longNum = " + longNum);
        
        // char to int
		int charToInt = 'A';
		System.out.println("charToInt = " + charToInt);
		
		// int to char
		char intToChar = 98;
        System.out.println("intToChar = " + intToChar);
	}
}
```
Output：
```
byteNum = 100
shortNum = 100
intNum = 100
longNum = 100
charToInt = 65
intToChar = b
```
### 强制类型转换
与自动类型转换相反，从取值范围大的数据类型转换至取值范围小的数据类型，如果不进行强制类型转换则会引发报错，同时在强制类型转换的过程中，也需要考虑能否接受精度缺失。
Input：
```
public class TypeExchange {

	public static void main(String[] args) {
		double d = 1000;
		float f = (float) d;
		System.out.println("f = " + f);
		double d2 = 1823.23429345235d;
		float f2 = (float)d2;
		System.out.println("f2 = " + f2);
	}
}
```
Output:
```
f = 1000.0
f2 = 1823.2343
```
f在类型转换过程并没有造成精度的缺失，因为小数位都是零。但是f2的小数位就做了一定的取舍，造成double f2小数后几位的缺失。所以在强制类型转换的过程中，必须要考虑到数值精度的问题，不能盲目run起来就完事了。

## 总结
java的基本数据类型从小到大，我们需要了解的都有那么类型。其次，每种类型的取值范围是从哪里到哪里，知道这些才知道什么场景用什么类型。对于什么时候自动类型转换，什么时候强制类型转换，记住从小到大自动转，从大到小强制转即可，不可避免还有很多边界情况以及特殊情况文中并未提到，还是要在实践中不断进步。同时，现在IDE一般对于这么问题都会有提示，但是仍需理解并熟悉以上内容做到心中有数。