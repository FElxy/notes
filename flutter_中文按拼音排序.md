
# SQLite COLLATE排序
在做flutter开发中使用了floor作为数据库工具，floor底层使用的是SQLite。
下面的语句在iOS和Android的表现上不一致，安卓上是可以看到中文按照拼音顺序，同时中英文分开排序，而iOS上是无法正常获取数据。

```sql
SELECT * FROM Folder where parentFileId=?1 and type = 'normal' ORDER by name COLLATE LOCALIZED
```
问题出现在最后的排序规则上，SQLite内置支持的3种类型：

- BINARY
默认排序规则。比较字符串时使用二进制比较（按字节逐个比较），大小写敏感。
排序按字符串的原始二进制值进行。

- NOCASE
大小写不敏感的排序规则。在比较字符串时，忽略 ASCII 字符的大小写差异，但对非 ASCII 字符大小写敏感。
这对于需要大小写不敏感排序的英文字符串非常有用。

- RTRIM
在比较字符串时，去除右侧的空格后进行二进制比较。
字符串右端的空格不会影响排序结果。

> https://www.w3resource.com/sqlite/sqlite-collating-function-or-sequence.php


Android实际上是多提供了两种类型的支持LOCALIZED和UNICODE，用于实现区域化排序。
- LOCALIZED 根据设备当前的区域设置（Locale）来排序文本。排序结果会根据用户的语言和国家/地区进行调整，确保与用户的本地语言习惯相一致。
适用于需要对文本进行本地化排序的应用场景。例如，在中文环境下，COLLATE LOCALIZED 会根据中文拼音规则进行排序，而在德语环境下，则会按照德语的排序规则。

- UNICODE 基于 Unicode 标准进行排序。这意味着它尝试按照 Unicode 字符的通用规则进行排序，而不考虑具体的区域设置。
虽然这种排序方式是基于 Unicode 的，但并不一定完全符合特定语言或地区的用户习惯。因此，UNICODE 通常更适合处理需要跨区域、跨语言一致排序的场景。

**关键点**
本地化排序: LOCALIZED 可以确保文本排序方式符合用户当前的语言习惯（如拼音排序等），特别适合需要本地化支持的应用。
Unicode 排序: UNICODE 则基于通用的 Unicode 排序规则，适合不依赖具体区域设置的场景。

> https://developer.android.com/reference/android/database/sqlite/SQLiteDatabase#localized-collation---order-by

# 中英混排
如果不是安卓应用则不支持LOCALIZED，这里混排有两个思路，分别是在数据库层面和在应用层面实现，

- 数据库层
在保存和更新名称时，增加一个字段用于存储排序的内容的拼音首字母，sql查询时直接按照此字段排序即可。

- 应用层
获取到所有数据后再根据文件名的拼音首字重新排序。不适合数据量大的场景。

flutter中获取拼音的库使用的是[pinyin https://pub.dev/packages/pinyin](https://pub.dev/packages/pinyin)。
使用方式：
```dart
import 'package:pinyin/pinyin.dart';

String text = "天府广场";

//字符串拼音首字符
PinyinHelper.getShortPinyin(str); // tfgc
```

