# Python Learning



## 背景知识

### 	·Python 对象

### 	·动态类型

### 	·内存管理

### 	模块布局

### 	标识符

### 	IO编程



## 数据类型

### 	·标准类型（6）

​		Number

​		String

​		List

​		Tuple

​		Sets

​		Dictionary

​		· 空值 None 不能理解为0 ；None为特殊空值

### 	·内建函数BIF

​		type( ) 查询数据类型

​		isinstance( ) 查询数据类型

​		cmp(a, b) a>b返回1；a<b返回-1; a=b返回0

​		str( )/ repr( ) 返回对象的「字符串表示」

​		dir( ) 查询一个「类或者对象」所有属性

### 	· 字符串

​		字符串BIF

​			· 格式限定符

​			更改显示

​			查看

​			修改

### 	· List/Tuple

​			· List 值可变，Tuple值不可变

​			· 深浅拷贝（新引用vs新对象）

###	· Dist/Set

​			· Dict vs list：list顺序查找（内存占用小）；Dict 引索查找 （内存占用大）

​			· Set vs Tuple ：set可看作无value的字典（无序）；tuple可看作不变的list列表（有序）

### 	· 函数

​			参数传递方式

​				· 包裹传递（可变参数）

​				关键词传递

### 	· 文件IO

​			属性

​			内建函数

​				· open（）

​				· 没有显式关闭文件可能会丢失「缓冲区数据」

​			内建方法

​				· 输入/输出

​				· 文件定位

​			文件迭代

