# Python Regular Expression 

//引自 http://www.runoob.com/python/python-reg-expressions.html

## Example 1

```
#!/usr/bin/python
import re
line = "Cats are smarter than dogs"
matchObj = re.match( r'(.*) are (.*?) .*', line, re.M|re.I)
if matchObj:
    print "matchObj.group() : ", matchObj.group()
    print "matchObj.group(1) : ", matchObj.group(1)
    print "matchObj.group(2) : ", matchObj.group(2)
else:
    print "No match!!"
```

正则表达式：

```
r'(.*) are (.*?) .*'
```

解析:

### 匹配目标是  X + “are” + Y + blablabla

前面的一个 **r** 表示字符串为非转义的原始字符串，让编译器忽略反斜杠，也就是忽略转义字符。但是这个字符串里没有反斜杠，所以这个 **r** 可有可无。

-  **(.\*)** 第一个匹配分组**即X** ，.***** 代表匹配除换行符之外的所有字符。
-  **(.\*?)** 第二个匹配分组**即Y** ，**.\*?** 后面多个问号，代表非贪婪模式，也就是说只匹配符合条件的最少字符
-  后面的一个 **.\*** 没有括号包围，所以不是分组( 即blablabla )，匹配效果和第一个一样，但是不计入匹配结果中。

matchObj.group() 等同于 matchObj.group(0)，表示匹配到的完整文本字符

matchObj.group(1) 得到第一组匹配结果，也就是(.*)匹配到的

matchObj.group(2) 得到第二组匹配结果，也就是(.*?)匹配到的

因为只有匹配结果中只有两组，所以如果填 3 时会报错。

## Example 2

'(?P...)' 分组匹配**

例：身份证 **1102231990xxxxxxxx**

```
import re
s = '1102231990xxxxxxxx'
res = re.search('(?P<province>\d{3})(?P<city>\d{3})(?P<born_year>\d{4})',s)
print(res.groupdict())
```

此分组取出结果为：

```
{'province': '110', 'city': '223', 'born_year': '1990'}
```

直接将匹配结果直接转为字典模式，方便使用。

