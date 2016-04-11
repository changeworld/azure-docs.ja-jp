<properties 
	pageTitle="Application Insights の Analytics のスカラー式" 
	description="Application Insights の強力な検索ツールである Analytics で使用される数値、文字列、動的な式、および型。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="awills"/>

 
# Analytics のスカラー式


[Analytics](app-insights-analytics.md) は、

[Application Insights](app-insights-overview.md) の強力な検索機能です。ここでは、
Analytics のクエリ言語について説明します。


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor) 
<br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull)
<br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) 
| [itemCount](#itemCount) | [split](#split) | [sqrt](#sqrt) 
<br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) 
| [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



"スカラー" とは、テーブル内の 1 つのセルを占有できる数値や文字列のような値を意味します。スカラー式は、スカラー関数と演算子で構成され、スカラー値に評価されます。`sqrt(score)/100 > target+2` はスカラー式です。

また、"スカラー" には、配列や複合オブジェクトも含まれます。これらも、1 つのデータベース セルに格納できます。

スカラー式は、[クエリ](app-insights-analytics-queries.md)とは異なります。クエリは、結果としてテーブルを返します。

## スカラー

[キャスト](#casts) | [比較](#scalar-comparisons)
<br/>
[gettype](#gettype) | [hash](#hash) | [iff](#iff)|  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

サポートされている型は次のとおりです。

| 型 | その他の名前 | 同等の .NET 型 |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### キャスト

ある型から別の型にキャストできます。一般に、変換が理にかなっている場合はうまく動作します。

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
	toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

### スカラーの比較

||
---|---
`<` |小さい
`<=`|小さいまたは等しい
`>` |大きい
`>=`|大きいまたは等しい
`<>`|等しくない
`!=`|等しくない 
`in`| 右オペランドは (動的) 配列で、左オペランドがその要素のいずれかに等しい
`!in`| 右オペランドは (動的) 配列で、左オペランドがその要素のいずれとも等しくない




### gettype

**戻り値**

単一の引数の基になるストレージ型を表す文字列。これは、`dynamic` のような値がある場合に、特に便利です。この場合、`gettype()` は値がどのようにエンコードされているかを示します。

**例**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### hash

**構文**

    hash(source [, mod])

**引数**

* *source*: ハッシュの計算の基となるソース スカラー。
* *mod*: ハッシュの結果に適用される剰余値。

**戻り値**

指定したスカラーの xxhash (long) 値。特定の mod 値が指定されている場合は、その剰余。

**例**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

`iff()` 関数は、最初の引数 (述語) を評価し、述語が `true` であるか `false` であるかに応じて、
2 番目または 3 番目の引数の値を返します。2 番目と 
3 番目の引数は、同じ型である必要があります。

**構文**

    iff(predicate, ifTrue, ifFalse)


**引数**

* *predicate:* `boolean` 値に評価される式。
* *ifTrue:* *predicate* が `true` に評価された場合に、この式が評価され、その値が関数から返されます。
* *ifFalse:* *predicate* が `false` に評価された場合に、この式が評価され、その値が関数から返されます。

**戻り値**

この関数は、*predicate* が `true` に評価された場合に *ifTrue* 
の値を返し、それ以外の場合に *ifFalse* の値を返します。

**例**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### isnull、isnotnull、notnull

    isnull(parsejson("")) == true

1 つの引数を受け取り、null かどうかを判定します。

**構文**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**戻り値**

値が null かどうかに応じて、True または false を返します。


|○|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("[ ]")|false
|parsejson("{}")|false

**例**

    T | where isnotnull(PossiblyNull) | count

上記の結果を得る方法はほかにもあることに注意してください。

    T | summarize count(PossiblyNull)




## Boolean 

### ブール型リテラル

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### ブール演算子

	and 
    or 

    

## 数値

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### 数値リテラル

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### 算術演算子

|| |
|---|-------------|
| + | 追加 のいずれかを |
| - | 減算 |
| * | 乗算 |
| / | 除算 |
| % | 剰余 |
||
|`<` |小さい 
|`<=`|小さいか等しい 
|`>` |大きい 
|`>=`|大きいか等しい 
|`<>`|等しくない 
|`!=`|等しくない




### bin

値を切り捨てて、指定された bin サイズの倍数である整数にします。[`summarize by`](app-insights-analytics-queries.md#summarize-operator) クエリでよく使用されます。値が分散している場合に、特定の値ごとの小さなセットにグループ化されます。

エイリアス `floor`。

**構文**

     bin(value, roundTo)

**引数**

* *value:* 数値、日付、または期間。 
* *roundTo:* "bin サイズ"。*value* を除算する数値、日付、または期間。 

**戻り値**

*value* 以下で、*roundTo* の最も近い倍数。
 
    (toint((value/roundTo)-0.5)) * roundTo

**例**

式 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


次の式は、バケットのサイズを 1 
秒として、各期間のヒストグラムを計算します。

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### floor

[`bin()`](#bin) のエイリアス。


### rand

乱数ジェネレーター。

* `rand()` - 0.0 から 1.0 の範囲の実数
* `rand(n)` - 0 から n-1 の範囲の整数




### sqrt

平方根関数。

**構文**

    sqrt(x)

**引数**

* *x:* 0 以上の実数。

**戻り値**

* `sqrt(x) * sqrt(x) == x` のような正の数値。
* 引数が負であるか、`real` 値に変換できない場合は `null`。 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## 日付と時刻


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)|  [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### 日付と時刻のリテラル

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|時刻は常に UTC 形式です。日付を省略すると、今日の時刻になります。
`now()`|現在の時刻。
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 日
`1.5h`|1\.5 時間 
`30m`|30 分
`10s`|10 秒
`0.1s`|0\.1 秒
`100ms`| 100 ミリ秒
`10microsecond`|
`1tick`|100 ナノ秒
`time("15 seconds")`|
`time("2")`| 2 日
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### 日付と時刻の式

式 |結果
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |小さい
`<=`|小さいまたは等しい
`>` |大きい
`>=`|大きいまたは等しい
`<>`|等しくない
`!=`|等しくない 




### ago

現在の UTC 時刻から指定された期間を減算します。
`now()` 
と同様、この関数はステートメント内で複数回使用することができます。
また、参照される UTC 時刻はすべてのインスタンスで同じになります。

**構文**

    ago(a_timespan)

**引数**

* *a\_timespan*: 現在の UTC 時刻 
(`now()`) から減算する期間。

**戻り値**

    now() - a_timespan

**例**

過去 1 時間以内のタイムスタンプを持つすべての行は、次のようになります。

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

前の日曜日からの日数を、`timespan` として示す整数。

**構文**

    dayofweek(a_date)

**引数**

* `a_date`: `datetime`。

**戻り値**

前の日曜日の午前 0 時からの `timespan`。日数を示す整数に丸められます。

**例**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

datetime から月 (1 ～ 12) を取得します。

**例**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

datetime から年を取得します。

**例**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

現在の UTC 時刻。オプションで、オフセットの期間を指定できます。この関数はステートメント内で複数回使用することができます。また、参照される時刻はすべてのインスタンスで同じになります。

**構文**

    now([offset])

**引数**

* *offset:* 現在の UTC 時刻に加算される `timespan`。既定値: 0。

**戻り値**

`datetime` 型の現在の UTC 時刻。

    now() + offset

**例**

述語によって特定されるイベント以降の期間を判断します。

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

該当月の最初の日付。

### startofyear

    startofyear(date)

該当年の最初の日付。


### todatetime

エイリアス `datetime()`。

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

エイリアス `timespan()`。

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## String

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### 文字列リテラル

規則は JavaScript の場合と同様です。

文字列は、単一引用符または二重引用符で囲むことがあります。

バックスラッシュ (`\`) は、`\t` (タブ)、`\n` (改行)、文字列を囲む引用符などの文字をエスケープするために使用されます。

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### 難読化された文字列リテラル

難読化された文字列リテラルとは、文字列の出力時 (たとえば、トレース時) に Analytics によってわかりにくくされる文字列のことです。難読化プロセスでは、難読化されるすべての文字が開始 (`*`) 文字に置き換えられます。

難読化された文字列リテラルを形成するには、前に `h` または 'H' を付加します。次に例を示します。

```
h'hello'
h@'world' 
h"hello"
```

### 文字列の比較

演算子|説明|大文字と小文字の区別|実際の例
---|---|---|---
`==`|等しい |あり| `"aBc" == "aBc"`
`<>`|等しくない|あり| `"abc" <> "ABC"`
`=~`|等しい |いいえ| `"abc" =~ "ABC"`
`!~`|等しくない |いいえ| `"aBc" !~ "xyz"`
`has`|右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる|いいえ| `"North America" has "america"`
`!has`|RHS が LHS に完全な単語として含まれない|なし|`"North America" !has "amer"` 
`contains` | RHS が LHS のサブシーケンスとして出現する|いいえ| `"FabriKam" contains "BRik"`
`!contains`| RHS が LHS 内に出現しない|いいえ| `"Fabrikam" !contains "xyz"`
`containscs` | RHS が LHS のサブシーケンスとして出現する|あり| `"FabriKam" contains "Kam"`
`!containscs`| RHS が LHS 内に出現しない|あり| `"Fabrikam" !contains "Kam"`
`startswith`|RHS が LHS の先頭のサブシーケンスである|いいえ|`"Fabrikam" startswith "fab"`
`matches regex`|LHS には RHS との一致が含まれている|あり| `"Fabrikam" matches regex "b.*k"`


語彙全体があるかどうかをテストしている場合は、`has` または `in` を使用します。語彙全体とは、非英数字またはフィールドの先頭か末尾によって囲まれた、記号または英数字から成る単語を意味します。`has` は、`contains` や `startswith` より速く動作します。以下のクエリでは、最初の方が処理速度は速くなります。

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

文字列内の部分文字列の出現回数をカウントします。プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。

**構文**

    countof(text, search [, kind])

**引数**

* *text:* 文字列。
* *search:* *テキスト*内で一致させるプレーン文字列または[正規表現](app-insights-analytics-reference.md#regular-expressions)。
* *kind:* `"normal"|"regex"`。既定では `normal`。 

**戻り値**

コンテナー内で検索文字列が一致する回数。プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。

**例**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (2 ではありません)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

テキスト文字列から[正規表現](app-insights-analytics-reference.md#regular-expressions)との一致を取得します。必要に応じて、抽出された部分文字列を、指定された型に変換することもできます。

**構文**

    extract(regex, captureGroup, text [, typeLiteral])

**引数**

* *regex:* [正規表現](app-insights-analytics-reference.md#regular-expressions)。
* *captureGroup:* 抽出するキャプチャ グループを示す、正の `int` 定数。0 は一致全体、1 は正規表現の最初のかっこで囲まれた部分と一致した値、2 以上は後続のかっこを示します。
* *text:* 検索する `string`。
* *typeLiteral:* オプションの型のリテラル (例: `typeof(long)`)。指定した場合、抽出された部分文字列はこの型に変換されます。 

**戻り値**

*regex* が *text* 内で一致を見つけた場合: 指定されたキャプチャ グループ *captureGroup* に対応する部分文字列。オプションで、*typeLiteral* に変換できます。

一致がないか、型変換が失敗した場合: `null`。

**例**

サンプル文字列 `Trace` で `Duration` の定義が検索されます。一致は `real` に変換され、時間定数 (`1s`) で乗算されて、`Duration` は `timespan` 型になります。この例では、123.45 秒と等しくなります。

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

次の例は、`substring(Text, 2, 4)` に相当します。

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### isempty、isnotempty、notempty

    isempty("") == true

引数が空の文字列または null である場合は True です。
[isnull](#isnull) も参照してください。


**構文**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**戻り値**

引数が空の文字列または null であるかどうかを示します。

|○|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[ ]")|false
|parsejson("{}")|false


**例**


    T | where isempty(fieldName) | count




### replace

正規表現のすべての一致を別の文字列に置き換えます。

**構文**

    replace(regex, rewrite, text)

**引数**

* *regex:* *text* の検索に使用する[正規表現](https://github.com/google/re2/wiki/Syntax)。複数のキャプチャ グループをかっこ内に含めることができます。 
* *rewrite:* *matchingRegex* によるすべての一致を置き換える正規表現。完全一致を参照する場合は `\0`、最初のキャプチャ グループの場合は `\1`、後続のキャプチャ グループの場合は `\2` などを使用します。
* *text:* 文字列。

**戻り値**

*regex* のすべての一致を *rewrite* の評価で置き換えた後の *text*。一致が重なり合うことはありません。

**例**

次のステートメントを実行します。

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

結果は次のとおりです。

| ○    | str | replaced|
|---|---|---|
| 1    | Number is 1.000000  | Number was: 1.000000|
| 2    | Number is 2.000000  | Number was: 2.000000|
| 3    | Number is 3.000000  | Number was: 3.000000|
| 4    | Number is 4.000000  | Number was: 4.000000|
| 5    | Number is 5.000000  | Number was: 5.000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

指定された区切り記号に従って、指定された文字列を分割し、部分文字列が含まれている文字列配列を返します。必要に応じて、特定の部分文字列が存在すれば、それを返すこともできます。

**構文**

    split(source, delimiter [, requestedIndex])

**引数**

* *source*: 指定された区切り記号に従って分割されるソース文字列。
* *delimiter*: ソース文字列を分割するために使用される区切り記号。
* *requestedIndex*: 0 から始まるインデックス `int` (オプション)。指定した場合、返される文字列配列には、要求した部分文字列が含まれます (存在する場合)。 

**戻り値**

指定された区切り記号によって分割された、指定されたソース文字列の部分文字列が含まれている文字列配列。

**例**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### strcat

    strcat("hello", " ", "world")

1 ～ 16 個の引数を連結します。引数は文字列である必要があります。

### strlen

    strlen("hello") == 5

文字列の長さ。

### substring

    substring("abcdefg", 1, 2) == "bc"

指定されたソース文字列の指定されたインデックス位置から部分文字列を抽出します。必要に応じて、要求する部分文字列の長さを指定できます。

**構文**

    substring(source, startingIndex [, length])

**引数**

* *source:* 部分文字列の取得元となるソース文字列。
* *startingIndex:* 要求する部分文字列の、0 から始まる開始文字位置。
* *length:* 要求する部分文字列の文字数を指定するために使用できるオプションのパラメーター。 

**戻り値**

指定した文字列の部分文字列。部分文字列は、startingIndex の (0 から始まる) 文字位置から始まり、文字列の末尾か、指定した文字数になるまで続きます。

**例**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

文字列を小文字に変換します。

### toupper

    toupper("hello") == "HELLO"

文字列を大文字に変換します。



## GUID

    guid(00000000-1111-2222-3333-055567f333de)


## 配列とオブジェクト: 動的な型

[リテラル](#dynamic-literals) | [キャスト](#casting-dynamic-objects) | [演算子](#operators) | [let 句](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Application Insights の例外に対するクエリの結果を次に示します。`details` の値は配列です。

![](./media/app-insights-analytics-scalars/310.png)

**インデックス:** JavaScript と同様に、配列やオブジェクトのインデックスを作成できます。

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* ただし、`arraylength` やその他の Analytics 関数 (".length" 以外) を使用してください。

**キャスト:** 場合によっては、オブジェクトから抽出する要素をキャストする必要があります。これは、オブジェクトの型が一様ではないためです。たとえば、`summarize...to` には次のように特定の型が必要です。

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**リテラル:** 明示的な配列またはプロパティ バッグ オブジェクトを作成するには、次のように、JSON 文字列として記述し、キャストします。

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** オブジェクトのプロパティを個々の行に分解するには、次のように、mvexpand を使用します。

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-scalars/410.png)


**treepath:** 複合オブジェクト内のすべてのパスを検索するには、次のようにします。

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-insights-analytics-scalars/420.png)

**buildschema:** テーブル内の式のすべての値を受け入れる最小限のスキーマを見つけるには、次のようにします。

    exceptions | summarize buildschema(details)

結果は次のとおりです。

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

数値インデックスを使用する必要がある場所をマークするために `indexer` が使用されていることに注目してください。このスキーマでは、以下のように、いくつかの有効なパスがあります (この例のインデックスが範囲内にあると仮定した場合)。

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### 配列とオブジェクトのリテラル

動的リテラルを作成するには、次のように、JSON 文字列引数を指定した `parsejson` (エイリアス `todynamic`) を使用します。

* `parsejson('[43, 21, 65]')` - 数値の配列
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')` - 数値を含む、動的な型の単一の値
* `parsejson('"21"')` - 文字列を含む、動的な型の単一の値

JavaScript とは異なり、JSON では文字列の前後で二重引用符 (`"`) の使用が必須であることに注意してください。そのため、一般的に、JSON 形式でエンコードされた文字列リテラルを単一引用符 (`'`) で囲むことは簡単です。

この例では、動的な値を作成した後、そのフィールドを使用します。

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


<a name="operators"></a>
### 動的な型に対する演算子と関数

|||
|---|---|
| *value* `in` *array*| *array* に *value* と等しい要素がある場合は true。<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| *array* に *value* と等しい要素がない場合は true。
|[`arraylength(`array`)`](#arraylength)| 配列でない場合は null。
|[`extractjson(`path,object`)`](#extractjson)|オブジェクトに移動するためのパスを使用します。
|[`parsejson(`source`)`](#parsejson)| JSON 文字列を動的オブジェクトに変換します。
|[`range(`from,to,step`)`](#range)| 値の配列。
|[`mvexpand` listColumn](app-insights-analytics-queries.md#mvexpand-operator) | リスト内の指定されたセルの各値に対して、行を複製します。
|[`summarize buildschema(`column`)`](app-insights-analytics-queries.md#summarize-operator) |列の内容から型スキーマを推測します。
|[`summarize makelist(`column`)` ](app-insights-analytics-queries.md#summarize-operator)| 行のグループをフラット化し、列の値を配列に格納します。
|[`summarize makeset(`column`)`](app-insights-analytics-queries.md#summarize-operator) | 行のグループをフラット化し、列の値を重複しないように配列に格納します。

### let 句の動的オブジェクト


[let 句](app-insights-analytics-queries.md#let-clause)には動的値が文字列として格納されるため、以下の 2 つの句は同等で、どちらも使用前に `parsejson` (または `todynamic`) が必要です。

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

動的配列内の要素数。

**構文**

    arraylength(array)

**引数**

* *array:* `dynamic` 値。

**戻り値**

*array* 内の要素数。*array* が配列ではない場合は `null`。

**例**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[\]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

パス式を使用している JSON テキストから、指定された要素を取得します。必要に応じて、抽出した文字列を特定の型に変換することもできます。


**構文**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**戻り値**

この関数は、有効な JSON 文字列が含まれている dataSource への JsonPath クエリを実行します。オプションで、その値を 3 番目の引数に基づいて他の型に変換することもできます。



**例**

次のように、角かっこ ([ ]) 表記とドット表記は同等です。

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### JSON パス式

|||
|---|---|
|`$`|ルート オブジェクト|
|`@`|現在のオブジェクト|
|`.` または `[ ]` | 子|
|`[ ]`|配列インデックス|

*(現在、ワイルドカード、再帰、共用体、およびスライスは実装していません。)*


**パフォーマンスに関するヒント**

* `extractjson()` を使用する前に、where 句を適用します。
* 代わりに、[extract](#extract) による正規表現の一致を使用することを検討してください。こちらの方が実行速度が非常に速く、JSON がテンプレートから生成される場合に効率的です。
* JSON から複数の値を抽出する必要がある場合は、`parsejson()` を使用してください。
* 列の型が動的になるように宣言することによって、取り込み時に JSON が解析されるようにすることを検討してください。



### parsejson

`string` を [JSON 値](http://json.org/)として解釈し、値を `dynamic` として返します。JSON 複合オブジェクトの複数の要素を抽出する必要がある場合は、`extractjson()` を使用する方が適しています。

**構文**

    parsejson(json)

**引数**

* *json:* JSON ドキュメント。

**戻り値**

*json* によって指定された、`dynamic` 型のオブジェクト。

**例**

次の例では、`context_custom_metrics` が `string` である場合、次のようになります。

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

その後、次のフラグメントがオブジェクトの `duration` スロットの値を取得し、そこから 2 つのスロット `duration.value` および `duration.min` (それぞれ `118.0` と `110.0`) を取得します。

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

`range()` 関数 (`range` 演算子と混同しないようにしてください) は、一連の等間隔の値を保持する動的配列を生成します。

**構文**

    range(start, stop, step)

**引数**

* *start:* 結果として生成される配列内の最初の要素の値。 
* *stop:* 結果として生成される配列内の最後の要素の値。
または、生成される配列内の最後の要素より大きく、
*start* からの *step* の倍数である整数に含まれる最小値。
* *step:* 配列の連続する 2 つの要素の差異。

**例**

次の例は、`[1, 4, 7]` を返します。

```AIQL
range(1, 8, 3)
```

次の例は、2015 
年のすべての日を保持する配列を返します。

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

文字列を動的な値に変換します。

### treepath

    treepath(dynamic_object)

動的オブジェクトのリーフを識別するパス式をすべて列挙します。

**戻り値**

パス式の配列。

**例**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

"[0]" は配列の存在を示しますが、特定のパスで使用されるインデックスを指定しないことに注意してください。


## サンプリング

### itemCount

[サンプリング](app-insights-sampling.md)の実行中に、SDK で生成されるデータのごく一部が Application Insights ポータルに取り込まれます。サンプリングは、SDK で、またはポータル エンドポイントへの取り込み時に適用できます。

`itemCount` は、ストリームで発生するイベントごとに SDK で生成されたイベントの数を示します。したがって、たとえば、現在のサンプリング レートが 25% の場合は、itemCount == 4 となります。

イベントをカウントする場合は、`count()` ではなく `sum(itemCount)` を使用して、実際の数値を正確に見積もります。次に例を示します。

* 過去 2 時間に発生した例外の数:

    ![](./media/app-insights-analytics-scalars/510.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!----HONumber=AcomDC_0330_2016-->