# Number
数字引用类型
Js适用的是“双精度”格式（即64位二进制），` a  |  0可以将变量a中的数值转换为32位有符号整数，因为数位运算符|只适用于32位整数（它只关心32位以内的值，其他的数位将被忽略）。因此与0进行操作即可截取a中的32位数位。`  
* 0x/0X表示十六进制数，0o/0O表示八进制数，0b/0B表示二进制数（尽量使用小写）  
* void __ 返回undefined    
* 对负零进行字符串化会返回"0"，反过来将其从字符串转换为数字，得到的结果是准确的

| Number类属性 | 描述 |
| --- | --- |
| Number.EPSILON | 机器精度，通常是2^-52 |
| Number.MAX_VALUE | 最大浮点数，大约是1.798e+308 |
| Number.MIN_VALUE | 最小浮点数，大约是5e-324，它不是负数，但无限接近于0 |
| Number.MAX_SAFE_INTEGER | “安全”呈现的最大整数是2^53-1，即9007199254740991 |
| Number.MIN_SAFE_INTEGER | 最小整数是-9007199254740991 |
| Number.NEGATIVE_INfiNITY | -Infinity，JavaScript的运算结果有可能溢出，此时结果为Infinity或者-Infinity |
| Number.isInteger(..) | 检测一个值是否是整数 |
| Number.isSafeInteger(..) | 检测一个值是否是安全的整数 |
| Number.isNaN(..) | 检测是一个数值并且是否为NaN，由于全局isNaN()有缺陷，不能检测是否为一个数值，由此es6引出这个 |

| Number类型成员函数 | 描述 | 例子针对var a = 42.59; |
| --- | --- | --- |
| toFixed( 位数 ) | 指定小数保留几位数 | a.toFixed( 0 ); // "43" |
| toExponential() | 转换成指数形式 | a.toExponential() //"4.259e+1" |
| toPrecision(位数) | 指定有效数位的显示位数 |a.toPrecision( 1 ); // "4e+1"  a.toPrecision( 3 ); // "42.6" |

* Math.ceil(num)：向上取整
* Math.floor(num)：向下取整
* Math.round(num)：按四舍五入取整

```
//二进制浮点数会出现问题 
0.1 + 0.2 === 0.3; // false 简单来说，二进制浮点数中的0.1和0.2并不是十分精确，所以在处理带有小数的数字时需要特别注意。

//那么应该怎样来判断0.1 + 0.2和0.3是否相等呢？
//最常见的方法是设置一个误差范围值，通常称为“机器精度”，对JavaScript的数字来说，这个值通常是2^-52 (2.220446049250313e-16)，es6这个值存在于Number.EPSILON中。
if (!Number.EPSILON) {                     
   Number.EPSILON = Math.pow(2,-52); 
}
function numbersCloseEnoughToEqual(n1,n2) {     
   return Math.abs( n1 - n2 ) < Number.EPSILON; 
} 
var a = 0.1 + 0.2; 
var b = 0.3; 
numbersCloseEnoughToEqual( a, b );                  // true 
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 );  // false

//ES6之前的浏览器isNaN的polyfill，两种方式：
if (!Number.isNaN) {     
   Number.isNaN = function(n) {         
   return (             
      typeof n === "number" &&             
      window.isNaN( n )        
      );      
   }; 
}
if (!Number.isNaN) {     
   Number.isNaN = function(n) {         
   return n !== n;     //NaN是JavaScript中唯一一个不等于自身的值
   }; 
}

//JavaScript的运算结果有可能溢出，此时结果为Infinity或者-Infinity。规范规定，如果数学运算（如加法）的结果超出处理范围，则由IEEE 754规范中的“就近取整”模式来决定最后的结果。例如，相对于Infinity，Number.MAX_VALUE  +  Math.pow(2,  969)与Number.MAX_VALUE更为接近，因此它被“向下取整”；而Number.MAX_VALUE + Math.pow(2, 970)与Infinity更为接近，所以它被“向上取整”
//计算结果一旦溢出为无穷数（infinity）就无法再得到有穷数。
var a = Number.MAX_VALUE;   // 1.7976931348623157e+308 
a + a;                      // Infinity 
a + Math.pow( 2, 970 );     // Infinity 
a + Math.pow( 2, 969 );     // 1.7976931348623157e+308

//解决-0 === 0 的问题：或者通过Object.is(a,b)，能使用==和===时就尽量不要使用Object.is(..)，因为前者效率更高、更为通用。Object.is(..)主要用来处理那些特殊的相等比较。
function isNegZero(n) {     
   n = Number( n );     
   return (n === 0) && (1 / n === -Infinity); 
}
if (!Object.is) {     
   Object.is = function(v1, v2) {         
      // 判断是否是-0         
      if (v1 === 0 && v2 === 0) {             
         return 1 / v1 === 1 / v2; 
      }         
      // 判断是否是NaN         
      if (v1 !== v1) {             
         return v2 !== v2;         
      }         
      // 其他情况        
      return v1 === v2;     
   }; 
}
```
