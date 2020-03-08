
<a href="#1">1. 特殊字符</a>

<a href="#2">2. 仅匹配单字符</a>

<a href="#3">3. 限定符 - 对前面的子表达式重复x次</a>

<a href="#4">4. 常用的正则表达式</a>

<a href="#5">5. 语法（C# PowerShell）</a>

<a href="#6">6. 小练习To do</a>

<a href="#7">7. 参考</a>

只有普通字符的规则是具体且严格的，含有特殊字符的规则才更灵活，所以引入了正则表达式。
> 如何让特殊字符失去意义？
> - 反斜杠\ （Escape符号,使得特殊符号逃离特殊功能）：例如要匹配反斜杠，应输入\\\
> - Python在字符串前加前缀'r'去掉反斜杠的转移机制（r"\n"表示生字符'\n' vs "\n"表示包含换行符的单字符）
> - C# 加@

<a id="1"/>

## 1. 特殊字符
| 特殊字符 | 含义 | 备注|
|--|--|--|
| ^ |匹配字符串的开始位置|在方括号表达式中使用时，表示不接受该方括号表达式中的字符集合|
| $ |匹配字符串的结尾||
|()|子表达式的开始和结束位置||
| [] |匹配[ ]中列举的字符|集合字符[a-z]将匹配任何小写字母；特殊字符在集合内部失去其特殊含义；[\^5]将匹配除'5'以外的任何字符|
| \| |A\|B - 匹配A或B||


<a id="2"/>

## 2. 仅匹配单字符
| 特殊字符 | 含义 | 备注 |
|--|--|--|
| . |匹配除换行符 \n 外的**任意单字符**| 
| \d |匹配数字，即0-9|[0-9]|
| \D |匹配非数的字符|[\^0-9]|
| \s |匹配任意空白字符（空格，Tab键...）|\n换行 \n回车 \t Tab \f换页 \v制表符|
| \S |匹配非空白||
|\w|匹配非特殊字符，即a-z、A-Z、0-9和_|[a-zA-Z0-9_]|
|\W|匹配特殊字符，即a-z、A-Z、0-9和_|[\^a-zA-Z0-9_]|

<a id="3"/>

## 3. 限定符 - 对前面的子表达式重复x次
| 特殊字符 | 含义 | 备注 |
|--|--|--|
| * |对前面的子表达式重复**0次或多次**|等价于{0,}|
| + |对前面的子表达式重复**1次或多次**|规则"ab+"，则"ab"，"ab、bb"均匹配，而"a"不与之匹配。等价于{1,}|
| ? |对前面的子表达式重复**0次或1次**|规则"ab?"，则仅"a"和"ab"均匹配，等价于{0,1}|
| {m} | 对前面的子表达式恰好重复m次|规则"a{4}"表示"aaaa"|
| {m,n} | 对前面的子表达式重复m~n次|省略m则指定0作为下限，省略n指定无限上限|

> \*， \+ 和 ? 限定符都是贪婪匹配，会匹配尽可能多的文本。若想以非贪婪或最小方式匹配，可在限定符后加?: \*?， +?和??
> 例子1：规则"a*?" - 这是一条关于*的规则，而不是关于?的规则，后者只是为前者服务。
> 例子2：规则"a{3,5}"将匹配"aaa"，"a{3,5}?"将匹配"aaaaa"

<a id="4"/>

## 4. 常用的正则表达式
| 要求 | 规则 |
|--|--|
| 正整数 | [1-9][0-9]* |
| 1~99的正整数 | [1-9][0-9]? |

<a id="5"/>

## 5. 语法
### 5.1 C#
1）搜索匹配 - Match(String, String)
```
Match match = Regex.Match(str, pattern, RegexOptions.IgnoreCase);
MatchCollection matches = Regex.Matches(str, pattern);
```
> match会捕获第一个匹配 - match.Value
> matches会捕获所有匹配 - matches[0].Value, matches[1].Value

2）判断是否匹配 - IsMatch(String, String)
```
// 1 - Use IsMatch
Regex REGEX = new Regex(@"balaba (?<key>\d+) balaba");
if(REGEX .IsMatch(str))    {}

// 2 - Use match.Success
Match match = Regex.Match(str);
if (match.Success)
{
    value = match.Groups["key"].Value;
}
```
3）替换 - Replace(String, String)
```
Regex REGEX = new Regex("pattern");
string result = REGEX.Replace(str, "replacement");
```
4）**预先编译pattern**，Group提取匹配项
```
Regex REGEX = new Regex(@"balaba (?<key>\d+) balaba");
MatchCollection matches = REGEX.Matches(str);
if (matches.Count == 1)
{
    value = matches[0].Groups["key"].Value;
}
```
### 5.2 PowerShell
可参考：[PowerShell正则表达式](https://www.pstips.net/regex-describing-patterns.html)
1）搜索匹配 -match
```
"balabala" -match $pattern
```

2）判断是否匹配
```
if ("balabala" -match $pattern)
```
3）替换 -replace
```
"str" -replace "pattern", "replacement"
```
4）提取匹配项
```
$pattern = "balaba (?<key>\d+) balaba"
if ("balabala" -match $pattern)
{
    $matches[0]    # 匹配到的完整字符串的值
    $matches.key;
}
```

<a id="6"/>

## 6. 小练习To do
|Pattern|Explain|
|-------|--|
|"^Change (?<ChangeNumber>\d+) created.*"|  |
|"(?<commit>[0-9a-f]{40})"|  |
|"^\s*\((?<userAlias>.+?)\)"||
|"\^[A-Za-z0-9-]+$"||
|"Merged PR (?<pullRequestId>[0-9]+)"||
|"tree\s+(?<Hash>[0-9a-z]+)"||
|"\d+\s+blob\s+(?<BlobHash>[0-9a-z]+)\s+"||
|"[\^a-zA-Z0-9_]+"||
|"\^\[[^\]]+\]\\.+$"||

<a id="7"/>

## 7. 参考
>1. [正则表达式语法](https://www.runoob.com/regexp/regexp-syntax.html)
> 2. [细说python正则表达式](https://www.jianshu.com/p/147fab022566)