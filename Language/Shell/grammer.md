# Shell脚本细节
- [Shell脚本细节](#shell脚本细节)
  - [字符串拼接](#字符串拼接)
  - [参数的真假值判断](#参数的真假值判断)
  - [执行curl传json参数时](#执行curl传json参数时)
  - [大小写转化](#大小写转化)
  - [字符串截取操作](#字符串截取操作)

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