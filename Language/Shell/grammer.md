# Shell脚本细节
- [Shell脚本细节](#shell脚本细节)
  - [字符串拼接](#字符串拼接)
  - [参数的真假值判断](#参数的真假值判断)
  - [执行curl传json参数时](#执行curl传json参数时)
  - [大小写转化](#大小写转化)
  - [字符串截取操作](#字符串截取操作)
  - [判断字符串包含关系](#判断字符串包含关系)
    - [`=~`详解](#详解)

## 字符串拼接
关于长字串拼接，需要各行左侧顶边，且在每行的行尾加上`\`。或者用字符串`" \[换行]"`
```shell
# 第一种方式,注意bbbbb一定要顶住左边
"aaaaaa"\
"bbbbbb"

# 第二种方式, bbbb前面可以有空格，但是空格是会算进字符串中的
"aaaaaa\
bbbbbb"
```

## 参数的真假值判断
参数的真假值判断, 要使用如下代码
`=`两边一定要有空格，否则成赋值语句了
其中使用`[[]]`的原因是防止参数未定义的时候抛出异常
```shell
[ "$1" = "false" ]
[ "$1" = false ]
[[ $1 = false ]]
[[ $1 = "false" ]]
```

## 执行curl传json参数时
使用`curl` 传json的时候，需要`"$json"`来引用字串, 其中一定要注意`$json`的格式是否正确
```shell
curl -v $dingtalkUrl \
    -H 'Content-Type: application/json' \
    -d "$json"
```

## 大小写转化
如果要将变量`BuildType`的字符串变成小写，需要使用如下代码
```shell
# 转小写
BuildType="QuaryTest"
APK_DIR=`echo $BuildType | tr A-Z a-z`

# 转大写
BuildType="QuaryTest"
APK_DIR=`echo $BuildType | tr a-z A-Z`
```

## 字符串截取操作
```shell
apkName="app_qzd_v4.0.3_80004001_2022-07-19_quarytest.apk"
# 截取第一个v开头的字串
apkStartVStr=${apkName#*v}
echo $apkStartVStr
# 截取从右往左数，最后一个_前的字串
apkVersion=${apkStartVStr%%_*}
echo $apkVersion
```

## 判断字符串包含关系
```shell
# 充分利用了grep 的特性
strA="long string"
strB="string"
result=$(echo $strA | grep "${strB}")
if [[ "$result" != "" ]]
then
    echo "包含"
else
    echo "不包含"
fi

# 利用字符串运算符 =~ 直接判断strA是否包含strB
strA="helloworld"
strB="low"
if [[ $strA =~ $strB ]]
then
    echo "包含"
else
    echo "不包含"
fi

# 利用通配符
A="helloworld"
B="low"
if [[ $A == *$B* ]]
then
    echo "包含"
else
    echo "不包含"
fi

```
### `=~`详解
在 Linux bash shell 中，可以使用 `[[` 命令来进行判断。
其中，可以使用 `[[` 命令的 `=～` 操作符来判断某个字符串是否包含特定模式。

查看 man bash 对 `[[` 命令的 `=~` 操作符说明如下：
> An additional binary operator, `=~`, is available, with the same precedence as `==` and `!=`.
When it is used, the string to the right of the operator is considered an extended regular expression and matched accordingly.
The return value is 0 if the string matches the pattern, and 1 otherwise.
If the regular expression is syntactically incorrect, the conditional expression's return value is 2.
Any part of the pattern may be quoted to force the quoted portion to be matched as a string.

既
> 使用 `=~` 操作符时，其右边的字符串被认为是一个扩展正则表达式。
扩展之后跟左边字符串进行比较，看左边字符串是否包含指定模式。
注意是包含关系，不是完整匹配。
也就是判断右边的模式是否为左边字符串的子字符串，而不是判断右边的模式是否完全等于左边字符串。

这里面提到一个非常关键的点，在所给的扩展正则表达式中，用引号括起来的部分会被当成字符串，不再被当成正则表达式。
如果 `=~` 操作符右边的字符串都用引号括起来，那么表示匹配这个字符串自身的内容，不再解析成正则表达式。

如果想要 `=~` 操作符右边的字符串被当成正则表达式来处理，一定不能用引号把整个字符串括起来。
无论是双引号、还是单引号都不要加。
这是常见的使用误区，后面会举例说明。

**注意：** 只有 `[[ ]]` 命令支持 `=~` 操作符，`test` 命令和 `[ ]` 命令都不支持 `=~` 操作符。

