## 1 re.match、re.search、re.findall 区别
re.match：从字符串的开头开始匹配
re.search：在整个字符串中搜索匹配
re.findall：返回一个包含所有匹配结果的列表
## 2 group在正则中的作用
group 方法通常在成功匹配后使用，它允许你从匹配对象中提取出具体的文本信息。在不带参数的情况下，group() 返回整个匹配的文本。如果有分组，你可以通过传递分组的索引作为参数，获取指定分组的匹配文本

在正则表达式中，使用圆括号 () 可以创建分组
```python
import re

# 在正则表达式中使用圆括号创建分组
pattern = re.compile(r'(\d{3})-(\d{2})-(\d{4})')
match_obj = pattern.match("123-45-6789")

# 使用 group 方法提取分组的内容
group_1 = match_obj.group(1)
group_2 = match_obj.group(2)
group_3 = match_obj.group(3)

print(group_1, group_2, group_3)  # 输出: 123 45 6789

m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
m.group(0)       # The entire match 'Isaac Newton'
m.group(1)       # The first parenthesized subgroup. 'Isaac'
m.group(2)       # The second parenthesized subgroup. 'Newton'
m.group(1, 2)    # Multiple arguments give us a tuple. ('Isaac', 'Newton')
```
## 3 元字符
.（点）：匹配任意单个字符，除了换行符。

*：匹配前面的元素零次或多次。

+：匹配前面的元素一次或多次。

?：匹配前面的元素零次或一次。

^：匹配字符串的开头。

$：匹配字符串的结尾。
\：转义字符，用于取消元字符的特殊意义
## 4 字符集
[]：表示匹配其中任意一个字符。例如，[aeiou] 表示匹配任何一个元音字母
## 5 量词
*：匹配前面的元素零次或多次。

+：匹配前面的元素一次或多次。

?：匹配前面的元素零次或一次。

{n}：匹配前面的元素恰好 n 次。

{n,}：匹配前面的元素至少 n 次。

{n,m}：匹配前面的元素至少 n 次，至多 m 次。
## 6 非捕获组
(?: ... )它允许你对一部分模式进行分组，但不会捕获该分组的匹配结果

## 7 字符类等的含义和用法
\d: 匹配任意一个数字
\w: 匹配任意一个单词字符（字母、数字、下划线），相当于 [a-zA-Z0-9_]
\s: 匹配任意一个空白字符（空格、制表符、换行符）

## 8 贪婪匹配与非贪婪匹配
非贪婪操作符？
```python
import re
test = "aa1234"
pattern_greedy = re.compile(r"aa(\d+)")
pattern_no_greedy = re.compile(r"aa(\d+?)")
print(pattern_greedy.search(test).group(1))
print(pattern_no_greedy.search(test).group(1))
```
## 9 r""

在Python中，使用正则表达式时，通常建议将正则表达式的模式字符串（pattern string）作为原始字符串（raw string）传递给re.compile()函数。原始字符串是以r或R为前缀的字符串。
```python
import re

pattern = re.compile(r'\d+')
```
## 10 反向引用
\b 表示单词边界，\w+ 匹配一个或多个单词字符，(\b\w+\b) 表示一个捕获组，它用于匹配一个完整的单词。然后，\1 是一个反向引用，表示引用第一个捕获组匹配的内容，从而要求重复出现相同的单词。
```python
import re

text = "apple apple"[MySQL面试.md](MySQL%C3%E6%CA%D4.md)
pattern = re.compile(r'(\b\w+\b) \1')  # 匹配重复的单词
match = pattern.search(text)

print(match.group())  # 输出：apple apple
```
## 11 替换
```python
import re

text = "apple orange apple banana"
pattern = re.compile(r'\bapple\b')
result = pattern.sub('fruit', text)

print(result)
# 输出：fruit orange fruit banana
```
## 12 分割
```python
import re

text = "apple,orange,banana"
pattern = re.compile(r',')
result = pattern.split(text)

print(result)  # 输出：['apple', 'orange', 'banana']
```
## 13 零宽断言
(?=exp)：零宽度正先行断言。仅当子表达式 exp 在此位置的右侧匹配时才继续匹配。例如，\w+(?=\d) 与后跟数字的单词匹配

(?!exp)：零宽度负先行断言。仅当子表达式 exp 不在此位置的右侧匹配时才继续匹配。例如，\w+(?!\d) 与后不跟数字的单词匹配

(?<=exp)：零宽度正后发断言。仅当子表达式 exp 在此位置的左侧匹配时才继续匹配。例如，(?<=\d)\w 与跟在数字后面的实例匹配

(?<!exp)：零宽度负后发断言。仅当子表达式 exp 不在此位置的左侧匹配时才继续匹配。例如，(?<!\d)\w 与不跟在数字后面的实例匹配