python正则表达式

## 常用方法
1.`match(restr,str,modifier)`，可以用group()方法返回匹配结果，如果正则字符串中存在组匹配，可以传递数字参数；用span()方法返回匹配的准确位置，如果正则字符串中存在组匹配，可以传递数字参数。  

### 常见修饰符
* re.I  大小写不敏感
* re.M  多行匹配，影响^和$
* re.S  让`.`可以匹配换行符

2.`search(restr,str,modifier)`,返回第一个匹配的结果。和match的区别是，match是从字符串开头开始匹配的，一旦开头不符合，则直接匹配失败。

3.`findall(restr,str,modifier)`,返回所有匹配结果，返回一个list类型  

4.`sub(restr,replace_str,str)`,正则中的字符串replace方法。

5.`compile(restr)`,返回一个正则对象，方便其他方法直接引用。