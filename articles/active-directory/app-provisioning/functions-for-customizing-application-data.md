---
title: Azure Active Directory アプリケーションのプロビジョニングで属性マッピングの式を記述するためのリファレンス
description: Azure Active Directory で SaaS アプリ オブジェクトを自動プロビジョニングしているときに、式マッピングを使用して属性値を許容される形式に変換する方法について説明します。 関数の参照一覧が含まれています。
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 11/16/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: f63aaf34e616c6863c2a9aa1e6d5f1daabae4d01
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709009"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory で属性マッピングの式を記述するためのリファレンス

SaaS アプリケーションに対してプロビジョニングを構成するときに指定できる属性マッピングの種類の 1 つは、式マッピングです。 これらのマッピングでは、ユーザーのデータを SaaS アプリケーションが許容可能な形式に変換することができる、スクリプトのような式を記述する必要があります。

## <a name="syntax-overview"></a>構文の概要

属性マッピングの式の構文は、Visual Basic のApplications (VBA) 関数に似ています。

* 式全体は、関数の形式で定義する必要があります。名前の後にかっこで囲んだ引数を続けます。*FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 各関数内で他の関数を入れ子にすることができます。 次に例を示します。*FunctionOne(FunctionTwo(`<<argument1>>`))*
* 関数には、次の 3 つの異なる種類の引数を渡すことができます。
  
  1. 属性。角かっこで囲む必要があります。 例: [attributeName]
  2. 文字列定数。二重引用符で囲む必要があります。 次に例を示します。"米国"
  3. 他の関数 次に例を示します。FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 文字列定数では、文字列に円記号 (\) または引用符 (") を含める必要がある場合は、円記号 (\) でエスケープする必要があります。 次に例を示します。"会社名:\\"Contoso\\""
* この構文では大文字と小文字が区別されます。これは、関数に文字列として入力するときに考慮する必要があり、ここから直接コピーして貼り付けることもできます。 

## <a name="list-of-functions"></a>関数の一覧

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [AppRoleAssignmentsComplex](#approleassignmentscomplex) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateDiff](#datediff) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IgnoreFlowIfNullOrEmpty](#ignoreflowifnullorempty) &nbsp;&nbsp;&nbsp;&nbsp;[IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) &nbsp;&nbsp; &nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [RandomString](#randomstring) &nbsp;&nbsp;&nbsp;&nbsp; [Redact](#redact) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Append

**関数:** Append(source, suffix)

**説明:** source 文字列値を受け取り、その末尾に suffix を追加します。

**パラメーター:**

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **suffix** |必須 |String |source 値の末尾に追加する文字列。 |


#### <a name="append-constant-suffix-to-user-name"></a>ユーザー名に定数のサフィックスを追加する
例: Salesforce Sandbox を使用している場合は、ユーザー名を同期する前に、すべてのユーザー名に別のサフィックスを追加する必要があります。

**式:**  
`Append([userPrincipalName], ".test")`

**サンプル入力/出力:** 

* **入力**: (userPrincipalName): "John.Doe@contoso.com"
* **出力**:  "John.Doe@contoso.com.test"

---
### <a name="approleassignmentscomplex"></a>AppRoleAssignmentsComplex

**関数:** AppRoleAssignmentsComplex([appRoleAssignments])

**説明:** 複数のロールをユーザーにプロビジョニングするために使用されます。 詳しい使用方法については、「[チュートリアル - Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングをカスタマイズする](customize-application-attributes.md#provisioning-a-role-to-a-scim-app)」を参照してください。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **appRoleAssignments** |必須 |String |**appRoleAssignments** オブジェクト |

---
### <a name="bitand"></a>BitAnd
**関数:** BitAnd(value1, value2)

**説明:** この関数は両方のパラメーターをバイナリ表現に変換し、ビットを次に設定します。

- 0 - value1 と value2 内の対応するビットの 1 つまたは両方が 0 の場合
- 1 - 対応するビットの両方が 1 の場合。

つまり、両方のパラメーターの対応するビットが 1 の場合を除くすべてのケースで 0 を返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value1** |必須 |番号 |value2 と AND で処理する数値|
| **value2** |必須 |番号 |value1 と AND で処理する数値|

**例:** 
`BitAnd(&HF, &HF7)`

11110111 AND 00000111 = 00000111。`BitAnd` からは 7 が返されます。その 2 進数は 00000111 です。

---
### <a name="cbool"></a>CBool
**関数:**  
`CBool(Expression)`

**説明:**  
`CBool` からは、式の評価結果に基づいてブール値が返されます。 式の評価結果が 0 以外の値の場合は `CBool` によって *True* が返され、それ以外の場合は *False* が返されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **[式]** |必須 | expression | 任意の有効な式 |

**例:** 
`CBool([attribute1] = [attribute2])`                                                                    
両方の属性が同じ値を持つ場合は、True を返します。

---
### <a name="cdate"></a>CDate
**関数:**  
`CDate(expression)`

**説明:**  
CDate 関数は、文字列から UTC DateTime を返します。 DateTime はネイティブな属性の種類ではありませんが、[FormatDateTime](#formatdatetime) や [DateAdd](#dateadd) などの日付関数の中で使用することができます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **[式]** |必須 | Expression | 日付/時刻を表す任意の有効な文字列。 サポートされている形式については、「[.NET カスタム日時書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)」を参照してください。 |

**備考:**  
返される文字列は常に UTC で、**M/d/yyyy h:mm:ss tt** の形式に従います。

**例 1:** <br> 
`CDate([StatusHireDate])`  
**サンプル入力/出力:** 

* **入力** (StatusHireDate): "2020-03-16-07:00"
* **出力**:  "3/16/2020 7:00:00 AM" <-- *上記の DateTime に相当する UTC が返されます*

**例 2:** <br> 
`CDate("2021-06-30+08:00")`  
**サンプル入力/出力:** 

* **入力**: "2021-06-30+08:00"
* **出力**:  "6/29/2021 4:00:00 PM" <-- *上記の DateTime に相当する UTC が返されます*

**例 3:** <br> 
`CDate("2009-06-15T01:45:30-07:00")`  
**サンプル入力/出力:** 

* **入力**: "2009-06-15T01:45:30-07:00"
* **出力**:  "6/15/2009 8:45:30 AM" <-- *上記の DateTime に相当する UTC が返されます*

---
### <a name="coalesce"></a>Coalesce
**関数:** Coalesce(source1, source2, ..., defaultValue)

**説明:** NULL ではない最初のソース値が返されます。 すべての引数が NULL で、defaultValue が存在する場合、defaultValue が返されます。 すべての引数が NULL で、defaultValue が存在しない場合、Coalesce からは NULL が返されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source1  … sourceN** | 必須 | String |必須、回数は可変。 通常は、source オブジェクトの属性の名前。 |
| **defaultValue** | 省略可能 | String | すべてのソース値が NULL の場合に使用される既定値。 空の文字列 ("") を指定できます。

#### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>NULL でない場合はフロー メール値、それ以外の場合は userPrincipalName
例: メール属性が存在する場合は、それをフローすることをお勧めします。 存在しない場合、代わりに userPrincipalName の値をフローしてください。

**式:**  
`Coalesce([mail],[userPrincipalName])`

**サンプル入力/出力:** 

* **入力** (mail):NULL
* **入力** (userPrincipalName): "John.Doe@contoso.com"
* **出力**:  "John.Doe@contoso.com"


---
### <a name="converttobase64"></a>ConvertToBase64
**関数:** ConvertToBase64(source)

**説明:** ConvertToBase64 関数は、文字列を Unicode の base64 文字列に変換します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |base 64 に変換される文字列|

**例:** 
`ConvertToBase64("Hello world!")`

"SABlAGwAbABvACAAdwBvAHIAbABkACEA" を返します。

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**関数:** ConvertToUTF8Hex(source)

**説明:** ConvertToUTF8Hex 関数は、文字列を UTF8 の 16 進数でエンコードされた値に変換します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |UTF8 Hex に変換される文字列|

**例:** 
`ConvertToUTF8Hex("Hello world!")`

48656C6C6F20776F726C6421 を返します。

---
### <a name="count"></a>Count
**関数:** Count(attribute)

**説明:** Count 関数は、複数値の属性内の要素数を返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |必須 |属性 (attribute) |要素がカウントされる複数値の属性|

---
### <a name="cstr"></a>CStr
**関数:** CStr(value)

**説明:** CStr 関数では、値が文字列データ型に変換されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |必須 | 数値、参照、またはブール値 | 数値、参照属性、ブール値を指定できます。 |

**例:** 
`CStr([dn])`

"cn=Joe,dc=contoso,dc=com" が返されます

---
### <a name="dateadd"></a>DateAdd
**関数:**  
`DateAdd(interval, value, dateTime)`

**説明:**  
指定した時間間隔が追加された日付を表す日時文字列を返します。 返される日付は、**M/d/yyyy h:mm:ss tt** 形式です。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | メモ |
| --- | --- | --- | --- |
| **interval** |必須 | String | 追加する時間間隔。 この表の下にある指定可能な値を参照してください。 |
| **value** |必須 | 数値 | 追加する単位の数。 正の数 (将来の日時を取得する場合) または負の数 (過去の日時を取得する場合) を指定できます。 |
| **dateTime** |必須 | DateTime | 間隔が追加される日付を表す DateTime。 |

**間隔** 文字列には次のいずれかの値が必要です。 
 * yyyy: 年 
 * m: 月
 * d: 日
 * ww: 週
 * h: 時
 * n: 分
 * s: 秒

**例 1: 入社日に 7 日を追加する**  
`DateAdd("d", 7, CDate([StatusHireDate]))`
* **入力** (StatusHireDate): 2012-03-16-07:00
* **出力**: 3/23/2012 7:00:00 AM

**例 2: 入社日の 10 日前に日付を取得する**  
`DateAdd("d", -10, CDate([StatusHireDate]))`
* **入力** (StatusHireDate): 2012-03-16-07:00
* **出力**: 3/6/2012 7:00:00 AM

**例 3: 入社日に 2 週間を追加する**  
`DateAdd("ww", 2, CDate([StatusHireDate]))`
* **入力** (StatusHireDate): 2012-03-16-07:00
* **出力**: 3/30/2012 7:00:00 AM

**例 4: 入社日に 10 か月を追加する**  
`DateAdd("m", 10, CDate([StatusHireDate]))`
* **入力** (StatusHireDate): 2012-03-16-07:00
* **出力**: 1/16/2013 7:00:00 AM

**例 5: 入社日に 2 年を追加する**  
`DateAdd("yyyy", 2, CDate([StatusHireDate]))`
* **入力** (StatusHireDate): 2012-03-16-07:00
* **出力**: 3/16/2014 7:00:00 AM
---
### <a name="datediff"></a>DateDiff
**関数:**  
`DateDiff(interval, date1, date2)`

**説明:**  
この関数は *interval* パラメーターを使用して、入力された 2 つの日付の差を示す数値を返します。  を返します。 
  * date2 が date1 より大きい場合は正の数 
  * date2 が date1 より小さい場合は負の数 
  * date2 と date1 が等しい場合は 0

**パラメーター:** 

| 名前 | 必須/省略可能 | Type | メモ |
| --- | --- | --- | --- |
| **interval** |必須 | String | 差の計算に使用する時間の間隔。 |
| **date1** |必須 | DateTime | 有効な日付を表す DateTime。 |
| **date2** |必須 | DateTime | 有効な日付を表す DateTime。 |

**間隔** 文字列には次のいずれかの値が必要です。 
 * yyyy: 年 
 * m: 月
 * d: 日
 * ww: 週
 * h: 時
 * n: 分
 * s: 秒

**例 1: 現在の日付と Workday の採用日を異なる間隔で比較する** <br>
`DateDiff("d", Now(), CDate([StatusHireDate]))`

| 例 | interval | date1 | date2 | output |
| --- | --- | --- | --- | --- |
| 2 つの日付の間における日数の正の差 | d | 2021-08-18+08:00 | 2021-08-31+08:00 | 13 |
| 2 つの日付の間における日数の負の差 | d | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -3449 |
| 2 つの日付の間の週の差 | ww | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -493 | 
| 2 つの日付の間の月の差 | m | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -113 | 
| 2 つの日付の間の年の差 | yyyy | 8/25/2021 5:41:18 PM | 2012-03-16-07:00 | -9 | 
| 両方の日付が同じ場合の差 | d | 2021-08-31+08:00 | 2021-08-31+08:00 | 0 | 
| 2 つの日付の間の時間の差 | h | 2021-08-24 | 2021-08-25 | 24 | 
| 2 つの日付の間の分の差 | n | 2021-08-24 | 2021-08-25 | 1440 | 
| 2 つの日付の間の秒の差 | s | 2021-08-24 | 2021-08-25 | 86400 | 

**例 2: DateDiff と IIF 関数を組み合わせて属性値を設定する** <br>
Workday でアカウントが Active な場合、雇用日が次の 5 日以内の場合にのみ、ユーザーの *accountEnabled* 属性を True に設定します。 

```
Switch([Active], , 
  "1", IIF(DateDiff("d", Now(), CDate([StatusHireDate])) > 5, "False", "True"), 
  "0", "False")
```

---

### <a name="datefromnum"></a>DateFromNum
**関数:** DateFromNum(value)

**説明:** DateFromNum 関数は、AD の日付形式の値を DateTime 型に変換します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |必須 | Date | DateTime 型に変換される AD 日付 |

**例:** 
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

2012 年 1 月 1 日の午後 11:00 を表す DateTime が返されます。

---
### <a name="formatdatetime"></a>FormatDateTime
**関数:** FormatDateTime(source, dateTimeStyles, inputFormat, outputFormat)

**説明:** 1 つの形式の日付文字列を受け取り、別の形式に変換します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **dateTimeStyles** | 省略可能 | String | これを使用して、いくつかの日時解析メソッドによる文字列の解析をカスタマイズする形式指定オプションを指定します。 サポートされる値については、[DateTimeStyles のドキュメント](/dotnet/api/system.globalization.datetimestyles)を参照してください。空のままにすると、使用される既定値は DateTimeStyles.RoundtripKind、DateTimeStyles.AllowLeadingWhite、DateTimeStyles.AllowTrailingWhite です  |
| **inputFormat** |必須 |String |有効な形式の source 値。 サポートされている形式については、「[.NET カスタム日時書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)」を参照してください。 |
| **outputFormat** |必須 |String |出力日付の形式。 |



#### <a name="output-date-as-a-string-in-a-certain-format"></a>特定の形式の文字列として日付を出力する
例: ServiceNow などの SaaS アプリケーションに特定の形式で日付を送信する場合があります。 次の式を使用することを検討してください。 

**Expression:** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**サンプル入力/出力:**

* **入力** (extensionAttribute1):"20150123105347.1Z"
* **出力**:"2015-01-23"


---
### <a name="guid"></a>Guid
**関数:** Guid()

**説明:** GUID 関数は、新しいランダムな GUID を生成します。

**例:** <br>
`Guid()`<br>
出力例: "1088051a-cd4b-4288-84f8-e02042ca72bc"

---
### <a name="ignoreflowifnullorempty"></a>IgnoreFlowIfNullOrEmpty
**関数:** IgnoreFlowIfNullOrEmpty(expression)

**説明**: IgnoreFlowIfNullOrEmpty 関数は、属性を無視し、囲まれた関数または属性が NULL または空の場合にフローから削除するようにプロビジョニング サービスに指示します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **[式]** | 必須 | Expression | 評価の対象となる式 |

**例 1: 属性が null の場合はフローしない** <br>
`IgnoreFlowIfNullOrEmpty([department])` <br>
上記の式では、プロビジョニング フローが null または空の場合は、department 属性が削除されます。 <br>

**例 2: 式マッピングが空の文字列または null と評価された場合、属性をフローしない** <br>
たとえば、SuccessFactors の属性 *prefix* を、以下の式マッピングを用いて、オンプレミスの Active Directory Domain Services の属性 *personalTitle* にマッピングしたとします。 <br>
`IgnoreFlowIfNullOrEmpty(Switch([prefix], "", "3443", "Dr.", "3444", "Prof.", "3445", "Prof. Dr."))` <br>
上の式は、まず [Switch](#switch) 関数を評価します。 *prefix* 属性が *Switch* 関数内に記載された値のいずれも持たない場合、*Switch* は空の文字列を返し、属性 *personalTitle* はオンプレミス Active Directory Domain Services へのプロビジョニング フローに含まれません。

---
### <a name="iif"></a>IIF
**関数:** IIF(condition,valueIfTrue,valueIfFalse)

**説明:** IIF 関数は、指定した条件に基づいて、使用できる一連の値のうち、いずれかを返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **condition** |必須 |変数または式 |true または false に評価できる任意の値または式。 |
| **valueIfTrue** |必須 |変数または文字列 | 条件の評価結果が true の場合に返される値。 |
| **valueIfFalse** |必須 |変数または文字列 |条件の評価結果が false の場合に返される値。|

**例:** 
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**関数:** InStr(value1, value2, start, compareType)

**説明:** InStr 関数は文字列内の最初の部分文字列を検索します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value1** |必須 |String |検索対象の文字列 |
| **value2** |必須 |String |検索する文字列 |
| **start** |省略可能 |Integer |部分文字列の検索を開始する位置|
| **compareType** |省略可能 |列挙型 |vbTextCompare か vbBinaryCompare になります |

**例:** 
`InStr("The quick brown fox","quick")`

評価結果は 5 になります。

`InStr("repEated","e",3,vbBinaryCompare)`

評価結果は 7 になります。

---
### <a name="isnull"></a>IsNull
**関数:** IsNull(Expression)

**説明:** 式の評価結果が Null の場合、IsNull 関数は true を返します。 属性の場合、Null は属性の不在によって表されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **[式]** |必須 |Expression |評価の対象となる式 |

**例:** 
`IsNull([displayName])`

属性がない場合は True が返されます。

---
### <a name="isnullorempty"></a>IsNullorEmpty
**関数:** IsNullOrEmpty(Expression)

**説明:** 式が null または空の文字列の場合、IsNullOrEmpty 関数は true を返します。 属性の場合は、属性がないか、存在しても空の文字列の場合、評価結果は True になります。
この関数の逆の関数は IsPresent です。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **[式]** |必須 |Expression |評価の対象となる式 |

**例:** 
`IsNullOrEmpty([displayName])`

属性がないか、空の文字列の場合は True が返されます。

---
### <a name="ispresent"></a>IsPresent
**関数:** IsPresent(Expression)

**説明:** 式の評価結果が Null でもなく、空でもない文字列の場合、IsPresent 関数は true を返します。 この関数の逆関数は IsNullOrEmpty です。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **[式]** |必須 |Expression |評価の対象となる式 |

**例:** 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**関数:** IsString(Expression)

**説明:** 式を文字列型として評価できる場合、IsString 関数の評価結果は True になります。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **[式]** |必須 |Expression |評価の対象となる式 |

---
### <a name="item"></a>Item
**関数:** Item(attribute, index)

**説明:** Item 関数は複数値の文字列/属性から 1 つの項目を返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |必須 |属性 |検索対象の複数値の属性 |
| **インデックス** |必須 |Integer | 複数値の文字列内の項目のインデックス|

**例:** 
`Item([proxyAddresses], 1)` は、複数値属性の 1 番目の項目を返します。 インデックス 0 は使用しないでください。 

---
### <a name="join"></a>Join
**関数:** Join(separator, source1, source2, …)

**説明:** Join() は Append() によく似ていますが、Join() では複数の **source** 文字列値を 1 つの文字列に結合できます。文字列値は **separator** で区切って指定します。

source 値の 1 つが複数値属性である場合は、その属性のすべての値を結合し、separator 値で区切ります。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **separator** |必須 |String |source 値を 1 つの文字列に連結するときに、各値を区切るのに使用する文字列。 区切り記号が必要ない場合は、“” とすることができます。 |
| **source1  … sourceN** |必須、回数は可変 |String |結合する文字列値。 |

---
### <a name="left"></a>Left
**関数:** Left(String, NumChars)

**説明:** Left 関数は文字列の左端から数えて指定した文字数分の文字を返します。 numChars = 0 の場合、空の文字列を返します。
numChars < 0 の場合、入力文字列を返します。
string が null の場合、空の文字列を返します。
string に含まれる文字数が numChars で指定した数より少ない場合は、string と同一の文字列 (パラメーター 1 のすべての文字が含まれる) が返されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **String** |必須 |属性 | 返される文字を含む文字列 |
| **NumChars** |必須 |Integer | 文字列の先頭 (左端) から取得する文字数を示す値|

**例:** 
`Left("John Doe", 3)`

"Joh" を返します。

---
### <a name="mid"></a>Mid
**関数:** Mid(source, start, length)

**説明:** source 値の部分文字列を返します。 部分文字列は、source 文字列の文字のみを含む文字列です。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常、属性の名前。 |
| **start** |必須 |Integer |部分文字列が始まる **source** 文字列のインデックス。 文字列内の最初の文字のインデックスは 1、2 番目の文字のインデックスは 2です (以降同様)。 |
| **length** |必須 |Integer |部分文字列の長さ。 length が **source** 文字列の外で終わる場合は、**start** インデックスから **source** 文字列の末尾までの部分文字列を返します。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**関数:** NormalizeDiacritics(source)

**説明:** 1 つの文字列引数が必要です。 文字列を返しますが、分音記号はそれ以外の同等の記号で置換されます。 通常、分音記号 (アクセント記号) を含む姓と名を、さまざまなユーザー識別子 (ユーザー プリンシパル名、SAM アカウント名、電子メール アドレスなど) で使用できる有効な値に変換するために使用します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String | 通常は、名または姓の属性です。 |


| 分音記号を含む文字  | 正規化された文字 | 分音記号を含む文字  | 正規化された文字 | 
| --- | --- | --- | --- | 
| ä, à, â, ã, å, á, ą, ă, ā, ā́, ā̀, ā̂, ā̃, ǟ, ā̈, ǡ, a̱, å̄ | a | Ä, À, Â, Ã, Å, Á, Ą, Ă, Ā, Ā́, Ā̀, Ā̂, Ā̃, Ǟ, Ā̈, Ǡ, A̱, Å̄ | A | 
| æ, ǣ | ae | Æ, Ǣ | AE | 
| ç, č, ć, c̄, c̱ | c | Ç, Č, Ć, C̄, C̱ | C | 
| ď, d̄, ḏ | d | Ď, D̄, Ḏ | D | 
| ë, è, é, ê, ę, ě, ė, ē, ḗ, ḕ, ē̂, ē̃, ê̄, e̱, ë̄, e̊̄ | e | Ë, È, É, Ê, Ę, Ě, Ė, Ē, Ḗ, Ḕ, Ē̂, Ē̃, Ê̄, E̱, Ë̄, E̊̄ | E | 
| ğ, ḡ, g̱ | G | Ğ, Ḡ, G̱ | G | 
| ï, î, ì, í, ı, ī, ī́, ī̀, ī̂, ī̃, i̱ | i | Ï, Î, Ì, Í, İ, Ī, Ī́, Ī̀, Ī̂, Ī̃, I̱ | I |  
| ľ, ł, l̄, ḹ, ḻ | l |  Ł, Ľ, L̄, Ḹ, Ḻ | L | 
| ñ, ń, ň, n̄, ṉ | n |  Ñ, Ń, Ň, N̄, Ṉ | N | 
| ö, ò, ő, õ, ô, ó, ō, ṓ, ṑ, ō̂, ō̃, ȫ, ō̈, ǭ, ȭ, ȱ, o̱ | o |  Ö, Ò, Ő, Õ, Ô, Ó, Ō, Ṓ, Ṑ, Ō̂, Ō̃, Ȫ, Ō̈, Ǭ, Ȭ, Ȱ, O̱ | O | 
| ø, ø̄, œ̄  | oe |  Ø, Ø̄, Œ̄ | OE | 
| ř, r̄, ṟ, ṝ | r |  Ř, R̄, Ṟ, Ṝ | R | 
| ß | ss | | | 
| š, ś, ș, ş, s̄, s̱ | s |  Š, Ś, Ș, Ş, S̄, S̱ | S | 
| ť, ț, t̄, ṯ | t | Ť, Ț, T̄, Ṯ | T | 
| ü, ù, û, ú, ů, ű, ū, ū́, ū̀, ū̂, ū̃, u̇̄, ǖ, ṻ, ṳ̄, u̱ | u |  Ü, Ù, Û, Ú, Ů, Ű, Ū, Ū́, Ū̀, Ū̂, Ū̃, U̇̄, Ǖ, Ṻ, Ṳ̄, U̱ | U | 
| ÿ, ý, ȳ, ȳ́, ȳ̀, ȳ̃, y̱ | ○ | Ÿ, Ý, Ȳ, Ȳ́, Ȳ̀, Ȳ̃, Y̱ | Y | 
| ź, ž, ż, z̄, ẕ | z | Ź, Ž, Ż, Z̄, Ẕ | Z | 


#### <a name="remove-diacritics-from-a-string"></a>文字列から分音記号を削除する
例: アクセント記号を含む文字を、アクセント記号を含まない同等の文字に置換します。

**Expression:** NormalizeDiacritics([givenName])

**サンプル入力/出力:** 

* **入力** (givenName):"Zoë"
* **出力**:"Zoe"


---
### <a name="not"></a>Not
**関数:** Not(source)

**説明:** **source** のブール値を反転します。 **source** 値が True の場合は False を返します。 そうでない場合は、True を返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |Boolean String |有効な **source** 値は "True" または "False" です。 |

---
### <a name="now"></a>Now
**関数:** Now()

**説明:**  
Now 関数は、**M/d/yyyy h:mm:ss tt** の形式で現在の UTC DateTime を表す文字列を返します。

**例:** 
`Now()` <br>
値の例は *7/2/2021 3:33:38 PM* を返しました

---
### <a name="numfromdate"></a>NumFromDate
**関数:** NumFromDate(value)

**説明:** NumFromDate 関数は、DateTime 値を、[accountExpires](/windows/win32/adschema/a-accountexpires) などの属性を設定するために必要な Active Directory 形式に変換します。 この関数を使用して、Workday や SuccessFactors などのクラウド HR アプリから受信した DateTime 値を、それと等価な AD 表現に変換します。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |必須 | String | サポートされる形式の日付と時刻の文字列。 サポートされる形式については、 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx をご覧ください。 |

**例:**
* Workday の例: *2020-12-31-08:00* 形式の Workday の *ContractEndDate* 属性を、AD の *accountExpires* フィールドにマップする場合に、この関数を使用して、ロケールに合わせてタイムゾーン オフセットを変更する方法をここで示します。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), " 23:59:59-08:00"))`

* SuccessFactors の例: *M/d/yyyy hh:mm:ss tt* 形式の SuccessFactors の *endDate* 属性を、AD の *accountExpires* フィールドにマップする場合に、この関数を使用して、ロケールに合わせてタイムゾーン オフセットを変更する方法をここで示します。
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd")," 23:59:59-08:00"))`


---
### <a name="pcase"></a>PCase
**関数:** PCase(source, wordSeparators)

**説明:** PCase 関数は、文字列内の各単語の最初の文字を大文字に変換し、その他のすべての文字は小文字に変換されます。

**パラメーター:** 

| 名前 | 必須/省略可能 | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |正しい大文字/小文字に変換する **source** 値。 |
| **wordSeparators** |省略可能 |String |単語の区切り文字として使用される一連の文字を指定します (" ,-'" など)。 |

**備考:**

* *wordSeparators* パラメーターが指定されていない場合、PCase は内部的に .NET 関数 [ToTitleCase](/dotnet/api/system.globalization.textinfo.totitlecase) を呼び出して、*source* 文字列を正しい大文字/小文字に変換します。 .NET 関数 *ToTitleCase* では、単語の区切り文字として [Unicode 文字カテゴリ](https://www.unicode.org/reports/tr44/#General_Category_Values)の包括的なセットをサポートしています。 
  * 空白文字
  * 改行文字
  * CRLF などの "*制御*" 文字
  * "*書式*" 制御文字
  * アンダースコアなどの *ConnectorPunctuation* 文字
  * ダッシュやハイフンなどの *DashPunctuation* 文字 (En ダッシュ、Em ダッシュ、ダブルハイフンなどの文字を含む)
  * かっこ、中かっこ、山かっこなどのペアで現れる *OpenPunctuation* および *ClosePunctuation* 文字。 
  * 単一引用符、二重引用符、角引用符などの *InitialQuotePunctuation* および *FinalQuotePunctuation* 文字。 
  * 感嘆符、番号記号、パーセント記号、アンパサンド、アスタリスク、コンマ、終止符、コロン、セミコロンなどの *OtherPunctuation* 文字。 
  * プラス記号、大なり記号、大なり記号、縦線、チルダ、等号などの *MathSymbol* 文字。
  * ドル記号、セント記号、シャープ記号、ユーロ記号などの *CurrencySymbol* 文字。 
  * 長音記号、アクセント、矢印などの *ModifierSymbol* 文字。 
  * 著作権記号、度記号、商標記号などの *OtherSymbol* 文字。 
* *wordSeparators* パラメーターが指定されている場合、PCase では、指定された文字のみを単語の区切り文字として使用します。 

**例:**

たとえば、属性 *firstName* と *lastName* を SAP SuccessFactors から取得しており、人事ではこれらの両方の属性が大文字になっているとします。 PCase 関数を使用すると、次に示すように、名前を正しい大文字/小文字に変換できます。 

| Expression | 入力 | Output | メモ |
| --- | --- | --- | --- |
| `PCase([firstName])` | *firstName* = "PABLO GONSALVES (SECOND)" | "Pablo Gonsalves (Second)" | *wordSeparators* パラメーターが指定されていないため、*PCase* 関数では、単語の区切り文字の既定の文字セットを使用します。 |
| `PCase([lastName]," '-")` | *lastName* = "PINTO-DE'SILVA" | "Pinto-De'Silva" | *PCase* 関数では、*wordSeparators* パラメーターの文字を使用して単語を識別し、それらを正しい大文字/小文字に変換します。 |
| `PCase(Join(" ",[firstName],[lastName]))` | *firstName* = GREGORY, *lastName* = "JAMES" | "Gregory James" | Join 関数を PCase 内で入れ子にすることができます。 *wordSeparators* パラメーターが指定されていないため、*PCase* 関数では、単語の区切り文字の既定の文字セットを使用します。  |


---

### <a name="randomstring"></a>RandomString
**関数:** RandomString(Length, MinimumNumbers, MinimumSpecialCharacters , MinimumCapital, MinimumLowerCase, CharactersToAvoid)

**説明:** RandomString 関数は、指定された条件に基づいてランダムな文字列を生成します。 使用できる文字は、[こちら](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference)で確認できます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **Length** |必須 |数値 |ランダムな文字列の合計文字数。 MinimumNumbers、MinimumSpecialCharacters、MinimumCapital の合計と同じか、それ以上であることが必要です。 最大 256 文字です。|
| **MinimumNumbers** |必須 |数値 |ランダムな文字列に含まれる数字の最小文字数。|
| **MinimumSpecialCharacters** |必須 |数値 |特殊文字の最小文字数。|
| **MinimumCapital** |必須 |数値 |ランダムな文字列に含まれる大文字の最小文字数。|
| **MinimumLowerCase** |必須 |数値 |ランダムな文字列に含まれる小文字の最小文字数。|
| **CharactersToAvoid** |オプション |String |ランダムな文字列を生成するときに除外する文字。|


**例 1:** - 特殊文字の制限なしでランダムな文字列を生成する: `RandomString(6,3,0,0,3)`
6 文字のランダムな文字列を生成します。 文字列には数字が 3 つと小文字が 3 つ含まれます (1a73qt)。

**例 2:** - 特殊文字の制限付きでランダムな文字列を生成する: `RandomString(10,2,2,2,1,"?,")`
10 文字のランダムな文字列を生成します。 この文字列には、少なくとも数字が 2 つ、特殊文字が 2 つ、大文字が 2 つ、小文字が 1 つ含まれており、"?" 文字と "," 文字は除外されます (1@!2BaRg53)。

---
### <a name="redact"></a>Redact
**関数:** Redact()

**説明:** Redact 関数は、プロビジョニング ログで、属性値を文字列リテラル "[Redact]" に置き換えます。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute/value** |必須 |String|ログで編集する属性または定数/文字列を指定します。|

**例 1:** 属性を編集します: `Redact([userPrincipalName])`
プロビジョニング ログから userPrincipalName を削除します。

**例 2:** 文字列を編集します: `Redact("StringToBeRedacted")`
プロビジョニング ログから定数文字列を削除します。

**例 3:** ランダムな文字列を編集します: `Redact(RandomString(6,3,0,0,3))`
プロビジョニング ログからランダムな文字列を削除します。

---
### <a name="removeduplicates"></a>RemoveDuplicates
**関数:** RemoveDuplicates(attribute)

**説明:** RemoveDuplicates 関数は複数値の文字列を受け取り、各値が一意になるように処理します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |必須 |複数値の属性 |重複が削除される複数値の属性|

**例:** 
`RemoveDuplicates([proxyAddresses])` 重複する値がすべて削除された、校正済みの proxyAddress 属性を返します。

---
### <a name="replace"></a>Replace
**関数:** Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**説明:** 大文字と小文字を区別して、文字列内の値を置換します。 この関数は指定されたパラメーターに応じて異なる動作をします。

* **oldValue** と **replacementValue** が指定された場合:
  
  * **source** に含まれるすべての **oldValue** を **replacementValue** に置換します。
* **oldValue** と **template** が指定された場合:
  
  * **template** に含まれるすべての **OldValue** を **source** 値に置換します。
* **regexPattern** と **replacementValue** が指定された場合:

  * この関数で **regexPattern** が **source** の文字列に適用され、regex グループ名を使って **replacementValue** の文字列を作成できます。
* **regexPattern**、**regexGroupName**、**replacementValue** が指定された場合:
  
  * この関数で **regexPattern** が **source** の文字列に適用され、**regexGroupName** と一致するすべての値が **replacementValue** に置き換えられます
* **regexPattern**、**regexGroupName**、**replacementAttributeName** が指定された場合:
  
  * **source** に値が指定されていない場合は、**source** を返します。
  * **source** に値がある場合、この関数で **regexPattern** が **source** の文字列に適用され、**regexGroupName** と一致するすべての値が **replacementAttributeName** に関連付けられた値に置き換えられます

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、**source** オブジェクトの属性の名前。 |
| **oldValue** |省略可能 |String |**source** または **template** に含まれる置換前の値。 |
| **regexPattern** |省略可能 |String |**source** に含まれる置換前の値の正規表現パターン。 または、**replacementPropertyName** が使われるときは、**replacementPropertyName** から値を抽出するパターン。 |
| **regexGroupName** |省略可能 |String |**regexPattern** 内のグループの名前。 **replacementPropertyName** を使用した場合にのみ、このグループの値が **replacementPropertyName** から **replacementValue** として抽出されます。 |
| **replacementValue** |省略可能 |String |古い値を置き換える新しい値。 |
| **replacementAttributeName** |省略可能 |String |置換値に使用する属性の名前 |
| **template** |省略可能 |String |**template** の値を指定した場合、template 内で **oldValue** が検索され、**source** の値で置換されます。 |

#### <a name="replace-characters-using-a-regular-expression"></a>正規表現を使用して文字を置換します
例: 正規表現の値と一致する文字を見つけて削除する必要があります。

**Expression:** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**サンプル入力/出力:**

* **INPUT** (mailNickname: "john_doe72"
* **出力**:"72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**関数:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**説明:** 少なくとも 2 つの引数が必要です。引数は、式を使用して定義されている一意値生成ルールです。 関数では、各ルールが評価された後、生成された値の対象となるアプリ/ディレクトリでの一意性が確認されます。 最初に見つかった一意の値が返されます。 すべての値がターゲットに既に存在する場合、エントリはエスクローされて、理由が監査ログに記録されます。 渡すことができる引数の数に上限はありません。


 - この関数は最上位に配置する必要があり、入れ子にすることはできません。
 - この関数は、照合の優先順位を持つ属性には適用できません。     
 - この関数は、エントリの作成に使用されることだけを目的としたものです。 属性で使用するときは、 **[Apply Mapping]\(マッピングの適用\)** プロパティを **[オブジェクトの作成中のみ]** に設定します。
 - この関数は現在、"Workday から Active Directory へのユーザー プロビジョニング" と "SuccessFactors から Active Directory へのユーザー プロビジョニング" でのみサポートされています。 他のプロビジョニング アプリでは使用できません。 
 - *SelectUniqueValue* 関数がオンプレミスの Active Directory で実行する LDAP 検索では、分音記号などの特殊文字がエスケープされません。 特殊文字を含む文字列 ("Jéssica Smith" など) を渡した場合、処理エラーが発生します。 以下の例のように [NormalizeDiacritics](#normalizediacritics) 関数を入れ子にして、特殊文字を正規化してください。 


**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |2 つ以上必要であり、上限はありません |String | 評価する一意値生成ルールの一覧。 |

#### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>userPrincipalName (UPN) 属性用に一意の値を生成する
例: ユーザーの名、ミドル ネーム、姓を基にして UPN 属性の値を生成し、値を UPN 属性に割り当てる前に、対象の AD ディレクトリで値が一意であることを確認する必要があります。

**Expression:** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**サンプル入力/出力:**

* **入力** (PreferredFirstName):"John"
* **入力** (PreferredLastName):"Smith"
* **出力**: John.Smith@contoso.com の UPN 値がディレクトリにまだ存在しない場合は "John.Smith@contoso.com"
* **出力**: John.Smith@contoso.com の UPN 値がディレクトリに既に存在する場合は "J.Smith@contoso.com"
* **出力**: 上記の 2 つの UPN 値がディレクトリに既に存在する場合は "Jo.Smith@contoso.com"



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**関数:** SingleAppRoleAssignment(appRoleAssignments)

**説明:** 指定したアプリケーションで 1 人のユーザーに割り当てられた appRoleAssignments の全一覧から、単一の appRoleAssignment を返します。 appRoleAssignments オブジェクトを単一のロール名文字列に変換するために、この関数が必要になります。 ベスト プラクティスは、確実に 1 つの appRoleAssignment だけが一度に 1 人のユーザーに割り当てられるようにすることです。複数のロールが割り当てられると、返されるロール文字列を予測できません。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **appRoleAssignments** |必須 |String |**appRoleAssignments** オブジェクト |

---
### <a name="split"></a>Split
**関数:** Split(source, delimiter)

**説明:** 指定された区切り記号文字を使用して、文字列を複数値の配列に分割します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |**source** 値。 |
| **delimiter** |必須 |String |文字列の分割に使用される文字を指定します (例: ",") |

#### <a name="split-a-string-into-a-multi-valued-array"></a>文字列を複数値の配列に分割します
例: コンマ区切りの一覧になっている文字列を受け取り、Salesforce の PermissionSets 属性などの複数値の属性にプラグインできる配列に分割する必要があります。 この例では、アクセス許可セットの一覧が、Azure AD の extensionAttribute5 に格納されています。

**Expression:** Split([extensionAttribute5], ",")

**サンプル入力/出力:** 

* **INPUT** (extensionAttribute5):
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
**関数:** StripSpaces(source)

**説明:** source 文字列からすべての空白文字 (" ") を削除します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |**source** 値。 |

---
### <a name="switch"></a>Switch
**関数:** Switch(source, defaultValue, key1, value1, key2, value2, …)

**説明:** **source** 値が **key** と一致するときに、その **key** の **value** を返します。 **source** 値がどの key とも一致しない場合は、**defaultValue** を返します。  **key** と **value** パラメーターは、常にペアで指定する必要があります。 この関数には、常に、偶数個のパラメーターを指定する必要があります。 この関数は、manager などの参照属性には使用しないでください。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |**Source** 値。 |
| **defaultValue** |省略可能 |String |source がどの key とも一致しないときに使用される既定値。 空の文字列 ("") を指定できます。 |
| **key** |必須 |String |**source** 値と比較する **key**。 |
| **value** |必須 |String |key と一致する **source** の置換値。 |

#### <a name="replace-a-value-based-on-predefined-set-of-options"></a>定義済みのオプション セットに基づいて値を置換する
例: Azure AD に格納されている都道府県コードに基づいて、ユーザーのタイム ゾーンを定義します。 都道府県コードが定義済みオプションのいずれにも一致しない場合は、既定値 "Australia/Sydney" を使用します。

**式:**  
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**サンプル入力/出力:**

* **入力** (state):"QLD"
* **出力**:"Australia/Brisbane"


---
### <a name="tolower"></a>ToLower
**関数:** ToLower(source, culture)

**説明:** *source* 文字列値を受け取り、指定されたカルチャ ルールを使用して小文字に変換します。 *culture* 情報が指定されていない場合は、インバリアント カルチャが使用されます。

ターゲット システムの既存の値を小文字に設定する場合は、[ターゲット アプリケーションのスキーマを更新](./customize-application-attributes.md#editing-the-list-of-supported-attributes)し、対象の属性のプロパティ caseExact を "true" に設定します。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **culture** |省略可能 |String |RFC 4646 に基づくカルチャ名の形式は、*languagecode2-country/regioncode2* です。ここで、*languagecode2* は 2 文字の言語コードで、*country/regioncode2* は 2 文字のサブカルチャ コードです。 例には、日本語 (日本) の場合の ja-JP と英語 (米国) の場合の en-US が含まれています。 2 文字の言語コードが使用できない場合は、ISO 639-2 から派生した 3 文字のコードが使用されます。|

#### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>生成された userPrincipalName (UPN) の値を小文字に変換します
例: PreferredFirstName および PreferredLastName のソース フィールドを連結して、すべての文字を小文字に変換することで、UPN 値を生成する場合があります。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**サンプル入力/出力:**

* **入力** (PreferredFirstName):"John"
* **入力** (PreferredLastName):"Smith"
* **出力**: "john.smith@contoso.com"


---
### <a name="toupper"></a>ToUpper
**関数:** ToUpper(source, culture)

**説明:** *source* 文字列値を受け取り、指定されたカルチャ ルールを使用して大文字に変換します。 *culture* 情報が指定されていない場合は、インバリアント カルチャが使用されます。

ターゲット システムの既存の値を大文字に設定する場合は、[ターゲット アプリケーションのスキーマを更新](./customize-application-attributes.md#editing-the-list-of-supported-attributes)し、対象の属性のプロパティ caseExact を "true" に設定します。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **culture** |省略可能 |String |RFC 4646 に基づくカルチャ名の形式は、*languagecode2-country/regioncode2* です。ここで、*languagecode2* は 2 文字の言語コードで、*country/regioncode2* は 2 文字のサブカルチャ コードです。 例には、日本語 (日本) の場合の ja-JP と英語 (米国) の場合の en-US が含まれています。 2 文字の言語コードが使用できない場合は、ISO 639-2 から派生した 3 文字のコードが使用されます。|

---
### <a name="word"></a>Word
**関数:** Word(String,WordNumber,Delimiters)

**説明:** Word 関数は、使用する区切り記号と返す単語の番号を表すパラメーターに基づいて、文字列内に含まれる単語を返します。 delimiters 内のいずれかの文字で区切られた string 内の各文字列が、単語として識別されます。

num < 1 の場合、空の文字列を返します。
string が null の場合、空の文字列を返します。
string に含まれる単語の数が指定より少ないか、区切り記号文字で識別されるどの単語も string に含まれていない場合は、空の文字列が返されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **String** |必須 |複数値の属性 |返される単語を含む文字列|
| **WordNumber** |必須 | Integer | 返すべき単語の番号を指定する数値|
| **delimiters** |必須 |String| 単語を識別するために使用される区切り記号を表す文字列|

**例:** 
`Word("The quick brown fox",3," ")`

"brown" が返されます。

`Word("This,string!has&many separators",3,",!&#")`

"has" が返されます。

---

## <a name="examples"></a>例
ここでは、式関数の使用例をさらに詳しく説明します。 

### <a name="strip-known-domain-name"></a>既知のドメイン名をストリップする
ユーザーの電子メールから既知のドメイン名をストリップして、ユーザー名を取得します。 たとえば、ドメインが "contoso.com" の場合は、次の式を使用することができます。

**式:**  
`Replace([mail], "@contoso.com", , ,"", ,)`

**サンプル入力/出力:** 

* **入力** (mail): "john.doe@contoso.com"
* **出力**: "john.doe"


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>姓の一部と名の一部を連結することでユーザー エイリアスを生成する
ユーザーの名の最初の 3 文字とユーザーの姓の最初の 5 文字を取得することでユーザー エイリアスを生成します。

**式:**  
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**サンプル入力/出力:** 

* **入力** (givenName):"John"
* **入力** (surname):"Doe"
* **出力**:"JohDoe"


## <a name="related-articles"></a>関連記事
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](../app-provisioning/customize-application-attributes.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [アカウント プロビジョニング通知](../app-provisioning/user-provisioning.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
