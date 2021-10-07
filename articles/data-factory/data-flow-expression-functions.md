---
title: マッピング データ フローでの式関数
titleSuffix: Azure Data Factory & Azure Synapse
description: マッピング データ フローでの式関数について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: dc0bdf8f8d40b23f4f6e8338ab6b608278428149
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060273"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>マッピング データ フローでのデータ変換式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

この記事では、マッピング データ フローで Azure Data Factory および Azure Synapse Analytics によってサポートされる式と関数に関する詳細情報を提供します。


## <a name="expression-functions"></a>式関数

Data Factory および Synapse パイプラインでは、マッピング データ フロー機能の式言語を使用して、データ変換が構成されます。

| 式関数 | タスク |
|-----|-----|
| [abs](data-flow-expression-functions.md#abs) | 数値の絶対値。  |
| [acos](data-flow-expression-functions.md#acos) | 逆コサイン値を計算します。  |
| [add](data-flow-expression-functions.md#add) | 文字列または数値のペアを追加します。 日付に日数を加算します。 タイムスタンプに期間を追加します。 別の配列に類似するタイプの配列を追加します。 \+ 演算子と同じです。  |
| [addDays](data-flow-expression-functions.md#addDays) | 日付またはタイムスタンプに日数を加算します。 日付に対する + 演算子と同じです。  |
| [addMonths](data-flow-expression-functions.md#addMonths) | 日付またはタイムスタンプに月数を加算します。 必要に応じて、タイムゾーンを渡すことができます。  |
| [and](data-flow-expression-functions.md#and) | 論理 AND 演算子です。 && と同じです。  |
| [asin](data-flow-expression-functions.md#asin) | 逆サイン値を計算します。  |
| [atan](data-flow-expression-functions.md#atan) | 逆タンジェント値を計算します。  |
| [atan2](data-flow-expression-functions.md#atan2) | 座標で指定された、平面の正の x 軸と点の間の角度をラジアンで返します。  |
| [between](data-flow-expression-functions.md#between) | 最初の値が他の 2 つの値の範囲内にあるかどうかを確認します。 数値、文字列値、datetime 値を比較できます   |
| [bitwiseAnd](data-flow-expression-functions.md#bitwiseAnd) | 整数型間でのビット AND 演算子です。 & 演算子と同じです   |
| [bitwiseOr](data-flow-expression-functions.md#bitwiseOr) | 整数型間でのビット OR 演算子です。 \| 演算子と同じです  |
| [bitwiseXor](data-flow-expression-functions.md#bitwiseXor) | 整数型間でのビット OR 演算子です。 \| 演算子と同じです  |
| [blake2b](data-flow-expression-functions.md#blake2b) | さまざまなプリミティブ データ型の列セットの Blake2 ハッシュを計算します。ビット長として指定できるのは、8 から 512 の範囲内の 8 の倍数のみです。 行のフィンガープリントを計算するために使用できます  |
| [blake2bBinary](data-flow-expression-functions.md#blake2bBinary) | さまざまなプリミティブ データ型の列セットの Blake2 ハッシュを計算します。ビット長として指定できるのは、8 から 512 の範囲内の 8 の倍数のみです。 行のフィンガープリントを計算するために使用できます  |
| [case](data-flow-expression-functions.md#case) | 交互条件に基づいて、2 つの値のいずれかを適用します。 入力数が偶数の場合、最後の条件でその他は既定で NULL になります。  |
| [cbrt](data-flow-expression-functions.md#cbrt) | 数値の立方根を計算します。  |
| [ceil](data-flow-expression-functions.md#ceil) | 特定の数値以上の最小の整数を返します。  |
| [coalesce](data-flow-expression-functions.md#coalesce) | 一連の入力から、最初の null でない値を返します。 すべての入力は同じ型である必要があります。  |
| [columnNames](data-flow-expression-functions.md#columnNames) | ストリームのすべての出力列の名前を取得します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。  |
| [columns](data-flow-expression-functions.md#columns) | ストリームのすべての出力列の値を取得します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。   |
| [compare](data-flow-expression-functions.md#compare) | 同じ型の 2 つの値を比較します。 value1 < value2 の場合は負の整数、value1 == value2 の場合は 0、value1 > value2 の場合は正の値を返します。  |
| [concat](data-flow-expression-functions.md#concat) | 可変数の文字列を連結します。 文字列で使用する + 演算子と同じです。  |
| [concatWS](data-flow-expression-functions.md#concatWS) | 可変数の文字列を区切り記号を使用して連結します。 最初のパラメーターは区切り記号です。  |
| [cos](data-flow-expression-functions.md#cos) | コサイン値を計算します。  |
| [cosh](data-flow-expression-functions.md#cosh) | 値の双曲線コサインを計算します。  |
| [crc32](data-flow-expression-functions.md#crc32) | さまざまなプリミティブ データ型の列セットの CRC32 ハッシュを指定のビット長で計算します。ビット長として指定できるのは、0(256)、224、256、384、512 の値のみです。 行のフィンガープリントを計算するために使用できます。  |
| [currentDate](data-flow-expression-functions.md#currentDate) | このジョブの実行を開始する現在の日付を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). |
| [currentTimestamp](data-flow-expression-functions.md#currentTimestamp) | ジョブの実行を開始する現在のタイムスタンプをローカル タイムゾーンを使用して取得します。  |
| [currentUTC](data-flow-expression-functions.md#currentUTC) | 現在のタイムスタンプを UTC で取得します。 現在の時刻がクラスターのタイム ゾーンとは異なるタイムゾーンで解釈されるようにする場合は、省略可能なタイム ゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡すことができます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). UTC 時刻を別のタイムゾーンに変換するには、`fromUTC()` を使用します。  |
| [dayOfMonth](data-flow-expression-functions.md#dayOfMonth) | 指定した日付から、その月の何日目かを取得します。  |
| [dayOfWeek](data-flow-expression-functions.md#dayOfWeek) | 指定した日付から、曜日を取得します。 1 - 日曜日、2 - 月曜日...、7 - 土曜日。  |
| [dayOfYear](data-flow-expression-functions.md#dayOfYear) | 指定した日付から、その年の何日目かを取得します。  |
| [days](data-flow-expression-functions.md#days) | ミリ秒単位での日数の期間。  |
| [degrees](data-flow-expression-functions.md#degrees) | ラジアンを角度に変換します。  |
| [divide](data-flow-expression-functions.md#divide) | 数値のペアを除算します。 `/` 演算子と同じです。  |
| [dropLeft](data-flow-expression-functions.md#dropLeft) | 文字列の左側から任意の文字数の文字を削除します。 要求されたドロップが文字列の長さを超える場合は、空の文字列が返されます。|
| [dropRight](data-flow-expression-functions.md#dropRight) | 文字列の右側から任意の文字数の文字を削除します。 要求されたドロップが文字列の長さを超える場合は、空の文字列が返されます。|
| [endsWith](data-flow-expression-functions.md#endsWith) | 文字列が指定した文字列で終了しているかをチェックします。  |
| [equals](data-flow-expression-functions.md#equals) | 等価比較演算子。 == 演算子と同じです。  |
| [equalsIgnoreCase](data-flow-expression-functions.md#equalsIgnoreCase) | 大文字と小文字の区別を無視する等価比較演算子。 <=> 演算子と同じです。  |
| [escape](data-flow-expression-functions.md#escape) | 形式に従って、文字列をエスケープします。 使用できる形式のリテラル値は、'json'、'xml'、'ecmascript'、'html'、'java' です。|
| [expr](data-flow-expression-functions.md#expr) | 結果は文字列からの式です。 これは、非リテラル形式でこの式を記述することと同じです。 これは、文字列表現としてパラメーターを渡すために使用できます。|
| [factorial](data-flow-expression-functions.md#factorial) | 数値の階乗を計算します。  |
| [false](data-flow-expression-functions.md#false) | 常に false 値を返します。 'false' という名前の列がある場合は、関数 `syntax(false())` を使用します。  |
| [floor](data-flow-expression-functions.md#floor) | 特定の数値以下の最大の整数を返します。  |
| [fromBase64](data-flow-expression-functions.md#fromBase64) | 指定した base64 エンコード文字列をデコードします。|
| [fromUTC](data-flow-expression-functions.md#fromUTC) | UTC からタイムスタンプに変換します。 必要に応じて、タイムゾーンを "GMT"、"PST"、"UTC"、"America/Cayman" の形式で渡すことができます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [greater](data-flow-expression-functions.md#greater) | 比較超過演算子。 > 演算子と同じです。  |
| [greaterOrEqual](data-flow-expression-functions.md#greaterOrEqual) | 比較 (以上) 演算子。 >= 演算子と同じです。  |
| [greatest](data-flow-expression-functions.md#greatest) | null 値をスキップした入力値のリストの中の最大値を返します。 すべての入力が null の場合は null を返します。  |
| [hasColumn](data-flow-expression-functions.md#hasColumn) | ストリームでの名前で列の値をチェックします。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  |
| [hour](data-flow-expression-functions.md#hour) | タイムスタンプから時間の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [hours](data-flow-expression-functions.md#hours) | ミリ秒単位での時間数の期間。  |
| [iif](data-flow-expression-functions.md#iif) | 条件に基づいて、2 つの値のいずれかを適用します。 その他が指定されていない場合は、NULL と見なされます。 両方の値は互換性がなければなりません (数値、文字列...)。  |
| [iifNull](data-flow-expression-functions.md#iifNull) | 最初のパラメーターが null かどうかを確認します。 null 以外の場合は、最初のパラメーターが返されます。 null の場合は、2 番目のパラメーターが返されます。 3 つのパラメーターが指定されている場合、動作は iif(isNull(value1), value2, value3) と同じであり、最初の値が null でない場合は 3 番目のパラメーターが返されます。  |
| [initCap](data-flow-expression-functions.md#initCap) | すべての単語の最初の文字を大文字に変換します。 単語は、空白文字で区切られているものとして識別されます。  |
| [instr](data-flow-expression-functions.md#instr) | 文字列内の部分文字列の位置 (1 を基準とする) を見つけます。 見つからない場合は 0 が返されます。  |
| [isDelete](data-flow-expression-functions.md#isDelete) | 行が削除用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  |
| [isError](data-flow-expression-functions.md#isError) | 行がエラーとしてマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  |
| [isIgnore](data-flow-expression-functions.md#isIgnore) | 行を無視するようにマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  |
| [isInsert](data-flow-expression-functions.md#isInsert) | 行が挿入用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  |
| [isMatch](data-flow-expression-functions.md#isMatch) | 行がルックアップで一致するかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  |
| [isNull](data-flow-expression-functions.md#isNull) | 値が NULL かどうかをチェックします。  |
| [isUpdate](data-flow-expression-functions.md#isUpdate) | 行が更新用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  |
| [isUpsert](data-flow-expression-functions.md#isUpsert) | 行が挿入用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  |
| [jaroWinkler](data-flow-expression-functions.md#jaroWinkler) | 2 つの文字列の間の JaroWinkler 距離を取得します。 |
| [lastDayOfMonth](data-flow-expression-functions.md#lastDayOfMonth) | 指定した日付から、その月の最後の日を取得します。  |
| [least](data-flow-expression-functions.md#least) | 比較 (以下) 演算子。 <= 演算子と同じです。  |
| [left](data-flow-expression-functions.md#left) | インデックス 1 から開始して指定した文字数の部分文字列を抽出します。 SUBSTRING(str, 1, n) と同じです。  |
| [length](data-flow-expression-functions.md#length) | 文字列の長さを返します。  |
| [lesser](data-flow-expression-functions.md#lesser) | 比較未満演算子。 < 演算子と同じです。  |
| [lesserOrEqual](data-flow-expression-functions.md#lesserOrEqual) | 比較 (以下) 演算子。 <= 演算子と同じです。  |
| [levenshtein](data-flow-expression-functions.md#levenshtein) | 2 つの文字列の間のレーベンシュタイン距離を取得します。  |
| [like](data-flow-expression-functions.md#like) | パターンは文字通り一致する文字列です。 次の特殊文字は例外です。_ は入力内の任意の 1 文字と一致します (posix 正規表現の . に ```posix``` 正規表現で)|
| [locate](data-flow-expression-functions.md#locate) | 特定の位置から開始して、文字列内の部分文字列の位置 (1 を基準とする) を見つけます。 位置を省略すると、文字列の最初からとみなされます。 見つからない場合は 0 が返されます。  |
| [log](data-flow-expression-functions.md#log) | 対数の値を計算します。 省略可能な底を指定できます。省略すると、オイラー数を返します (使用される場合)。  |
| [log10](data-flow-expression-functions.md#log10) | 10 を底とする対数の値を計算します。  |
| [lower](data-flow-expression-functions.md#lower) | 文字列を小文字にします。  |
| [lpad](data-flow-expression-functions.md#lpad) | 特定の長さになるまで、指定した埋め込み文字で文字列の左側を埋め込みます。 文字列が指定された長さ以上の場合は、その長さまで削除されます。  |
| [ltrim](data-flow-expression-functions.md#ltrim) | 文字列の先頭文字を左から削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  |
| [md5](data-flow-expression-functions.md#md5) | さまざまなプリミティブ データ型の列セットの MD5 ハッシュを計算し、32 文字の16 進数の文字列を返します。 行のフィンガープリントを計算するために使用できます。  |
| [millisecond](data-flow-expression-functions.md#millisecond) | 日付のミリ秒の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [milliseconds](data-flow-expression-functions.md#milliseconds) | ミリ秒単位でのミリ秒数の期間。  |
| [minus](data-flow-expression-functions.md#minus) | 数値を減算します。 日付から日数を減算します。 タイムスタンプから期間を減算します。 2 つのタイムスタンプを減算して、ミリ秒単位での差を取得します。 \- 演算子と同じです。  |
| [minute](data-flow-expression-functions.md#minute) | タイムスタンプから分の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [分](data-flow-expression-functions.md#minutes) | ミリ秒単位での分数の期間。  |
| [mod](data-flow-expression-functions.md#mod) | 数値のペアの剰余です。 % 演算子と同じです。  |
| [month](data-flow-expression-functions.md#month) | 日付またはタイムスタンプから月の値を取得します。  |
| [monthsBetween](data-flow-expression-functions.md#monthsBetween) | 2 つの日付の間の月数を取得します。 計算を丸めることができいます。省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [multiply](data-flow-expression-functions.md#multiply) | 数値のペアを乗算します。 \* 演算子と同じです。  |
| [negate](data-flow-expression-functions.md#negate) | 数値の符号を反転します。 正の数値を負の数値 (または、その逆) に変換します。  |
| [nextSequence](data-flow-expression-functions.md#nextSequence) | 次の固有なシーケンスを返します。 数値は、パーティション内でのみ連続し、プレフィックスとして partitionId が付加されます。  |
| [normalize](data-flow-expression-functions.md#normalize) | 文字列値を個別にアクセント記号が付いた Unicode 文字に正規化します。  |
| [not](data-flow-expression-functions.md#not) | 論理否定演算子。  |
| [notEquals](data-flow-expression-functions.md#notEquals) | 比較不等価演算子。 != 演算子と同じです。  |
| [notNull](data-flow-expression-functions.md#notNull) | 値が NULL 以外であるかどうかをチェックします。  |
| [null](data-flow-expression-functions.md#null) | NULL 値を返します。 "null" という名前の列がある場合は、関数 `syntax(null())` を使用します。 これを使用する操作はすべて NULL になります。  |
| [or](data-flow-expression-functions.md#or) | 論理 OR 演算子です。 \|\| と同じです。  |
| [pMod](data-flow-expression-functions.md#pMod) | 数値のペアの正の剰余です。  |
| [partitionId](data-flow-expression-functions.md#partitionId) | 入力行が含まれている現在のパーティション ID を返します。  |
| [power](data-flow-expression-functions.md#power) | 数値を別の数値でべき乗します。  |
| [radians](data-flow-expression-functions.md#radians) | 角度をラジアンに変換します。|
| [random](data-flow-expression-functions.md#random) | パーティション内に省略可能なシード値を指定すると、乱数を返します。 シード値は固定値である必要があり、乱数値を生成するために partitionId と組み合わせて使用されます。  |
| [regexExtract](data-flow-expression-functions.md#regexExtract) | 指定された正規表現パターンに一致する部分文字列を抽出します。 最後のパラメーターは、一致グループを識別し、省略すると既定値として 1 が使用されます。 エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  |
| [regexMatch](data-flow-expression-functions.md#regexMatch) | 指定された正規表現パターンに文字列が一致するかどうかをチェックします。 エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  |
| [regexReplace](data-flow-expression-functions.md#regexReplace) | 指定の文字列内の正規表現パターンのすべての出現を別の部分文字列に置換します。エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  |
| [regexSplit](data-flow-expression-functions.md#regexSplit) | 文字列を正規表現に基づく区切り文字に基づいて分割し、文字列の配列を返します。  |
| [replace](data-flow-expression-functions.md#replace) | 指定された文字列内の substring のすべての出現を別の substring に置換します。 最後のパラメーターを省略すると、既定値は空の文字列になります。  |
| [reverse](data-flow-expression-functions.md#reverse) | 文字列を反転します。  |
| [right](data-flow-expression-functions.md#right) | 指定した文字数の部分文字列を右から抽出します。 SUBSTRING(str, LENGTH(str) - n, n) と同じです。  |
| [rlike](data-flow-expression-functions.md#rlike) | 指定された正規表現パターンに文字列が一致するかどうかをチェックします。  |
| [round](data-flow-expression-functions.md#round) | 省略可能な桁数と省略可能な丸めモードで数値を丸めます。 桁数を省略すると、既定値の 0 が使用されます。 モードを省略すると、既定値は ROUND_HALF_UP(5) になります。 丸めの値には以下が含まれます|
| [rpad](data-flow-expression-functions.md#rpad) | 特定の長さになるまで、指定した埋め込み文字で文字列の右側を埋め込みます。 文字列が指定された長さ以上の場合は、その長さまで削除されます。  |
| [rtrim](data-flow-expression-functions.md#rtrim) | 文字列の末尾文字を右から削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  |
| [second](data-flow-expression-functions.md#second) | 日付の秒の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [seconds](data-flow-expression-functions.md#seconds) | ミリ秒単位での秒数の期間。  |
| [sha1](data-flow-expression-functions.md#sha1) | さまざまなプリミティブ データ型の列セットの SHA-1 ハッシュを計算し、40 文字の16 進数の文字列を返します。 行のフィンガープリントを計算するために使用できます。  |
| [sha2](data-flow-expression-functions.md#sha2) | さまざまなプリミティブ データ型の列セットの SHA-2 ハッシュを指定のビット長で計算します。ビット長として指定できるのは、0(256)、224、256、384、512 の値のみです。 行のフィンガープリントを計算するために使用できます。  |
| [sin](data-flow-expression-functions.md#sin) | サイン値を計算します。  |
| [sinh](data-flow-expression-functions.md#sinh) | 双曲線サイン値を計算します。  |
| [soundex](data-flow-expression-functions.md#soundex) | 文字列の ```soundex``` コードを取得します。  |
| [split](data-flow-expression-functions.md#split) | 文字列を区切り文字に基づいて分割し、文字列の配列を返します。  |
| [sqrt](data-flow-expression-functions.md#sqrt) | 数値の平方根を計算します。  |
| [startsWith](data-flow-expression-functions.md#startsWith) | 文字列が指定した文字列で開始しているかどうかをチェックします。  |
| [subDays](data-flow-expression-functions.md#subDays) | 日付またはタイムスタンプから日数を減算します。 日付に対する - 演算子と同じです。  |
| [subMonths](data-flow-expression-functions.md#subMonths) | 日付またはタイムスタンプから月数を減算します。  |
| [substring](data-flow-expression-functions.md#substring) | ある位置から特定の長さの部分文字列を抽出します。 位置は 1 から始まります。 長さを省略すると、既定値は文字列の最後になります。  |
| [tan](data-flow-expression-functions.md#tan) | タンジェント値を計算します。  |
| [tanh](data-flow-expression-functions.md#tanh) | 双曲線タンジェント値を計算します。  |
| [translate](data-flow-expression-functions.md#translate) | 文字列内のある文字セットを別の文字セットで置換します。 文字の置換は 1 対 1 で行われます。  |
| [trim](data-flow-expression-functions.md#trim) | 文字列の先頭文字と末尾文字を削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  |
| [true](data-flow-expression-functions.md#true) | 常に true 値を返します。 "true" という名前の列がある場合は、関数 `syntax(true())` を使用します。  |
| [typeMatch](data-flow-expression-functions.md#typeMatch) | 列の型を照合します。 パターン式でのみ使用できます。数値は short、integer、long、double、float、または decimal 型に一致し、整数は short、integer、long 型に一致し、小数は double、float、decimal 型に一致し、日時は date または timestamp 型に一致します。  |
| [unescape](data-flow-expression-functions.md#unescape) | 形式に従って、文字列を非エスケープします。 使用できる形式のリテラル値は、'json'、'xml'、'ecmascript'、'html'、'java' です。|
| [upper](data-flow-expression-functions.md#upper) | 文字列を大文字にします。  |
| [uuid](data-flow-expression-functions.md#uuid) | 生成された UUID を返します。  |
| [weekOfYear](data-flow-expression-functions.md#weekOfYear) | 指定した日付から、その年の何週目かを取得します。  |
| [weeks](data-flow-expression-functions.md#weeks) | ミリ秒単位での週数の期間。  |
| [xor](data-flow-expression-functions.md#xor) | XOR 論理演算子。 ^ 演算子と同じです。  |
| [year](data-flow-expression-functions.md#year) | 日付の年の値を取得します。  |
|||

## <a name="aggregate-functions"></a>集計関数
次の関数は、集計、ピボット、ピボット解除、ウィンドウ変換でのみ使用できます。

| 集計関数 | タスク |
|----|----|
| [approxDistinctCount](data-flow-expression-functions.md#approxDistinctCount) | 列の個別の値のおおよその集計数を取得します。 省略可能な 2 番目のパラメーターは、推定誤差を制御するためのものです。|
| [avg](data-flow-expression-functions.md#avg) | 列の値の平均を取得します。  |
| [avgIf](data-flow-expression-functions.md#avgIf) | 条件に基づいて、列の値の平均を取得します。  |
| [collect](data-flow-expression-functions.md#collect) | 集計されたグループ内の式のすべての値を配列に収集します。 この処理中に構造体を収集し、別の構造体に変換できます。 項目の数はそのグループの行数と等しくなります。また、null 値を含めることができます。 収集する項目の数は少なくするようにします。  |
| [count](data-flow-expression-functions.md#count) | 値の集計カウントを取得します。 省略可能な列を指定すると、カウントの NULL 値が無視されます。  |
| [countDistinct](data-flow-expression-functions.md#countDistinct) | 列セットの個別値の集計カウントを取得します。  |
| [countIf](data-flow-expression-functions.md#countIf) | 条件に基づいて、値の集計カウントを取得します。 省略可能な列を指定すると、カウントの NULL 値が無視されます。  |
| [covariancePopulation](data-flow-expression-functions.md#covariancePopulation) | 2 つの列の間の母共分散を取得します。  |
| [covariancePopulationIf](data-flow-expression-functions.md#covariancePopulationIf) | 条件に基づいて、2 つの列の間の母共分散を取得します。  |
| [covarianceSample](data-flow-expression-functions.md#covarianceSample) | 2 つの列の標本共分散を取得します。  |
| [covarianceSampleIf](data-flow-expression-functions.md#covarianceSampleIf) | 条件に基づいて、2 つの列の標本共分散を取得します。  |
| [first](data-flow-expression-functions.md#first) | 列グループの最初の値を取得します。 2 番目のパラメーター ignoreNulls を省略すると、false であると見なされます。  |
| [isDistinct](data-flow-expression-functions.md#isDistinct) | 列または列セットが個別かどうかを検出します。 null は個別の値としてカウントされません。|
| [kurtosis](data-flow-expression-functions.md#kurtosis) | 列の尖度を取得します。  |
| [kurtosisIf](data-flow-expression-functions.md#kurtosisIf) | 条件に基づいて、列の尖度を取得します。  |
| [last](data-flow-expression-functions.md#last) | 列グループの最後の値を取得します。 2 番目のパラメーター ignoreNulls を省略すると、false であると見なされます。  |
| [max](data-flow-expression-functions.md#max) | 列の最大値を取得します。  |
| [maxIf](data-flow-expression-functions.md#maxIf) | 条件に基づいて、列の値の最大値を取得します。  |
| [mean](data-flow-expression-functions.md#mean) | 列の値の平均を取得します。 AVG と同じです。  |
| [meanIf](data-flow-expression-functions.md#meanIf) | 条件に基づいて、列の値の平均を取得します。 avgIf と同じです。  |
| [min](data-flow-expression-functions.md#min) | 列の最小値を取得します。  |
| [minIf](data-flow-expression-functions.md#minIf) | 条件に基づいて、列の値の最小値を取得します。  |
| [skewness](data-flow-expression-functions.md#skewness) | 列の歪度を取得します。  |
| [skewnessIf](data-flow-expression-functions.md#skewnessIf) | 条件に基づいて、列の歪度を取得します。  |
| [stddev](data-flow-expression-functions.md#stddev) | 列の標準偏差を取得します。  |
| [stddevIf](data-flow-expression-functions.md#stddevIf) | 条件に基づいて、列の標準偏差を取得します。  |
| [stddevPopulation](data-flow-expression-functions.md#stddevPopulation) | 列の母標準偏差を取得します。  |
| [stddevPopulationIf](data-flow-expression-functions.md#stddevPopulationIf) | 条件に基づいて、列の母標準偏差を取得します。  |
| [stddevSample](data-flow-expression-functions.md#stddevSample) | 列の標本標準偏差を取得します。  |
| [stddevSampleIf](data-flow-expression-functions.md#stddevSampleIf) | 条件に基づいて、列の標本標準偏差を取得します。  |
| [sum](data-flow-expression-functions.md#sum) | 数値列の集計を取得します。  |
| [sumDistinct](data-flow-expression-functions.md#sumDistinct) | 数値列の個別値の集計を取得します。  |
| [sumDistinctIf](data-flow-expression-functions.md#sumDistinctIf) | 条件に基づいて、数値列の集計を取得します。 条件は、任意の列に基づくことができます。  |
| [sumIf](data-flow-expression-functions.md#sumIf) | 条件に基づいて、数値列の集計を取得します。 条件は、任意の列に基づくことができます。  |
| [variance](data-flow-expression-functions.md#variance) | 列の分散を取得します。  |
| [varianceIf](data-flow-expression-functions.md#varianceIf) | 条件に基づいて、列の分散を取得します。  |
| [variancePopulation](data-flow-expression-functions.md#variancePopulation) | 列の母分散を取得します。  |
| [variancePopulationIf](data-flow-expression-functions.md#variancePopulationIf) | 条件に基づいて、列の母分散を取得します。  |
| [varianceSample](data-flow-expression-functions.md#varianceSample) | 列の不偏分散を取得します。  |
| [varianceSampleIf](data-flow-expression-functions.md#varianceSampleIf) | 条件に基づいて、列の不偏分散を取得します。  |
|||

## <a name="array-functions"></a>配列関数
配列関数は、配列であるデータ構造体に対して変換を実行します。 これらには、配列の要素とインデックスをアドレス指定するための特殊なキーワードが含まれます。

* ```#acc``` は、配列を縮小するときに 1 つの出力に含める値を表します。
* ```#index``` は、配列のインデックス番号 ```#index2, #index3 ...``` と共に、現在の配列のインデックスを表します。
* ```#item``` は、配列内の現在の要素の値を表します。

| 配列関数 | タスク |
|----|----|
| [array](data-flow-expression-functions.md#array) | 項目の配列を作成します。 すべての項目は同じ型になっている必要があります。 項目が指定されていない場合、既定値は空の文字列配列です。 [] 作成演算子と同じです。  |
| [at](data-flow-expression-functions.md#at) | 配列インデックスにある要素を検索します。 インデックスは 1 から始まっています。 境界外インデックスは、null 値の結果になります。 キーを指定してマップ内の値を検索します。 キーが見つからない場合は、null が返されます。|
| [contains](data-flow-expression-functions.md#contains) | 指定された配列内のいずれかの要素が、指定された述語で true と評価される場合に true を返します。 contains は、述語関数の 1 つの要素への参照を #item として予期します。  |
| [distinct](data-flow-expression-functions.md#distinct) | 配列から個別の項目セットを返します。|
| [except](data-flow-expression-functions.md#except) | 重複をドロップした 1 つの配列の差分セットを返します。|
| [filter](data-flow-expression-functions.md#filter) | 指定された述語を満たさない要素を配列から除外します。 filter は、述語関数の 1 つの要素への参照を #item として予期します。  |
| [find](data-flow-expression-functions.md#find) | 配列から条件に一致する最初の項目を検索します。 フィルター関数が使用されます。この関数では、配列内の項目を #item としてアドレス指定できます。 入れ子になった深いマップの場合は、#item_n (#item_1, #item_2...) 表記を使用して親マップを参照できます。  |
| [flatten](data-flow-expression-functions.md#flatten) | 1 つまたは複数の配列を単一の配列にフラット化します。 アトミック項目の配列は、変更されずに返されます。 最後の引数は省略可能で、既定値は false です。1 レベルを超える深さは再帰的にフラット化されます。|
| [in](data-flow-expression-functions.md#in) | 項目が配列内にあるかどうかをチェックします。  |
| [intersect](data-flow-expression-functions.md#intersect) | 2 つの配列からの、個別の項目の交差セットを返します。|
| [map](data-flow-expression-functions.md#map) | 指定された式を使用して、配列の各要素を新しい要素にマップします。 map は、式関数の 1 つの要素への参照を #item として予期します。  |
| [mapIf](data-flow-expression-functions.md#mapIf) | 配列を、同じ長さまたは短い長さの別の配列に条件付きでマップします。 値は、structTypes など、任意のデータ型とすることができます。 配列内のアイテムを #item として、現在のインデックスを #index としてアドレス指定できるマッピング関数を取ります。 深く入れ子になったマップの場合、``#item_[n](#item_1, #index_1...)`` という表記を使用して親マップを参照できます。|
| [mapIndex](data-flow-expression-functions.md#mapIndex) | 指定された式を使用して、配列の各要素を新しい要素にマップします。 map は、式関数内の 1 つの要素への参照を #item として予期し、要素インデックスへの参照を #index として予期します。  |
| [mapLoop](data-flow-expression-functions.md#mapLoop) | 1 から length までループさせてその長さの配列を作成します。 これは、配列内のインデックスを #index としてアドレス指定できるマッピング関数を取ります。 深く入れ子になったマップの場合、#index_n(#index_1, #index_2...) という表記を使用して親マップを参照できます。|
| [reduce](data-flow-expression-functions.md#reduce) | 配列内の要素を累積します。 reduce は、最初の式関数のアキュムレータと 1 つの要素への参照を #acc および #item として予期し、結果の値を 2 番目の式関数で使用される #result として予期します。  |
| [size](data-flow-expression-functions.md#size) | 配列またはマップの種類のサイズを見つけます  |
| [slice](data-flow-expression-functions.md#slice) | ある位置から配列のサブセットを抽出します。 位置は 1 から始まります。 長さを省略すると、既定値は文字列の最後になります。  |
| [sort](data-flow-expression-functions.md#sort) | 指定された述語関数を使用して配列を並べ替えます。 sort は、式関数の 2 つの連続する要素への参照を #item1 および #item2 として予期します。  |
| [unfold](data-flow-expression-functions.md#unfold) | 配列を一連の行に展開し、すべての行内の残りの列に値を繰り返します。|
| [union](data-flow-expression-functions.md#union) | 2 つの配列からの個別の項目の和集合を返します。|
|||

## <a name="cached-lookup-functions"></a>キャッシュされた Lookup 関数
次の関数は、キャッシュされたシンクを含めているときに、キャッシュされた検索を使用する場合にのみ使用できます。

| キャッシュされた lookup 関数 | タスク |
|----|----|
| [lookup](data-flow-expression-functions.md#lookup) | キャッシュされたシンクのキーと一致する特定のキーを使用して、キャッシュされたシンクの最初の行が検索されます。|
| [mlookup](data-flow-expression-functions.md#mlookup) | キャッシュされたシンクのキーと一致する特定のキーを使用して、キャッシュされたシンクの一致するすべての行が検索されます。|
| [output](data-flow-expression-functions.md#output) | キャッシュ シンクの結果の最初の行を返します。|
| [outputs](data-flow-expression-functions.md#outputs) | キャッシュ シンクの結果の出力行セット全体を返します。|
|||

## <a name="conversion-functions"></a>変換関数

変換関数は、データの変換とデータ型のテストに使用されます

| 変換関数 | タスク |
|----|----|
| [isBitSet](data-flow-expression-functions.md#isBitSet) | ビット位置がこのビットセットに設定されているかどうかを確認します。|
| [setBitSet](data-flow-expression-functions.md#setBitSet) | このビットセットにビット位置を設定します。|
| [isBoolean](data-flow-expression-functions.md#isBoolean) | 文字列値が ``toBoolean()`` の規則に従ったブール値であるかどうかを確認します  |
| [isByte](data-flow-expression-functions.md#isByte) | 文字列値が ``toByte()`` の規則に従った省略可能な形式が指定されたバイト値であるかどうかを確認します |
| [isDate](data-flow-expression-functions.md#isDate) | 入力日付文字列が、省略可能な入力日付形式を使用した日付であるかどうかを確認します。 使用可能な形式については、Java の SimpleDateFormat を参照してください。 入力日付形式が省略されている場合、既定の形式は ``yyyy-[M]M-[d]d`` になります。 許容される形式は ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]`` です |
| [isShort](data-flow-expression-functions.md#isShort) | 文字列値が ``toShort()`` の規則に従った省略可能な形式が指定された 短整数型値であることを確認します |
| [isInteger](data-flow-expression-functions.md#isInteger) | 文字列値が ``toInteger()`` の規則に従った省略可能な形式が指定された整数値であることを確認します |
| [isLong](data-flow-expression-functions.md#isLong) | 文字列値が ``toLong()`` の規則に従った省略可能な形式が指定された 長整数型値であることを確認します |
| [isNan](data-flow-expression-functions.md#isNan) | これが数値でないことを確認します。|
| [isFloat](data-flow-expression-functions.md#isFloat) | 文字列値が ``toFloat()`` の規則に従った省略可能な形式が指定された浮動小数点値であることを確認します |
| [isDouble](data-flow-expression-functions.md#isDouble) | 文字列値が ``toDouble()`` の規則に従った省略可能な形式が指定された倍精度値であることを確認します |
| [isDecimal](data-flow-expression-functions.md#isDecimal) | 文字列値が ``toDecimal()`` の規則に従った省略可能な形式が指定された 10 進数値であることを確認します |
| [isTimestamp](data-flow-expression-functions.md#isTimestamp) | 入力日付文字列が、省略可能な入力タイムスタンプ形式を使用したタイムスタンプであるかどうかを確認します。 使用可能な形式については、Java の SimpleDateFormat を参照してください。 タイムスタンプを省略すると、既定のパターンの ``yyyy-[M]M-[d]d hh:mm:ss[.f...]`` が使用されます。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 タイムスタンプはミリ秒の精度で 999 の値までサポートされます。使用可能な形式については、Java の SimpleDateFormat を参照してください。|
| [toBase64](data-flow-expression-functions.md#toBase64) | 指定された文字列を base64 でエンコードします。  |
| [toBinary](data-flow-expression-functions.md#toBinary) | 任意の数値、日付、タイムスタンプ、文字列をバイナリ表現に変換します。  |
| [toBoolean](data-flow-expression-functions.md#toBoolean) | ('t'、'true'、'y'、'yes'、'1') の値を true に変換し、('f'、'false'、'n'、'no'、'0') の値を false に変換し、それ以外の値を NULL に変換します。  |
| [toByte](data-flow-expression-functions.md#toByte) | 任意の数値または文字列をバイト値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。  |
| [toDate](data-flow-expression-functions.md#toDate) | 省略可能な入力日付形式を使用して、入力日付文字列を日付に変換します。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 入力日付形式が省略されている場合、既定の形式は yyyy-[M]M-[d]d になります。 許容される形式は [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ] です。  |
| [toDecimal](data-flow-expression-functions.md#toDecimal) | 任意の数値または文字列を decimal 型の値に変換します。 有効桁数と小数点以下桁数を指定しない場合、既定値の (10,2) が使用されます。変換には、任意指定の Java 10 進数形式を使用できます。 en-US、de、zh-CN など、BCP47 言語形式に属する省略可能なロケール形式。  |
| [toDouble](data-flow-expression-functions.md#toDouble) | 任意の数値または文字列を double 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 en-US、de、zh-CN など、BCP47 言語形式に属する省略可能なロケール形式。  |
| [toFloat](data-flow-expression-functions.md#toFloat) | 任意の数値または文字列を float 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 double を切り捨てます。  |
| [toInteger](data-flow-expression-functions.md#toInteger) | 任意の数値または文字列を integer 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 long、float、double を切り捨てます。  |
| [toLong](data-flow-expression-functions.md#toLong) | 任意の数値または文字列を long 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 float、double を切り捨てます。  |
| [toShort](data-flow-expression-functions.md#toShort) | 任意の数値または文字列を short 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 integer、long、float、double を切り捨てます。  |
| [toString](data-flow-expression-functions.md#toString) | プリミティブ データ型を文字列に変換します。 数値と日付の場合は、形式を指定できます。 指定しない場合、システムの既定値が選択されます。数値には、Java の 10 進数形式が使用されます。 使用できるすべての日付形式については、Java SimpleDateFormat を参照してください。既定の形式は yyyy-MM-dd です。  |
| [toTimestamp](data-flow-expression-functions.md#toTimestamp) | 省略可能なタイムスタンプ形式を指定して、文字列をタイムスタンプに変換します。 タイムスタンプを省略すると、既定のパターンの yyyy-[M]M-[d]d hh:mm:ss[.f...] が使用されます。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 タイムスタンプでサポートされる最大精度は、ミリ秒 (値 999) です。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [toUTC](data-flow-expression-functions.md#toUTC) | タイムスタンプを UTC に変換します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
|||

## <a name="map-functions"></a>マップ関数

  マップ関数は、マップ データ型に対して操作を実行します

| マップ関数 | タスク |
|----|----|
| [associate](data-flow-expression-functions.md#associate) | キーと値のマップを作成します。 すべてのキーと値の組み合わせは同じ型である必要があります。 項目が指定されていない場合、既定では文字列型へのマップが使用されます。```[ -> ]``` 作成演算子と同じです。 キーと値は互いに交互である必要があります。|
| [keyValues](data-flow-expression-functions.md#keyValues) | キーと値のマップを作成します。 最初のパラメーターはキーの配列、2 番目のパラメーターは値の配列です。 両方の配列の長さは等しい必要があります。|
| [mapAssociation](data-flow-expression-functions.md#mapAssociation) | キーを新しい値に関連付け、マップを変換します。 配列を返します。 項目を #key として、現在の値を #value としてアドレス指定できるマッピング関数を取得します。 |
| [reassociate](data-flow-expression-functions.md#reassociate) | キーを新しい値に関連付け、マップを変換します。 項目を #key として、現在の値を #value としてアドレス指定できるマッピング関数を取得します。  |
|||

## <a name="metafunctions"></a>メタ関数

メタ関数は、主にデータ フロー内のメタデータに対して機能します。

| メタ関数  | タスク |
|----|----|
| [byItem](data-flow-expression-functions.md#byItem) | 構造体または構造体の配列内のサブ項目を検索します。複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換アクション (? date、? string など) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます  |
| [byOrigin](data-flow-expression-functions.md#byOrigin) | 元のストリームでの名前で列の値を選択します。 2 番目の引数は元のストリームの名前です。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  |
| [byOrigins](data-flow-expression-functions.md#byOrigins) | ストリーム内の名前を指定して列の配列を選択します。 2 番目の引数は発生したストリームです。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。|
| [byName](data-flow-expression-functions.md#byName) | ストリームでの名前で列の値を選択します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  |
| [byNames](data-flow-expression-functions.md#byNames) | ストリーム内の名前を指定して列の配列を選択します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 複数の一致がある場合は、最初の一致が返されます。 列に一致するものがない場合は、出力全体が NULL 値になります。 戻り値には、型変換関数 (toDate、toString、...) が必要です。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。|
| [byPath](data-flow-expression-functions.md#byPath) | ストリームでの名前で階層パスを見つけます。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 そのようなパスが見つからない場合は、null 値が返されます。 設計時に判明している列名やパスは、その名前またはドット表記パスだけで扱う必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  |
| [byPosition](data-flow-expression-functions.md#byPosition) | ストリーム内の相対位置 (1 から始まる) で列の値を選択します。 位置が範囲外にある場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります計算入力はサポートされていませんが、パラメーター置換を使用することができます。  |
| [hasPath](data-flow-expression-functions.md#hasPath) | ストリームでの名前で特定の階層パスが存在するかどうかを確認します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 設計時に判明している列名やパスは、その名前またはドット表記パスだけで扱う必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  |
| [originColumns](data-flow-expression-functions.md#originColumns) | 列が作成された配信元ストリームのすべての出力列を取得します。 別の関数内で囲む必要があります。|
| [hex](data-flow-expression-functions.md#hex) | バイナリ値の 16 進数文字列表記を返します |
| [unhex](data-flow-expression-functions.md#unhex) | バイナリ値を 16 進数文字列表記から数値に変換します。 これは、文字列からバイナリ表記に変換するために、sha2、md5 と組み合わせて使用できます |
|||

## <a name="window-functions"></a>ウィンドウ関数

次の関数は、ウィンドウ変換でのみ使用できます。

| ウィンドウ関数 | タスク |
|----|----|
| [cumeDist](data-flow-expression-functions.md#cumeDist) | CumeDist 関数は、パーティション内のすべての値の相対値を計算します。 結果は、パーティションの順序内で現在行以前の行数を、ウィンドウ パーティション内の合計行数で除算した値です。 同順位の値は、同じ位置に評価されます。  |
| [denseRank](data-flow-expression-functions.md#denseRank) | ウィンドウの order by 句で指定した値のグループ内の値の順位を計算します。 結果は、パーティション順位内の現在行以前の行数に 1 を加算した値です。 値では、シーケンス内にギャップは生じません。 Dense Rank は、データが並べ替えられていない場合や、値の変更が予測される場合であっても機能します。  |
| [lag](data-flow-expression-functions.md#lag) | 現在の行の n 行前を評価した最初のパラメーターの値を取得します。 2 番目のパラメーターは、戻る行の数です。既定値は 1 です。 指定した数の行がない場合、既定値が指定されていない限り、null 値が返されます。  |
| [lead](data-flow-expression-functions.md#lead) | 現在の行の n 行後を評価した最初のパラメーターの値を取得します。 2 番目のパラメーターは、進む行の数です。既定値は 1 です。 指定した数の行がない場合、既定値が指定されていない限り、null 値が返されます。  |
| [nTile](data-flow-expression-functions.md#nTile) | ```NTile``` 関数は、各ウィンドウ パーティションの行を `n` バケット (1 から最大 `n`) に分割します。 バケットの値の差は最大で 1 です。 パーティション内の行数がバケット数に対して均等に分割されない場合、残りの値は、最初のバケットから順番にバケットごとに 1 つずつ分配されます。 ```NTile``` 関数は、```tertiles```、四分位数、十分位数およびその他の一般的な集計統計情報を計算する場合に便利です。 この関数は、初期化中に次の 2 つの変数を計算します。通常のバケットのサイズには、さらに 1 行が追加されます。 どちらの変数も現在のパーティションのサイズに基づいています。 計算プロセス中に、この関数は現在の行番号、現在のバケット番号、およびバケットが変更される行番号 (bucketThreshold) を追跡します。 現在の行番号がバケットしきい値に達すると、バケットの値が 1 つ増加し、しきい値にはバケット サイズが追加されます (現在のバケットが埋め込まれている場合は、さらに 1 が追加されます)。  |
| [rank](data-flow-expression-functions.md#rank) | ウィンドウの order by 句で指定した値のグループ内の値の順位を計算します。 結果は、パーティション順位内の現在行以前の行数に 1 を加算した値です。 値では、シーケンス内にギャップが生じます。 Rank は、データが並べ替えられていない場合や、値の変更が予測される場合でも機能します。  |
| [rowNumber](data-flow-expression-functions.md#rowNumber) | ウィンドウ内の行のシーケンシャル行番号を 1 から順番に割り当てます。  |
|||

## <a name="alphabetical-listing-of-all-functions"></a>すべての関数のアルファベット順の一覧

以下に、マッピング データ フローで使用できるすべての関数のアルファベット順の一覧を示します。

<a name="abs" ></a>

### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値の絶対値。  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   


<a name="acos" ></a>

### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆コサイン値を計算します。  
* ``acos(1) -> 0.0``  
___


<a name="add" ></a>

### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
文字列または数値のペアを追加します。 日付に日数を加算します。 タイムスタンプに期間を追加します。 別の配列に類似するタイプの配列を追加します。 \+ 演算子と同じです。  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="addDays" ></a>

### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプに日数を加算します。 日付に対する + 演算子と同じです。  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___


<a name="addMonths" ></a>

### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
日付またはタイムスタンプに月数を加算します。 必要に応じて、タイムゾーンを渡すことができます。  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___


<a name="and" ></a>

### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
論理 AND 演算子です。 && と同じです。  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___


<a name="approxDistinctCount" ></a>

### <code>approxDistinctCount</code>
<code><b>approxDistinctCount(<i>&lt;value1&gt;</i> : any, [ <i>&lt;value2&gt;</i> : double ]) => long</b></code><br/><br/>
列の個別の値のおおよその集計数を取得します。 省略可能な 2 番目のパラメーターは、推定誤差を制御するためのものです。
* ``approxDistinctCount(ProductID, .05) => long``  
___


<a name="array" ></a>

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
項目の配列を作成します。 すべての項目は同じ型になっている必要があります。 項目が指定されていない場合、既定値は空の文字列配列です。 [] 作成演算子と同じです。  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___


<a name="asin" ></a>

### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆サイン値を計算します。  
* ``asin(0) -> 0.0``  
___


<a name="associate" ></a>

### <code>associate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
キーと値のマップを作成します。 すべてのキーと値の組み合わせは同じ型である必要があります。 項目が指定されていない場合、既定では文字列型へのマップが使用されます。```[ -> ]``` 作成演算子と同じです。 キーと値は互いに交互である必要があります。
*   ``associate('fruit', 'apple', 'vegetable', 'carrot' )=> ['fruit' -> 'apple', 'vegetable' -> 'carrot']``
___


<a name="at" ></a>

### <code>at</code>
<code><b>at(<i>&lt;value1&gt;</i> : array/map, <i>&lt;value2&gt;</i> : integer/key type) => array</b></code><br/><br/>
配列インデックスにある要素を検索します。 インデックスは 1 から始まっています。 境界外インデックスは、null 値の結果になります。 キーを指定してマップ内の値を検索します。 キーが見つからない場合は、null が返されます。
*   ``at(['apples', 'pears'], 1) => 'apples'``
*   ``at(['fruit' -> 'apples', 'vegetable' -> 'carrot'], 'fruit') => 'apples'``
___


<a name="atan" ></a>

### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆タンジェント値を計算します。  
* ``atan(0) -> 0.0``  
___


<a name="atan2" ></a>

### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
座標で指定された、平面の正の x 軸と点の間の角度をラジアンで返します。  
* ``atan2(0, 0) -> 0.0``  
___


<a name="avg" ></a>

### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
列の値の平均を取得します。  
* ``avg(sales)``  
___


<a name="avgIf" ></a>

### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、列の値の平均を取得します。  
* ``avgIf(region == 'West', sales)``  
___


<a name="between" ></a>

### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
最初の値が他の 2 つの値の範囲内にあるかどうかを確認します。 数値、文字列値、datetime 値を比較できます * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___


<a name="bitwiseAnd" ></a>

### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
整数型間でのビット AND 演算子です。 & 演算子と同じです * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___


<a name="bitwiseOr" ></a>

### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
整数型間でのビット OR 演算子です。 | 演算子と同じです * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___


<a name="bitwiseXor" ></a>

### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
整数型間でのビット OR 演算子です。 | 演算子と同じです * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___


<a name="blake2b" ></a>

### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの Blake2 ハッシュを計算します。ビット長として指定できるのは、8 から 512 の範囲内の 8 の倍数のみです。 行のフィンガープリントを計算するために使用できます * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___


<a name="blake2bBinary" ></a>

### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの Blake2 ハッシュを計算します。ビット長として指定できるのは、8 から 512 の範囲内の 8 の倍数のみです。 行のフィンガープリントを計算するために使用できます * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___


<a name="byItem" ></a>

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
構造体または構造体の配列内のサブ項目を検索します。複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換アクション (? date、? string など) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
___


<a name="byName" ></a>

### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
ストリームでの名前で列の値を選択します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___


<a name="byNames" ></a>

### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
ストリーム内の名前を指定して列の配列を選択します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 複数の一致がある場合は、最初の一致が返されます。 列に一致するものがない場合は、出力全体が NULL 値になります。 戻り値には、型変換関数 (toDate、toString、...) が必要です。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___


<a name="byOrigin" ></a>

### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
元のストリームでの名前で列の値を選択します。 2 番目の引数は元のストリームの名前です。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___


<a name="byOrigins" ></a>

### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
ストリーム内の名前を指定して列の配列を選択します。 2 番目の引数は発生したストリームです。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___


<a name="byPath" ></a>

### <code>byPath</code>
<code><b>byPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => any</b></code><br/><br/>
ストリームでの名前で階層パスを見つけます。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 そのようなパスが見つからない場合は、null 値が返されます。 設計時に判明している列名やパスは、その名前またはドット表記パスだけで扱う必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``byPath('grandpa.parent.child') => column`` 
___


<a name="byPosition" ></a>

### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
ストリーム内の相対位置 (1 から始まる) で列の値を選択します。 位置が範囲外にある場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  
___


<a name="case" ></a>

### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
交互条件に基づいて、2 つの値のいずれかを適用します。 入力数が偶数の場合、最後の条件でその他は既定で NULL になります。  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___


<a name="cbrt" ></a>

### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
数値の立方根を計算します。  
* ``cbrt(8) -> 2.0``  
___


<a name="ceil" ></a>

### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
特定の数値以上の最小の整数を返します。  
* ``ceil(-0.1) -> 0``  
___


<a name="coalesce" ></a>

### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
一連の入力から、最初の null でない値を返します。 すべての入力は同じ型である必要があります。  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___


<a name="collect" ></a>

### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
集計されたグループ内の式のすべての値を配列に収集します。 この処理中に構造体を収集し、別の構造体に変換できます。 項目の数はそのグループの行数と等しくなります。また、null 値を含めることができます。 収集する項目の数は少なくするようにします。  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___


<a name="columnNames" ></a>

### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
ストリームのすべての出力列の名前を取得します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。  
* ``columnNames()``
* ``columnNames('DeriveStream')``
___


<a name="columns" ></a>

### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
ストリームのすべての出力列の値を取得します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。   
* ``columns()``
* ``columns('DeriveStream')``
___


<a name="compare" ></a>

### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
同じ型の 2 つの値を比較します。 value1 < value2 の場合は負の整数、value1 == value2 の場合は 0、value1 > value2 の場合は正の値を返します。  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___


<a name="concat" ></a>

### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
可変数の文字列を連結します。 文字列で使用する + 演算子と同じです。  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___


<a name="concatWS" ></a>

### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
可変数の文字列を区切り記号を使用して連結します。 最初のパラメーターは区切り記号です。  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___


<a name="contains" ></a>

### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
指定された配列内のいずれかの要素が、指定された述語で true と評価される場合に true を返します。 contains は、述語関数の 1 つの要素への参照を #item として予期します。  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___


<a name="cos" ></a>

### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
コサイン値を計算します。  
* ``cos(10) -> -0.8390715290764524``  
___


<a name="cosh" ></a>

### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
値の双曲線コサインを計算します。  
* ``cosh(0) -> 1.0``  
___


<a name="count" ></a>

### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
値の集計カウントを取得します。 省略可能な列を指定すると、カウントの NULL 値が無視されます。  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___


<a name="countDistinct" ></a>

### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
列セットの個別値の集計カウントを取得します。  
* ``countDistinct(custId, custName)``  
___


<a name="countIf" ></a>

### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
条件に基づいて、値の集計カウントを取得します。 省略可能な列を指定すると、カウントの NULL 値が無視されます。  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___


<a name="covariancePopulation" ></a>

### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
2 つの列の間の母共分散を取得します。  
* ``covariancePopulation(sales, profit)``  
___


<a name="covariancePopulationIf" ></a>

### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、2 つの列の間の母共分散を取得します。  
* ``covariancePopulationIf(region == 'West', sales)``  
___


<a name="covarianceSample" ></a>

### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
2 つの列の標本共分散を取得します。  
* ``covarianceSample(sales, profit)``  
___


<a name="covarianceSampleIf" ></a>

### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、2 つの列の標本共分散を取得します。  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___



<a name="crc32" ></a>

### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの CRC32 ハッシュを指定のビット長で計算します。ビット長として指定できるのは、0(256)、224、256、384、512 の値のみです。 行のフィンガープリントを計算するために使用できます。  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___


<a name="cumeDist" ></a>

### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 関数は、パーティション内のすべての値の相対値を計算します。 結果は、パーティションの順序内で現在行以前の行数を、ウィンドウ パーティション内の合計行数で除算した値です。 同順位の値は、同じ位置に評価されます。  
* ``cumeDist()``  
___


<a name="currentDate" ></a>

### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
このジョブの実行を開始する現在の日付を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___


<a name="currentTimestamp" ></a>

### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
ジョブの実行を開始する現在のタイムスタンプをローカル タイムゾーンを使用して取得します。  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___


<a name="currentUTC" ></a>

### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
現在のタイムスタンプを UTC で取得します。 現在の時刻がクラスターのタイム ゾーンとは異なるタイムゾーンで解釈されるようにする場合は、省略可能なタイム ゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡すことができます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). UTC 時刻を別のタイムゾーンに変換するには、`fromUTC()` を使用します。  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="dayOfMonth" ></a>

### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その月の何日目かを取得します。  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___


<a name="dayOfWeek" ></a>

### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、曜日を取得します。 1 - 日曜日、2 - 月曜日...、7 - 土曜日。  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___


<a name="dayOfYear" ></a>

### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その年の何日目かを取得します。  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___


<a name="days" ></a>

### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での日数の期間。  
* ``days(2) -> 172800000L``  
___


<a name="degrees" ></a>

### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
ラジアンを角度に変換します。  
* ``degrees(3.141592653589793) -> 180``  
___


<a name="denseRank" ></a>

### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
ウィンドウの order by 句で指定した値のグループ内の値の順位を計算します。 結果は、パーティション順位内の現在行以前の行数に 1 を加算した値です。 値では、シーケンス内にギャップは生じません。 Dense Rank は、データが並べ替えられていない場合や、値の変更が予測される場合であっても機能します。  
* ``denseRank()``  
___


<a name="distinct" ></a>

### <code>distinct</code>
<code><b>distinct(<i>&lt;value1&gt;</i> : array) => array</b></code><br/><br/>
配列から個別の項目セットを返します。
* ``distinct([10, 20, 30, 10]) => [10, 20, 30]``
___


<a name="divide" ></a>

### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアを除算します。 `/` 演算子と同じです。  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``
___


<a name="dropLeft" ></a>

### <code>dropLeft</code>
<code><b>dropLeft(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
文字列の左側から任意の文字数の文字を削除します。 要求されたドロップが文字列の長さを超える場合は、空の文字列が返されます。
*   dropLeft('bojjus', 2) => 'jjus' *   dropLeft('cake', 10) => '' ___


<a name="dropRight" ></a>

### <code>dropRight</code>
<code><b>dropRight(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
文字列の右側から任意の文字数の文字を削除します。 要求されたドロップが文字列の長さを超える場合は、空の文字列が返されます。
*   dropRight('bojjus', 2) => 'bojj' *   dropRight('cake', 10) => '' ___


<a name="endsWith" ></a>

### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
文字列が指定した文字列で終了しているかをチェックします。  
* ``endsWith('dumbo', 'mbo') -> true``  
___


<a name="equals" ></a>

### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
等価比較演算子。 == 演算子と同じです。  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___


<a name="equalsIgnoreCase" ></a>

### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
大文字と小文字の区別を無視する等価比較演算子。 <=> 演算子と同じです。  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___


<a name="escape" ></a>

### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
形式に従って、文字列をエスケープします。 使用できる形式のリテラル値は、'json'、'xml'、'ecmascript'、'html'、'java' です。
___


<a name="except" ></a>

### <code>except</code>
<code><b>except(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
重複をドロップした 1 つの配列の差分セットを返します。
* ``except([10, 20, 30], [20, 40]) => [10, 30]``  
___


<a name="expr" ></a>

### <code>expr</code>
<code><b>expr(<i>&lt;expr&gt;</i> : string) => any</b></code><br/><br/>
結果は文字列からの式です。 これは、非リテラル形式でこの式を記述することと同じです。 これは、文字列表現としてパラメーターを渡すために使用できます。
*    expr('price * discount') => any ___


<a name="factorial" ></a>

### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
数値の階乗を計算します。  
* ``factorial(5) -> 120``  
___


<a name="false" ></a>

### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
常に false 値を返します。 'false' という名前の列がある場合は、関数 `syntax(false())` を使用します。  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___


<a name="filter" ></a>

### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
指定された述語を満たさない要素を配列から除外します。 filter は、述語関数の 1 つの要素への参照を #item として予期します。  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___


<a name="find" ></a>

### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
配列から条件に一致する最初の項目を検索します。 フィルター関数が使用されます。この関数では、配列内の項目を #item としてアドレス指定できます。 入れ子になった深いマップの場合は、#item_n (#item_1, #item_2...) 表記を使用して親マップを参照できます。  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___


<a name="first" ></a>* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___


<a name="first" ></a>

### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
列グループの最初の値を取得します。 2 番目のパラメーター ignoreNulls を省略すると、false であると見なされます。  
* ``first(sales)``  
* ``first(sales, false)``  
___



<a name="flatten" ></a>

### <code>flatten</code>
<code><b>flatten(<i>&lt;array&gt;</i> : array, <i>&lt;value2&gt;</i> : array ..., <i>&lt;value2&gt;</i> : boolean) => array</b></code><br/><br/>
1 つまたは複数の配列を単一の配列にフラット化します。 アトミック項目の配列は、変更されずに返されます。 最後の引数は省略可能で、既定値は false です。1 レベルを超える深さは再帰的にフラット化されます。
*   ``flatten([['bojjus', 'girl'], ['gunchus', 'boy']]) => ['bojjus', 'girl', 'gunchus', 'boy']``
*   ``flatten([[['bojjus', 'gunchus']]] , true) => ['bojjus', 'gunchus']``
___


<a name="floor" ></a>

### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
特定の数値以下の最大の整数を返します。  
* ``floor(-0.1) -> -1``  
___


<a name="fromBase64" ></a>

### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
指定した base64 エンコード文字列をデコードします。
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___


<a name="fromUTC" ></a>

### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
UTC からタイムスタンプに変換します。 必要に応じて、タイムゾーンを "GMT"、"PST"、"UTC"、"America/Cayman" の形式で渡すことができます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="greater" ></a>

### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較超過演算子。 > 演算子と同じです。  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="greaterOrEqual" ></a>

### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較 (以上) 演算子。 >= 演算子と同じです。  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___


<a name="greatest" ></a>

### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
null 値をスキップした入力値のリストの中の最大値を返します。 すべての入力が null の場合は null を返します。  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="hasColumn" ></a>

### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
ストリームでの名前で列の値をチェックします。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``hasColumn('parent')``  
___


<a name="hasPath" ></a>

### <code>hasPath</code>
<code><b>hasPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => boolean</b></code><br/><br/>
ストリームでの名前で特定の階層パスが存在するかどうかを確認します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 設計時に判明している列名やパスは、その名前またはドット表記パスだけで扱う必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``hasPath('grandpa.parent.child') => boolean``
___  


<a name="hex" ></a>

### <code>hex</code>
<code><b>hex(<i>\<value1\></i>: binary) => string</b></code><br/><br/>
バイナリ値の 16 進数文字列表記を返します * ``hex(toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])) -> '1fadbe'``
___


<a name="hour" ></a>

### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
タイムスタンプから時間の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___


<a name="hours" ></a>

### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での時間数の期間。  
* ``hours(2) -> 7200000L``  
___


<a name="iif" ></a>

### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
条件に基づいて、2 つの値のいずれかを適用します。 その他が指定されていない場合は、NULL と見なされます。 両方の値は互換性がなければなりません (数値、文字列...)。* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___


<a name="iifNull" ></a>

### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
最初のパラメーターが null かどうかを確認します。 null 以外の場合は、最初のパラメーターが返されます。 null の場合は、2 番目のパラメーターが返されます。 3 つのパラメーターが指定されている場合、動作は iif(isNull(value1), value2, value3) と同じであり、最初の値が null でない場合は 3 番目のパラメーターが返されます。  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___


<a name="in" ></a>

### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
項目が配列内にあるかどうかをチェックします。  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___


<a name="initCap" ></a>

### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
すべての単語の最初の文字を大文字に変換します。 単語は、空白文字で区切られているものとして識別されます。  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___


<a name="instr" ></a>

### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
文字列内の部分文字列の位置 (1 を基準とする) を見つけます。 見つからない場合は 0 が返されます。  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___


<a name="intersect" ></a>

### <code>intersect</code>
<code><b>intersect(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
2 つの配列からの、個別の項目の交差セットを返します。
* ``intersect([10, 20, 30], [20, 40]) => [20]``  
___


<a name="isBitSet" ></a>

### <code>isBitSet</code>
<code><b>isBitSet (<i><i>\<value1\></i></i> : array, <i>\<value2\></i>:integer ) => boolean</b></code><br/><br/>
ビット位置がこのビットセットに設定されているかどうかを確認します。* ``isBitSet(toBitSet([10, 32, 98]), 10) => true``
___


<a name="isBoolean" ></a>

### <code>isBoolean</code>
<code><b>isBoolean(<i>\<value1\></i>: string) => boolean</b></code><br/><br/>
文字列値が ``toBoolean()`` の規則に従ったブール値であるかどうかを確認します 
* ``isBoolean('true') -> true``
* ``isBoolean('no') -> true``
* ``isBoolean('microsoft') -> false``
___


<a name="isByte" ></a> 

### <code>isByte</code>
<code><b>isByte(<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
文字列値が ``toByte()`` の規則に従った省略可能な形式が指定されたバイト値であるかどうかを確認します 
* ``isByte('123') -> true``
* ``isByte('chocolate') -> false``
___


<a name="isDate" ></a>

### <code>isDate</code>
<code><b>isDate (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
入力日付文字列が、省略可能な入力日付形式を使用した日付であるかどうかを確認します。 使用可能な形式については、Java の SimpleDateFormat を参照してください。 入力日付形式が省略されている場合、既定の形式は ``yyyy-[M]M-[d]d`` になります。 許容される形式は ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]`` です 
* ``isDate('2012-8-18') -> true``
* ``isDate('12/18--234234' -> 'MM/dd/yyyy') -> false``
___


<a name="isDecimal" ></a>

### <code>isDecimal</code>
<code><b>isDecimal (<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
文字列値が ``toDecimal()`` の規則に従った省略可能な形式が指定された 10 進数値であることを確認します 
* ``isDecimal('123.45') -> true``
* ``isDecimal('12/12/2000') -> false``
___


<a name="isDelete" ></a>

### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が削除用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isDelete()``  
* ``isDelete(1)``  
___


<a name="isDistinct" ></a>

### <code>isDistinct</code>
<code><b>isDistinct(<i>&lt;value1&gt;</i> : any , <i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
列または列セットが個別かどうかを検出します。 null は個別の値としてカウントされません。*    ``isDistinct(custId, custName) => boolean``
___



<a name="isDouble" ></a>

### <code>isDouble</code>
<code><b>isDouble (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
文字列値が ``toDouble()`` の規則に従った省略可能な形式が指定された倍精度値であることを確認します 
* ``isDouble('123') -> true``
* ``isDouble('$123.45' -> '$###.00') -> true``
* ``isDouble('icecream') -> false``
___


<a name="isError" ></a>

### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行がエラーとしてマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isError()``  
* ``isError(1)``  
___


<a name="isFloat" ></a>

### <code>isFloat</code>
<code><b>isFloat (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
文字列値が ``toFloat()`` の規則に従った省略可能な形式が指定された浮動小数点値であることを確認します 
* ``isFloat('123') -> true``
* ``isFloat('$123.45' -> '$###.00') -> true``
* ``isFloat('icecream') -> false``
___


<a name="isIgnore" ></a>

### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行を無視するようにマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isIgnore()``  
* ``isIgnore(1)``  
___


<a name="isInsert" ></a>

### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が挿入用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isInsert()``  
* ``isInsert(1)``  
___


<a name="isInteger" ></a>

### <code>isInteger</code>
<code><b>isInteger (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
文字列値が ``toInteger()`` の規則に従った省略可能な形式が指定された整数値であることを確認します 
* ``isInteger('123') -> true``
* ``isInteger('$123' -> '$###') -> true``
* ``isInteger('microsoft') -> false``
___


<a name="isLong" ></a>

### <code>isLong</code>
<code><b>isLong (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
文字列値が ``toLong()`` の規則に従った省略可能な形式が指定された 長整数型値であることを確認します 
* ``isLong('123') -> true``
* ``isLong('$123' -> '$###') -> true``
* ``isLong('gunchus') -> false``
___


<a name="isMatch" ></a>

### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行がルックアップで一致するかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isMatch()``  
* ``isMatch(1)``  
___


<a name="isNan" ></a>

### <code>isNan</code>
<code><b>isNan (<i>\<value1\></i> : integral) => boolean</b></code><br/><br/>
これが数値でないことを確認します。
* ``isNan(10.2) => false``
___


<a name="isNull" ></a>

### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
値が NULL かどうかをチェックします。  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___


<a name="isShort" ></a>

### <code>isShort</code>
<code><b>isShort (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
文字列値が ``toShort()`` の規則に従った省略可能な形式が指定された 短整数型値であることを確認します 
* ``isShort('123') -> true``
* ``isShort('$123' -> '$###') -> true``
* ``isShort('microsoft') -> false``
___


<a name="isTimestamp" ></a>

### <code>isTimestamp</code>
<code><b>isTimestamp (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
入力日付文字列が、省略可能な入力タイムスタンプ形式を使用したタイムスタンプであるかどうかを確認します。 使用可能な形式については、Java の SimpleDateFormat を参照してください。 タイムスタンプを省略すると、既定のパターンの ``yyyy-[M]M-[d]d hh:mm:ss[.f...]`` が使用されます。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 タイムスタンプはミリ秒の精度で 999 の値までサポートされます。使用可能な形式については、Java の SimpleDateFormat を参照してください。
* ``isTimestamp('2016-12-31 00:12:00') -> true``
* ``isTimestamp('2016-12-31T00:12:00' -> 'yyyy-MM-dd\\'T\\'HH:mm:ss' -> 'PST') -> true``
* ``isTimestamp('2012-8222.18') -> false``
___


<a name="isUpdate" ></a>

### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が更新用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isUpdate()``  
* ``isUpdate(1)``  
___


<a name="isUpsert" ></a>

### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が挿入用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isUpsert()``  
* ``isUpsert(1)``  
___


<a name="jaroWinkler" ></a>

### <code>jaroWinkler</code>
<code><b>jaroWinkler(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => double</b></code><br/><br/>
2 つの文字列の間の JaroWinkler 距離を取得します。 
* ``jaroWinkler('frog', 'frog') => 1.0``  
___


<a name="keyValues" ></a>

### <code>keyValues</code>
<code><b>keyValues(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => map</b></code><br/><br/>
キーと値のマップを作成します。 最初のパラメーターはキーの配列、2 番目のパラメーターは値の配列です。 両方の配列の長さは等しい必要があります。
*   ``keyValues(['bojjus', 'appa'], ['gunchus', 'ammi']) => ['bojjus' -> 'gunchus', 'appa' -> 'ammi']``
___ 


<a name="kurtosis" ></a>

### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の尖度を取得します。  
* ``kurtosis(sales)``  
___


<a name="kurtosisIf" ></a>

### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の尖度を取得します。  
* ``kurtosisIf(region == 'West', sales)``  
___


<a name="lag" ></a>

### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
現在の行の n 行前を評価した最初のパラメーターの値を取得します。 2 番目のパラメーターは、戻る行の数です。既定値は 1 です。 指定した数の行がない場合、既定値が指定されていない限り、null 値が返されます。  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___


<a name="last" ></a>

### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
列グループの最後の値を取得します。 2 番目のパラメーター ignoreNulls を省略すると、false であると見なされます。  
* ``last(sales)``  
* ``last(sales, false)``  
___


<a name="lastDayOfMonth" ></a>

### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
指定した日付から、その月の最後の日を取得します。  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___


<a name="lead" ></a>

### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
現在の行の n 行後を評価した最初のパラメーターの値を取得します。 2 番目のパラメーターは、進む行の数です。既定値は 1 です。 指定した数の行がない場合、既定値が指定されていない限り、null 値が返されます。  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___


<a name="least" ></a>

### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
比較 (以下) 演算子。 <= 演算子と同じです。  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___


<a name="left" ></a>

### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
インデックス 1 から開始して指定した文字数の部分文字列を抽出します。 SUBSTRING(str, 1, n) と同じです。  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___


<a name="length" ></a>

### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
文字列の長さを返します。  
* ``length('dumbo') -> 5``  
___


<a name="lesser" ></a>

### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較未満演算子。 < 演算子と同じです。  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="lesserOrEqual" ></a>

### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較 (以下) 演算子。 <= 演算子と同じです。  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___


<a name="levenshtein" ></a>

### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
2 つの文字列の間のレーベンシュタイン距離を取得します。  
* ``levenshtein('boys', 'girls') -> 4``  
___


<a name="like" ></a>

### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
パターンは文字通り一致する文字列です。 次の特殊文字は例外です。_ は入力内の任意の 1 文字と一致します (posix 正規表現の . に 類似 (```posix```))、% は入力内の 0 文字以上と一致します (```posix``` 正規表現の * に類似)。
エスケープ文字は '' です。 エスケープ文字の前に特殊記号または別のエスケープ文字がある場合、その次の文字が文字通り照合されます。 その他の文字をエスケープするのは無効です。  
* ``like('icecream', 'ice%') -> true``  
___


<a name="locate" ></a>

### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
特定の位置から開始して、文字列内の部分文字列の位置 (1 を基準とする) を見つけます。 位置を省略すると、文字列の最初からとみなされます。 見つからない場合は 0 が返されます。  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___


<a name="log" ></a>

### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
対数の値を計算します。 省略可能な底を指定できます。省略すると、オイラー数を返します (使用される場合)。  
* ``log(100, 10) -> 2``  
___


<a name="log10" ></a>

### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
10 を底とする対数の値を計算します。  
* ``log10(100) -> 2``  
___


<a name="lookup" ></a>

### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
キャッシュされたシンクのキーと一致する特定のキーを使用して、キャッシュされたシンクの最初の行が検索されます。
* ``cacheSink#lookup(movieId)``  
___


<a name="lower" ></a>

### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を小文字にします。  
* ``lower('GunChus') -> 'gunchus'``  
___


<a name="lpad" ></a>

### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
特定の長さになるまで、指定した埋め込み文字で文字列の左側を埋め込みます。 文字列が指定された長さ以上の場合は、その長さまで削除されます。  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``  
___


<a name="ltrim" ></a>

### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
文字列の先頭文字を左から削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___


<a name="map" ></a>

### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
指定された式を使用して、配列の各要素を新しい要素にマップします。 map は、式関数の 1 つの要素への参照を #item として予期します。  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___


<a name="mapAssociation" ></a>

### <code>mapAssociation</code>
<code><b>mapAssociation(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => array</b></code><br/><br/>
キーを新しい値に関連付け、マップを変換します。 配列を返します。 項目を #key として、現在の値を #value としてアドレス指定できるマッピング関数を取得します。 
*   ``mapAssociation(['bojjus' -> 'gunchus', 'appa' -> 'ammi'], @(key = #key, value = #value)) => [@(key = 'bojjus', value = 'gunchus'), @(key = 'appa', value = 'ammi')]``
___ 


<a name="mapIf" ></a>

### <code>mapIf</code>
<code><b>mapIf (<i>\<value1\></i> : array, <i>\<value2\></i> : binaryfunction, \<value3\>: binaryFunction) => any</b></code><br/><br/>
配列を、同じ長さまたは短い長さの別の配列に条件付きでマップします。 値は、structTypes など、任意のデータ型とすることができます。 配列内のアイテムを #item として、現在のインデックスを #index としてアドレス指定できるマッピング関数を取ります。 深く入れ子になったマップの場合、``#item_[n](#item_1, #index_1...)`` という表記を使用して親マップを参照できます。
*    ``mapIf([10, 20, 30], #item > 10, #item + 5) -> [25, 35]``
* ``mapIf(['icecream', 'cake', 'soda'], length(#item) > 4, upper(#item)) -> ['ICECREAM', 'CAKE']``
___


<a name="mapIndex" ></a>

### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
指定された式を使用して、配列の各要素を新しい要素にマップします。 map は、式関数内の 1 つの要素への参照を #item として予期し、要素インデックスへの参照を #index として予期します。  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___


<a name="mapLoop" ></a>

### <code>mapLoop</code>
<code><b>mapLoop(<i>\<value1\></i> : integer, <i>\<value2\></i> : unaryfunction) => any</b></code><br/><br/>
1 から length までループさせてその長さの配列を作成します。 これは、配列内のインデックスを #index としてアドレス指定できるマッピング関数を取ります。 深く入れ子になったマップの場合、#index_n(#index_1, #index_2...) という表記を使用して親マップを参照できます。
*    ``mapLoop(3, #index * 10) -> [10, 20, 30]``
___


<a name="max" ></a>

### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
列の最大値を取得します。  
* ``max(sales)``  
___


<a name="maxIf" ></a>

### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
条件に基づいて、列の値の最大値を取得します。  
* ``maxIf(region == 'West', sales)``  
___


<a name="md5" ></a>

### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの MD5 ハッシュを計算し、32 文字の16 進数の文字列を返します。 行のフィンガープリントを計算するために使用できます。  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___


<a name="mean" ></a>

### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
列の値の平均を取得します。 AVG と同じです。  
* ``mean(sales)``  
___


<a name="meanIf" ></a>

### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、列の値の平均を取得します。 avgIf と同じです。  
* ``meanIf(region == 'West', sales)``  
___


<a name="millisecond" ></a>

### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
日付のミリ秒の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="milliseconds" ></a>

### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位でのミリ秒数の期間。  
* ``milliseconds(2) -> 2L``  
___


<a name="min" ></a>

### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
列の最小値を取得します。  
* ``min(sales)``  
___


<a name="minIf" ></a>

### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
条件に基づいて、列の値の最小値を取得します。  
* ``minIf(region == 'West', sales)``  
___


<a name="minus" ></a>

### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値を減算します。 日付から日数を減算します。 タイムスタンプから期間を減算します。 2 つのタイムスタンプを減算して、ミリ秒単位での差を取得します。 \- 演算子と同じです。  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___


<a name="minute" ></a>

### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
タイムスタンプから分の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___


<a name="minutes" ></a>

### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での分数の期間。  
* ``minutes(2) -> 120000L``  
___


<a name="mlookup" ></a>

### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
キャッシュされたシンクのキーと一致する特定のキーを使用して、キャッシュされたシンクの一致するすべての行が検索されます。
* ``cacheSink#mlookup(movieId)``  
___


<a name="mod" ></a>

### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアの剰余です。 % 演算子と同じです。  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___


<a name="month" ></a>

### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
日付またはタイムスタンプから月の値を取得します。  
* ``month(toDate('2012-8-8')) -> 8``  
___


<a name="monthsBetween" ></a>

### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
2 つの日付の間の月数を取得します。 計算を丸めることができいます。省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___


<a name="multiply" ></a>

### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアを乗算します。 \* 演算子と同じです。  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___


<a name="negate" ></a>

### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値の符号を反転します。 正の数値を負の数値 (または、その逆) に変換します。  
* ``negate(13) -> -13``  
___


<a name="nextSequence" ></a>

### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
次の固有なシーケンスを返します。 数値は、パーティション内でのみ連続し、プレフィックスとして partitionId が付加されます。  
* ``nextSequence() == 12313112 -> false``  
___


<a name="normalize" ></a>

### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
文字列値を個別にアクセント記号が付いた Unicode 文字に正規化します。  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___


<a name="not" ></a>

### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
論理否定演算子。  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___


<a name="notEquals" ></a>

### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較不等価演算子。 != 演算子と同じです。  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___


<a name="notNull" ></a>

### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
値が NULL 以外であるかどうかをチェックします。  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___


<a name="nTile" ></a>

### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
```NTile``` 関数は、各ウィンドウ パーティションの行を `n` バケット (1 から最大 `n`) に分割します。 バケットの値の差は最大で 1 です。 パーティション内の行数がバケット数に対して均等に分割されない場合、残りの値は、最初のバケットから順番にバケットごとに 1 つずつ分配されます。 ```NTile``` 関数は、```tertiles```、四分位数、十分位数およびその他の一般的な集計統計情報を計算する場合に便利です。 この関数は、初期化中に次の 2 つの変数を計算します。通常のバケットのサイズには、さらに 1 行が追加されます。 どちらの変数も現在のパーティションのサイズに基づいています。 計算プロセス中に、この関数は現在の行番号、現在のバケット番号、およびバケットが変更される行番号 (bucketThreshold) を追跡します。 現在の行番号がバケットしきい値に達すると、バケットの値が 1 つ増加し、しきい値にはバケット サイズが追加されます (現在のバケットが埋め込まれている場合は、さらに 1 が追加されます)。  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___


<a name="null" ></a>

### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL 値を返します。 "null" という名前の列がある場合は、関数 `syntax(null())` を使用します。 これを使用する操作はすべて NULL になります。  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___


<a name="or" ></a>

### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
論理 OR 演算子です。 || と同じです。  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___


<a name="originColumns" ></a>

### <code>originColumns</code>
<code><b>originColumns(<i>&lt;streamName&gt;</i> : string) => any</b></code><br/><br/>
列が作成された配信元ストリームのすべての出力列を取得します。 別の関数内で囲む必要があります。
* ``array(toString(originColumns('source1')))``
___  


<a name="output" ></a>

### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
キャッシュ シンクの結果の最初の行を返します。* ``cacheSink#output()``  
___


<a name="outputs" ></a>

### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
キャッシュ シンクの結果の出力行セット全体を返します。* ``cacheSink#outputs()``
___



<a name="partitionId" ></a>

### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
入力行が含まれている現在のパーティション ID を返します。  
* ``partitionId()``  
___


<a name="pMod" ></a>

### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアの正の剰余です。  
* ``pmod(-20, 8) -> 4``  
___


<a name="power" ></a>

### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
数値を別の数値でべき乗します。  
* ``power(10, 2) -> 100``  
___


<a name="radians" ></a>

### <code>radians</code>
<code><b>radians(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
角度をラジアン * ``radians(180) => 3.141592653589793`` に変換します。  
___


<a name="random" ></a>

### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
パーティション内に省略可能なシード値を指定すると、乱数を返します。 シード値は固定値である必要があり、乱数値を生成するために partitionId と組み合わせて使用されます。* ``random(1) == 1 -> false``
___


<a name="rank" ></a>

### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
ウィンドウの order by 句で指定した値のグループ内の値の順位を計算します。 結果は、パーティション順位内の現在行以前の行数に 1 を加算した値です。 値では、シーケンス内にギャップが生じます。 Rank は、データが並べ替えられていない場合や、値の変更が予測される場合でも機能します。  
* ``rank()``  
___


<a name="reassociate" ></a>

### <code>reassociate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
キーを新しい値に関連付け、マップを変換します。 項目を #key として、現在の値を #value としてアドレス指定できるマッピング関数を取得します。  
* ``reassociate(['fruit' -> 'apple', 'vegetable' -> 'tomato'], substring(#key, 1, 1) + substring(#value, 1, 1)) => ['fruit' -> 'fa', 'vegetable' -> 'vt']``
___
  


<a name="reduce" ></a>

### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
配列内の要素を累積します。 reduce は、最初の式関数のアキュムレータと 1 つの要素への参照を #acc および #item として予期し、結果の値を 2 番目の式関数で使用される #result として予期します。  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___


<a name="regexExtract" ></a>

### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
指定された正規表現パターンに一致する部分文字列を抽出します。 最後のパラメーターは、一致グループを識別し、省略すると既定値として 1 が使用されます。 エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___


<a name="regexMatch" ></a>

### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
指定された正規表現パターンに文字列が一致するかどうかをチェックします。 エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___


<a name="regexReplace" ></a>

### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
指定の文字列内の正規表現パターンのすべての出現を別の部分文字列に置換します。エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___


<a name="regexSplit" ></a>

### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
文字列を正規表現に基づく区切り文字に基づいて分割し、文字列の配列を返します。  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___


<a name="replace" ></a>

### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
指定された文字列内の substring のすべての出現を別の substring に置換します。 最後のパラメーターを省略すると、既定値は空の文字列になります。  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___


<a name="reverse" ></a>

### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を反転します。  
* ``reverse('gunchus') -> 'suhcnug'``  
___


<a name="right" ></a>

### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
指定した文字数の部分文字列を右から抽出します。 SUBSTRING(str, LENGTH(str) - n, n) と同じです。  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___


<a name="rlike" ></a>

### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
指定された正規表現パターンに文字列が一致するかどうかをチェックします。  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___


<a name="round" ></a>

### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
省略可能な桁数と省略可能な丸めモードで数値を丸めます。 桁数を省略すると、既定値の 0 が使用されます。 モードを省略すると、既定値は ROUND_HALF_UP(5) になります。 丸めの値には、1 - ROUND_UP、2 - ROUND_DOWN、3 - ROUND_CEILING、4 - ROUND_FLOOR、5 - ROUND_HALF_UP、6 - ROUND_HALF_DOWN、7 - ROUND_HALF_EVEN、8 - ROUND_UNNECESSARY があります。  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___


<a name="rowNumber" ></a>

### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
ウィンドウ内の行のシーケンシャル行番号を 1 から順番に割り当てます。  
* ``rowNumber()``  



<a name="rpad" ></a>

### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
特定の長さになるまで、指定した埋め込み文字で文字列の右側を埋め込みます。 文字列が指定された長さ以上の場合は、その長さまで削除されます。  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___


<a name="rtrim" ></a>

### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
文字列の末尾文字を右から削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___


<a name="second" ></a>

### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
日付の秒の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___


<a name="seconds" ></a>

### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での秒数の期間。  
* ``seconds(2) -> 2000L``  
___


<a name="setBitSet" ></a>

### <code>setBitSet</code>
<code><b>setBitSet (<i>\<value1\></i>: array, <i>\<value2\></i>:array) => array</b></code><br/><br/>
このビットセットにビット位置を設定します。* ``setBitSet(toBitSet([10, 32]), [98]) => [4294968320L, 17179869184L]``
___  


<a name="sha1" ></a>

### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの SHA-1 ハッシュを計算し、40 文字の16 進数の文字列を返します。 行のフィンガープリントを計算するために使用できます。  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___


<a name="sha2" ></a>

### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの SHA-2 ハッシュを指定のビット長で計算します。ビット長として指定できるのは、0(256)、224、256、384、512 の値のみです。 行のフィンガープリントを計算するために使用できます。  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___


<a name="sin" ></a>

### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
サイン値を計算します。  
* ``sin(2) -> 0.9092974268256817``  
___


<a name="sinh" ></a>

### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
双曲線サイン値を計算します。  
* ``sinh(0) -> 0.0``  
___


<a name="size" ></a>

### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
配列またはマップの種類のサイズを見つけます * ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___


<a name="skewness" ></a>

### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の歪度を取得します。  
* ``skewness(sales)``  
___


<a name="skewnessIf" ></a>

### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の歪度を取得します。  
* ``skewnessIf(region == 'West', sales)``  
___


<a name="slice" ></a>

### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
ある位置から配列のサブセットを抽出します。 位置は 1 から始まります。 長さを省略すると、既定値は文字列の最後になります。  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___


<a name="sort" ></a>

### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
指定された述語関数を使用して配列を並べ替えます。 sort は、式関数の 2 つの連続する要素への参照を #item1 および #item2 として予期します。  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  
___


<a name="soundex" ></a>

### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列の ```soundex``` コードを取得します。  
* ``soundex('genius') -> 'G520'``  
___


<a name="split" ></a>

### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
文字列を区切り文字に基づいて分割し、文字列の配列を返します。  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___


<a name="sqrt" ></a>

### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
数値の平方根を計算します。  
* ``sqrt(9) -> 3``  
___


<a name="startsWith" ></a>

### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
文字列が指定した文字列で開始しているかどうかをチェックします。  
* ``startsWith('dumbo', 'du') -> true``  
___


<a name="stddev" ></a>

### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の標準偏差を取得します。  
* ``stdDev(sales)``  
___


<a name="stddevIf" ></a>

### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の標準偏差を取得します。  
* ``stddevIf(region == 'West', sales)``  
___


<a name="stddevPopulation" ></a>

### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の母標準偏差を取得します。  
* ``stddevPopulation(sales)``  
___


<a name="stddevPopulationIf" ></a>

### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の母標準偏差を取得します。  
* ``stddevPopulationIf(region == 'West', sales)``  
___


<a name="stddevSample" ></a>

### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の標本標準偏差を取得します。  
* ``stddevSample(sales)``  
___


<a name="stddevSampleIf" ></a>

### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の標本標準偏差を取得します。  
* ``stddevSampleIf(region == 'West', sales)``  
___


<a name="subDays" ></a>

### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプから日数を減算します。 日付に対する - 演算子と同じです。  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___


<a name="subMonths" ></a>

### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプから月数を減算します。  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___


<a name="substring" ></a>

### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
ある位置から特定の長さの部分文字列を抽出します。 位置は 1 から始まります。 長さを省略すると、既定値は文字列の最後になります。  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___


<a name="sum" ></a>

### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値列の集計を取得します。  
* ``sum(col)``  
___


<a name="sumDistinct" ></a>

### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値列の個別値の集計を取得します。  
* ``sumDistinct(col)``  
___


<a name="sumDistinctIf" ></a>

### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、数値列の集計を取得します。 条件は、任意の列に基づくことができます。  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___


<a name="sumIf" ></a>

### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、数値列の集計を取得します。 条件は、任意の列に基づくことができます。  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___


<a name="tan" ></a>

### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
タンジェント値を計算します。  
* ``tan(0) -> 0.0``  
___


<a name="tanh" ></a>

### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
双曲線タンジェント値を計算します。  
* ``tanh(0) -> 0.0``  
___


<a name="toBase64" ></a>

### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
指定された文字列を base64 でエンコードします。  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___


<a name="toBinary" ></a>

### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
任意の数値、日付、タイムスタンプ、文字列をバイナリ表現に変換します。  
* ``toBinary(3) -> [0x11]``  
___


<a name="toBoolean" ></a>

### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
('t'、'true'、'y'、'yes'、'1') の値を true に変換し、('f'、'false'、'n'、'no'、'0') の値を false に変換し、それ以外の値を NULL に変換します。  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___


<a name="toByte" ></a>

### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
任意の数値または文字列をバイト値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___


<a name="toDate" ></a>

### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
省略可能な入力日付形式を使用して、入力日付文字列を日付に変換します。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 入力日付形式が省略されている場合、既定の形式は yyyy-[M]M-[d]d になります。 許容される形式は [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ] です。  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___


<a name="toDecimal" ></a>

### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
任意の数値または文字列を decimal 型の値に変換します。 有効桁数と小数点以下桁数を指定しない場合、既定値の (10,2) が使用されます。変換には、任意指定の Java 10 進数形式を使用できます。 en-US、de、zh-CN など、BCP47 言語形式に属する省略可能なロケール形式。  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___


<a name="toDouble" ></a>

### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
任意の数値または文字列を double 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 en-US、de、zh-CN など、BCP47 言語形式に属する省略可能なロケール形式。  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___


<a name="toFloat" ></a>

### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
任意の数値または文字列を float 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 double を切り捨てます。  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___


<a name="toInteger" ></a>

### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
任意の数値または文字列を integer 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 long、float、double を切り捨てます。  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___


<a name="toLong" ></a>

### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
任意の数値または文字列を long 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 float、double を切り捨てます。  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___


<a name="toShort" ></a>

### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
任意の数値または文字列を short 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 integer、long、float、double を切り捨てます。  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___


<a name="toString" ></a>

### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
プリミティブ データ型を文字列に変換します。 数値と日付の場合は、形式を指定できます。 指定しない場合、システムの既定値が選択されます。数値には、Java の 10 進数形式が使用されます。 使用できるすべての日付形式については、Java SimpleDateFormat を参照してください。既定の形式は yyyy-MM-dd です。  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___


<a name="toTimestamp" ></a>

### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
省略可能なタイムスタンプ形式を指定して、文字列をタイムスタンプに変換します。 タイムスタンプを省略すると、既定のパターンの yyyy-[M]M-[d]d hh:mm:ss[.f...] が使用されます。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 タイムスタンプでサポートされる最大精度は、ミリ秒 (値 999) です。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="toUTC" ></a>

### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
タイムスタンプを UTC に変換します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  



<a name="translate" ></a>

### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
文字列内のある文字セットを別の文字セットで置換します。 文字の置換は 1 対 1 で行われます。  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___


<a name="trim" ></a>

### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
文字列の先頭文字と末尾文字を削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___


<a name="true" ></a>

### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
常に true 値を返します。 "true" という名前の列がある場合は、関数 `syntax(true())` を使用します。  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___


<a name="typeMatch" ></a>

### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
列の型を照合します。 パターン式でのみ使用できます。数値は short、integer、long、double、float、または decimal 型に一致し、整数は short、integer、long 型に一致し、小数は double、float、decimal 型に一致し、日時は date または timestamp 型に一致します。  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___


<a name="unescape" ></a>

### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
形式に従って、文字列を非エスケープします。 使用できる形式のリテラル値は、'json'、'xml'、'ecmascript'、'html'、'java' です。
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___


<a name="unfold" ></a>

### <code>unfold</code>
<code><b>unfold (<i>&lt;value1&gt;</i>: array) => any</b></code><br/><br/>
配列を一連の行に展開し、すべての行内の残りの列に値を繰り返します。
*   ``unfold(addresses) => any``
*   ``unfold( @(name = salesPerson, sales = salesAmount) ) => any``
___  


<a name="unhex" ></a>

### <code>unhex</code>
<code><b>unhex(<i>\<value1\></i>: string) => binary</b></code><br/><br/>
バイナリ値を 16 進数文字列表記から数値に変換します。 これは、文字列からバイナリ表記に変換するために、sha2、md5 と組み合わせて使用できます *    ``unhex('1fadbe') -> toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])``
*    ``unhex(md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))) -> toBinary([toByte(0x4c),toByte(0xe8),toByte(0xa8),toByte(0x80),toByte(0xbd),toByte(0x62),toByte(0x1a),toByte(0x1f),toByte(0xfa),toByte(0xd0),toByte(0xbc),toByte(0xa9),toByte(0x05),toByte(0xe1),toByte(0xbc),toByte(0x5a)])``



<a name="union" ></a>

### <code>union</code>
<code><b>union(<i>&lt;value1&gt;</i>: array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
2 つの配列からの個別の項目の和集合を返します。
* ``union([10, 20, 30], [20, 40]) => [10, 20, 30, 40]``
___  
  


<a name="upper" ></a>

### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を大文字にします。  
* ``upper('bojjus') -> 'BOJJUS'``  
___


<a name="uuid" ></a>

### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
生成された UUID を返します。  
* ``uuid()``  
___


<a name="variance" ></a>

### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の分散を取得します。  
* ``variance(sales)``  
___


<a name="varianceIf" ></a>

### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の分散を取得します。  
* ``varianceIf(region == 'West', sales)``  
___


<a name="variancePopulation" ></a>

### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の母分散を取得します。  
* ``variancePopulation(sales)``  
___


<a name="variancePopulationIf" ></a>

### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の母分散を取得します。  
* ``variancePopulationIf(region == 'West', sales)``  
___


<a name="varianceSample" ></a>

### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の不偏分散を取得します。  
* ``varianceSample(sales)``  
___


<a name="varianceSampleIf" ></a>

### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の不偏分散を取得します。  
* ``varianceSampleIf(region == 'West', sales)``  



<a name="weekOfYear" ></a>

### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その年の何週目かを取得します。  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___


<a name="weeks" ></a>

### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での週数の期間。  
* ``weeks(2) -> 1209600000L``  
___


<a name="xor" ></a>

### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
XOR 論理演算子。 ^ 演算子と同じです。  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___


<a name="year" ></a>

### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
日付の年の値を取得します。  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="next-steps"></a>次のステップ

[式ビルダーの使用方法を学習します](concepts-data-flow-expression-builder.md)。