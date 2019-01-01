# javascript正则表达式复习总结

标签：`笔记` `javascript` `正则表达式`

### 前言
辅助工具：[https://regexper.com/](https://regexper.com/)

详细文档：[MDN web docs](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)

在线编辑：[codepen](https://codepen.io/anon/pen/mQrwOO?editors=1111)

### 基础语法

**定义正则：2种写法**
1. 直接写: `/正式表达式/修饰符`, eg: `const re = /a/i`
2. 创建一个RegExp对象: `new RegExp('正则表达式', '修饰符')`, eg: `const re = new RegExp('a', 'i')`

**常用修饰符**

| 语法   | 说明 |
| ---- | ---- |
| \w     | 单词，匹配任何ASCII单字节[a-zA-Z0-9] |
| \W     | 非单词，[^a-zA-Z0-9] |
| \d     | 数字，匹配任何数字[0-9] |
| \D     | 非数字，[^0-9] |
| \s     | 空白，匹配任何Unicode空白符[ \t\v\n\r\f] |
| \S     | 非空白 |
| [...]  | 单词 |
| [^...] | 非单词 |

**特殊符号/字符需要 `/` 转义**

特殊符号：`! $ ^ * + = | . ? \ / ( ) [ ] { } `
特殊字符：` o t n f r xnn uxxxx cX `

**量词，重复类**

|语法   |说明|
| ---- | ---- |
|{n}    |匹配前一项n次|
|{n,}   |匹配前一项至少一次|
|{n,m}  |匹配前一项至少n次，最多m次|
|{?}    |匹配前一项0次或1次，等价：{0,1}|
|{+}    |匹配前一项至少一次，等价：{1,}|
|{*}    |匹配前一项0次或多次，等价：{0,}|

**分隔符，定位符**

1. 分隔符：`const re = /a|b/i`，a或者b
2. 定位符：
`^`: 匹配字符串开头，eg: const re = /^a/i, 字符串以a开头
`$`: 匹配字符串结尾，eg: const re = /a$/i, 字符串以a结尾
`\b`: 匹配一个单词的边界
`\B`: 匹配一个单词的非边界

注意：方括号中的`^`, `[^abc]`，表示是一个除 a,b,b 之外的任意一个字符。

**分组**

`(...)`: 将几个项目组合成一个单元  
a+，表示“a”字符连续出现至少一次  
(ab)+，表示"ab"两个字符连续出现多次  

```js
const re = /(\d{4})-(\d{2})-(\d{2})/i
const str = "2017-06-12"
console.log(str.match(re)) // ["2017-06-12", "2017", "06", "12"]
console.log(str.replace(re, '$1/$2/$3')) // "2017/06/12"
```

**对象属性/标志**

`i`: 匹配时不区分大小写，ignoreCase  
`g`: 匹配时执行全局匹配，global，无g时，匹配到第一个即停止向下匹配  
`m`: 匹配时执行多行匹配，  

**对象方法**

RegExp方法：
1. `exec()`: 一个在字符串中执行查找匹配的RegExp方法，它返回一个数组（未匹配到则返回null）
2. `test()`: 一个在字符串中测试是否匹配的RegExp方法，它返回true或false
3. `toString()`: 将RegExp对象转换成字符串

String方法：
1. `match`: 返回一个数组或者在未匹配到时返回null
2. `search`: 返回匹配到的位置索引，或者在失败时返回-1
3. `replace`: 使用替换字符串替换掉匹配到的子字符串
4. `split`: 分隔后的子字符串存储到数组中

```js
const re1 = /abc/i
const re2 = /abc/ig
const str = 'wwwabcdefabcdefabcdef'

const result1 = str.match(re1) // ["abc"]
const result2 = str.match(re2) // ["abc", "abc", "abc"]

const result3 = str.search(re1) // 3

const result4 = str.replace(re1, '-') // "www-defabcdefabcdef"
const result5 = str.replace(re2, '-') // "www-def-def-def"

const result6 = str.split(re1) // ["www", "def", "def", "def"]
```

### 组合案例

1. 去掉空格：

```js
function trim(str) {
  return str.replace(/^\s+|\s+$/g, '')
}
function trimAll(str) {
  return str.replace(/\s+/g, '')
}
console.log(trim("  a b c   ")); // "a b c"
console.log(trimAll("  a b c   ")); // "abc"
```

2. 验证qq号码：
合法QQ号码规则：1、5-15位数字；2、不能以0开头  
1-9的数字一个： `[1-9]{1}` / `[1-9]`  
0-9的数字4-14个： `[0-9]{4,14}`  
组合：`/[1-9][0-9]{4,14}/` , 012345，123456789123456789，这2个数字都有符合的片段，但不是合法qq号

```js
const re = /^[1-9][0-9]{4,14}$/
const str = 12345
const result = re.test(str) // true
```

3. 验证手机号：  
按以下规则：第一位【1】，第二位【3,4,5,7,8】，第三位之后【0-9】，共11位数字

```js
// const re = /^1(3|4|5|7|8){1}([0-9]{9})$/
const re = /^1[3|4|5|7|8][0-9]{9}$/
const str = 13011760730
const result = re.test(str) // true
```

4. html中过略视频、图片标签

```js
let htmlStr = '<div style="min-width: 1200px;"><video class="videobg" autoplay="autoplay" loop="loop" width="200px" height="100px"><source src="http://download.test.com/video/bgv9.webm"></video><img src="images/02.gif"/></div>'
const re1 = /<video[^>]*>[\s\S]*<\/[^>]*video>/gi
htmlStr = htmlStr.replace(re1, function (str) {
  return '<a href="javascript:;" class="my-class">视频链接</a>'
})

const re2 = /<img [^>]*src=['"]([^'"]+)[^>]*>/gi
htmlStr = htmlStr.replace(re2, '<img src="my-default.png"/>')
// "<div style='min-width: 1200px;'><a href='javascript:;' class='my-class'>视频链接</a><img src='my-default.png'/></div>"

// const re2 = /<img [^>]*src=['"]([^'"]+)[^>]*>/gi
// htmlStr = htmlStr.replace(re2, '<img src="$1?test=123"/>')
// // "<div style='min-width: 1200px;'><a href='javascript:;' class='my-class'>视频链接</a><img src='images/02.gif?test=123'/></div>"

console.log(htmlStr)
```

<!-- 深入参考： https://juejin.im/post/5965943ff265da6c30653879 -->
