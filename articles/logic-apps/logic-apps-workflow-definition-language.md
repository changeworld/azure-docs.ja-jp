---
title: ワークフロー定義言語のスキーマ参照 - Azure Logic Apps | Microsoft Docs
description: ワークフロー定義言語を使って Azure Logic Apps 用のカスタム ワークフロー定義を記述します。
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 0ac191f1191357ecc7292d51b298567f7f4e4786
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391226"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Azure Logic Apps のワークフロー定義言語のスキーマ参照

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用してロジック アプリのワークフローを作成するときは、ワークフローの基になる定義で、ロジック アプリに対して実行される実際のロジックを記述します。 この記述は、ワークフロー定義言語スキーマによって定義され、検証される構造に従います。このスキーマでは、[JavaScript Object Notation (JSON)](https://www.json.org/) が使用されます。 
  
## <a name="workflow-definition-structure"></a>ワークフロー定義の構造

ワークフローの定義には、ロジック アプリをインスタンス化する少なくとも 1 つのトリガーと、ロジック アプリが実行する 1 つまたは複数のアクションが含まれます。 

ワークフロー定義の高レベルな構造を次に示します。  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| 要素 | 必須 | 説明 | 
|---------|----------|-------------| 
| 定義 | [はい] | ワークフロー定義の開始要素 | 
| $schema | ワークフロー定義を外部参照する場合のみ | ワークフロー定義言語のバージョンが記述されている JSON スキーマ ファイルの場所。次の場所にあります。 <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | いいえ  | ワークフロー定義のバージョン番号。既定値は "1.0.0.0" です。 ワークフローを展開するときに正しい定義であることを識別して確認できるように、使用する値を指定します。 | 
| parameters | いいえ  | ワークフローにデータを渡す 1 つ以上のパラメーターの定義 <p><p>パラメーターの最大個数: 50 | 
| トリガー | いいえ  | ワークフローをインスタンス化する 1 つまたは複数のトリガーの定義。 複数のトリガーを定義できます。ワークフロー定義言語しか利用できず、Logic Apps デザイナーを使って視覚的に作成することはできません。 <p><p>トリガーの最大個数: 10 | 
| アクション | いいえ  | ワークフローの実行時に実行する 1 つまたは複数のアクションの定義 <p><p>アクションの最大個数: 250 | 
| outputs | いいえ  | ワークフローの実行から返される出力の定義 <p><p>出力の最大個数: 10 |  
|||| 

## <a name="parameters"></a>parameters

`parameters` セクションでは、ロジック アプリが展開時に入力を受け取るために使用するすべてのワークフロー パラメーターを定義します。 パラメーターの宣言とパラメーターの値の両方が、展開時に必要です。 他のワークフロー セクションでこれらのパラメーターを使用する前に、これらのセクションですべてのパラメーターを宣言したことを確認してください。 

パラメーターの定義の一般的な構造を次に示します。  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| 要素 | 必須 | Type | 説明 |  
|---------|----------|------|-------------|  
| type | [はい] | int、float、string、securestring、bool、array、JSON オブジェクト、secureobject <p><p>**注**: すべてのパスワード、キー、およびシークレットで、`securestring` 型と `secureobject` 型を使用します。`GET` 操作では、これらの型は返されません。 | パラメーターの型 |
| defaultValue | いいえ  | `type` と同じ | ワークフローのインスタンス化時に値が指定されていない場合の、既定のパラメーター値 | 
| allowedValues | いいえ  | `type` と同じ | パラメーターが受け取ることのできる値の配列 |  
| metadata | いいえ  | JSON オブジェクト | 他のパラメーターの詳細。たとえば、ロジック アプリの名前や読み取り可能な説明、または Visual Studio や他のツールによって使用される設計時のデータ |  
||||

## <a name="triggers-and-actions"></a>トリガーとアクション  

ワークフロー定義の `triggers` および `actions` セクションでは、ワークフローの実行中に発生する呼び出しを定義します。 これらのセクションの構文と詳細については、「[ワークフローのトリガーとアクション](../logic-apps/logic-apps-workflow-actions-triggers.md)」をご覧ください。
  
## <a name="outputs"></a>出力 

`outputs` セクションでは、実行完了時にワークフローが返すことのできるデータを定義します。 たとえば、各実行から特定の状態または値を追跡するには、ワークフローの出力がそのデータを返すように指定します。 

> [!NOTE]
> サービスの REST API からの受信要求に応答するときは、`outputs` を使わないでください。 代わりに、`Response` アクションの種類を使います。 詳しくは、「[ワークフローのトリガーとアクション](../logic-apps/logic-apps-workflow-actions-triggers.md)」をご覧ください。

出力の定義の一般的な構造を次に示します。 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| 要素 | 必須 | Type | 説明 | 
|---------|----------|------|-------------| 
| <*key-name*> | [はい] | String | 出力戻り値のキーの名前 |  
| type | [はい] | int、float、string、securestring、bool、array、JSON オブジェクト | 出力戻り値の型 | 
| value | [はい] | `type` と同じ | 出力の戻り値 |  
||||| 

ワークフローの実行からの出力を取得するには、Azure portal でロジック アプリの実行履歴と詳細を確認するか、または [ワークフローの REST API](https://docs.microsoft.com/rest/api/logic/workflows) を使います。 Power BI などの外部システムに出力を渡してダッシュボードを作成することもできます。 

<a name="expressions"></a>

## <a name="expressions"></a>式

JSON では、デザイン時に存在するリテラル値を使用できます。次はその例です。

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

実行時まで存在しない値を使うこともできます。 これらの値を表すには、実行時に評価される "*式*" を使うことができます。 式は、1 つ以上の[関数](#functions)、[演算子](#operators)、変数、明示的な値、または定数を含むことができるシーケンスです。 ワークフローの定義では、式の前にアットマーク (\@) を付けることによって、JSON 文字列値の任意の場所で式を使うことができます。 JSON 値を表す式を評価するときは、\@ 文字を削除することによって式の本体が抽出され、常に別の JSON 値になります。 

たとえば、前に定義した `customerName` プロパティでは、式の中で [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 関数を使ってプロパティの値を取得し、その値を `accountName` プロパティに割り当てることができます。

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

"*文字列の補間*" により、\@ 文字と中かっこ ({}) によってラップされている文字列内で複数の式を使用することもできます。 構文は次のとおりです。

```json
@{ "<expression1>", "<expression2>" }
```

結果は常に文字列であり、この機能は `concat()` 関数と似ています。次に例を示します。 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

\@ 文字で始まるリテラル文字列がある場合は、エスケープ文字として別の \@ 文字を \@ の前に付けます (\@\@)。

式の評価方法の例を次に示します。

| JSON 値 | 結果 |
|------------|--------| 
| "Sophia Owen" | 文字 'Sophia Owen' を返します |
| "array[1]" | 文字 'array[1]' を返します |
| "\@\@" | 1 文字の '\@' を返します |   
| " \@" | 2 文字の ' \@' を返します |
|||

次の例では、"myBirthMonth" を "January" に、"myAge" を数値 42 に定義してあるものとします。  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

式の評価方法の例を次に示します。

| JSON 式 | 結果 |
|-----------------|--------| 
| "\@parameters('myBirthMonth')" | 文字列 "January" が返されます |  
| "\@{parameters('myBirthMonth')}" | 文字列 "January" が返されます |  
| "\@parameters('myAge')" | 数値 42 が返されます |  
| "\@{parameters('myAge')}" | 文字列 "42" が返されます |  
| "My age is \@{parameters('myAge')}" | 文字列 "My age is 42" が返されます |  
| "\@concat('My age is ', string(parameters('myAge')))" | 文字列 "My age is 42" が返されます |  
| "My age is \@\@{parameters('myAge')}" | 式を含む文字列 "My age is \@{parameters('myAge')}" が返されます | 
||| 

Logic Apps デザイナーで視覚的に作業しているときは、式ビルダーなどで式を作成できます。 

![Logic Apps デザイナー > 式ビルダー](./media/logic-apps-workflow-definition-language/expression-builder.png)

完了すると、ワークフロー定義内の対応するプロパティに式が表示されます。次の例では `searchQuery` プロパティです。

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>演算子

[式](#expressions)と[関数](#functions)では、演算子はプロパティや配列内の値の参照などの特定のタスクを実行します。 

| operator | タスク | 
|----------|------|
| ' | 入力として、または式や関数の中で文字列リテラルを使うには、単一引用符で文字列のみをラップします (例: `'<myString>'`)。 二重引用符を ("") を使用しないでください。式全体を囲む JSON の書式設定と競合します。 例:  <p>**正しい**: length('Hello') </br>**正しくない**: length("Hello") <p>配列または数値を渡すとき、句読点をラップする必要はありません。 例:  <p>**正しい**: length([1, 2, 3]) </br>**正しくない**: length("[1, 2, 3]") | 
| [] | 配列内の特定の位置 (インデックス) にある値を参照するには、角かっこを使います。 たとえば、配列内の 2 番目の項目を取得するには次のようにします。 <p>`myArray[1]` | 
| . | オブジェクト内のプロパティを参照するには、ドット演算子を使用します。 たとえば、`customer` JSON オブジェクトの `name` プロパティを取得するには、次のようにします。 <p>`"@parameters('customer').name"` | 
| ? | 実行時エラーを発生させずにオブジェクト内の null プロパティを参照するには、疑問符演算子を使います。 たとえば、次の式を使うと、トリガーからの null 出力を処理できます。 <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Functions

一部の式では、ロジック アプリの実行開始時には存在しない可能性のある値を実行時のアクションから取得します。 このような値を式で参照または使用するには、"[*関数*](../logic-apps/workflow-definition-language-functions-reference.md)" を使います。 たとえば、計算に数学関数をつかうことができます。[add()](../logic-apps/workflow-definition-language-functions-reference.md#add) 関数は、整数または浮動小数点数から合計を返します。 各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。
または、関数とその一般的な用途についての学習を続けてください。

関数を使用して実行できるタスクの例を 2 つ示します。 

| タスク | 関数の構文 | 結果 | 
| ---- | --------------- | ------ | 
| 小文字の形式で文字列を返します。 | toLower('<*text*>') <p>例: toLower('Hello') | "hello" | 
| グローバル一意識別子 (GUID) を返します。 | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

次の例では、`customerName` パラメーターから値を取得し、式の中で [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 関数を使ってその値を `accountName` プロパティに割り当てる方法を示します。

```json
"accountName": "@parameters('customerName')"
```

式で関数を使用できる他の一般的な方法を次に示します。

| タスク | 式の中での関数の構文 | 
| ---- | -------------------------------- | 
| 項目を関数に渡すことにより、項目に関する作業を実行します。 | "\@<*functionName*>(<*item*>)" | 
| 1.入れ子になった `parameters()` 関数を使って、*parameterName* の値を取得します。 </br>2.その値を *functionName* に渡すことによって、結果に関する作業を実行します。 | "\@<*functionName*>(parameters('<*parameterName*>'))" | 
| 1.入れ子になった内側の関数 *functionName* から結果を取得します。 </br>2.結果を外側の関数 *functionName2* に渡します。 | "\@<*functionName2*>(<*functionName*>(<*item*>))" | 
| 1.*functionName* から結果を取得します。 </br>2.結果がプロパティ *propertyName* を含むオブジェクトであるとして、そのプロパティの値を取得します。 | "\@<*functionName*>(<*item*>).<*propertyName*>" | 
||| 

たとえば、`concat()` 関数はパラメーターとして 2 つ以上の文字列値を受け取ることができます。 この関数は、これらの文字列を 1 つの文字列に結合します。 たとえば "Sophia" と "Owen" のような文字列リテラルを渡して、結合された文字列 "SophiaOwen" を取得できます。

```json
"customerName": "@concat('Sophia', 'Owen')"
```

または、パラメーターから文字列値を取得することができます。 次の例では、各 `concat()` パラメーターおよび `firstName` パラメーターと `lastName` パラメーターで、`parameters()` 関数を使っています。 その後、結果の文字列を `concat()` 関数に渡して、結合された文字列 ("SophiaOwen" など) を取得します。

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

どちらの例でも、`customerName` プロパティに結果を割り当てています。 

各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。
または、一般的な用途に基づく関数についての学習を続けてください。

<a name="string-functions"></a>

### <a name="string-functions"></a>文字列関数

文字列を処理するには、以下の文字列関数および一部の[コレクション関数](#collection-functions)も使用できます。 文字列関数は文字列でのみ機能します。 

| 文字列関数 | タスク | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | 2 つ以上の文字列を結合し、結合された文字列を返します。 | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | 文字列が指定された部分文字列で終わっているかどうかを調べます。 | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | 文字列としてグローバル一意識別子 (GUID) を生成します。 | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | 部分文字列の開始位置を返します。 | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | 部分文字列の終了位置を返します。 | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | 部分文字列を指定した文字列で置換し、更新された文字列を返します。 | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | 文字列のすべての文字を含み、特定の区切り記号で各文字が区切られた配列を返します。 | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | 文字列が特定の部分文字列で始まっているかどうかを調べます。 | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | 文字列から、指定された位置から始まる文字を返します。 | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | 小文字の形式で文字列を返します。 | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | 大文字の形式で文字列を返します。 | 
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | 文字列から先頭と末尾の空白を削除し、更新された文字列を返します。 | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>コレクション関数

コレクション (通常は配列や文字列、場合によってはディクショナリ) を操作するには、以下のコレクション関数を使用できます。 

| コレクション関数 | タスク | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | コレクションに特定の項目があるかどうかを確認します。 |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | コレクションが空かどうかを調べます。 | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | コレクションから最初の項目を返します。 | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | 指定したコレクションすべてに共通する項目 "*のみ*" を含むコレクションを返します。 | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | 配列の "*すべて*" の項目を含み、指定された区切り記号で各項目が区切られた、文字列を返します。 | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | コレクションから最後の項目を返します。 | 
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | 文字列または配列内の項目の数を返します。 | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | コレクションの先頭から項目を削除し、"*他のすべて*" の項目を返します。 | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | コレクションの先頭から項目を返します。 | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | 指定した複数のコレクションの "*すべての*" 項目を含む 1 つのコレクションを返します。 | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>比較関数

条件の処理、値と式の結果の比較、さまざまな種類のロジックの評価などを行うには、以下の比較関数を使用できます。 各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。

| 比較関数 | タスク | 
| ------------------- | ---- | 
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | すべての式が true かどうかを調べます。 | 
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | 両方の値が等しいかどうかを調べます。 | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | 1 番目の値が 2 番目の値より大きいかどうかを調べます。 | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | 1 番目の値が 2 番目の値以上かどうかを調べます。 | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | 式が true か false かを調べます。 結果に基づき、指定された値を返します。 | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | 1 番目の値が 2 番目の値より小さいかどうかを調べます。 | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | 1 番目の値が 2 番目の値以下かどうかを調べます。 | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | 式が false かどうかを調べます。 | 
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | 少なくとも 1 つの式が true かどうかを調べます。 |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>変換関数

値の型または形式を変更するには、以下の変換関数を使用できます。 たとえば、値をブール値から整数に変更できます。 Logic Apps が変換時にコンテンツ タイプを処理する方法については、「[コンテンツ タイプを処理する](../logic-apps/logic-apps-content-type.md)」をご覧ください。 各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。

| 変換関数 | タスク | 
| ------------------- | ---- | 
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | 指定した 1 つの入力から配列を返します。 複数の入力の場合は、[createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) をご覧ください。 | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | 文字列の base64 エンコード バージョンを返します。 | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | base64 エンコード文字列のバイナリ バージョンを返します。 | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | base64 エンコード文字列の文字列バージョンを返します。 | 
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | 入力値のバイナリ バージョンを返します。 | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | 入力値のブール値バージョンを返します。 | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | 複数の入力から配列を作成して返します。 | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | 入力値のデータ URI を返します。 | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | データ URI のバイナリ バージョンを返します。 | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | データ URI の文字列バージョンを返します。 | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | base64 エンコード文字列の文字列バージョンを返します。 | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | データ URI のバイナリ バージョンを返します。 | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | エスケープ文字をデコード バージョンに置き換えた文字列を返します。 | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | URL の安全でない文字をエスケープ文字に置き換えた文字列を返します。 | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | 入力値の浮動小数点数を返します。 | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | 文字列の整数バージョンを返します。 | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | 文字列または XML に対する JSON (JavaScript Object Notation) 型の値またはオブジェクトを返します。 | 
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | 入力値の文字列バージョンを返します。 | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | URL の安全でない文字がエスケープ文字に置き換えられた、入力値の URI エンコード バージョンを返します。 | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | URI エンコード文字列のバイナリ バージョンを返します。 | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | URI エンコード文字列の文字列バージョンを返します。 | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | 文字列の XML バージョンを返します。 | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>算術関数

整数と浮動小数点数を操作するには、以下の算術関数を使用できます。 各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。

| 算術関数 | タスク | 
| ------------- | ---- | 
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | 2 つの数値を加算した結果を返します。 | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | 2 つの数値を除算した結果を返します。 | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | 数値のセットまたは配列から最大の値を返します。 | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | 数値のセットまたは配列から最小の値を返します。 | 
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | 2 つの数値を除算した剰余を返します。 | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | 2 つの数値を乗算した積を返します。 | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | 指定された範囲からランダムな整数を返します。 | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | 指定した整数から始まる整数の配列を返します。 | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | 1 番目の数値から 2 番目の数値を減算して、結果を返します。 | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>日付と時刻関数

日付と時刻を操作するには、以下の日付と時刻の関数を使用できます。
各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。

| 日付または時刻の関数 | タスク | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | タイムスタンプに日数を加算します。 | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | タイムスタンプに時間数を加算します。 | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | タイムスタンプに分数を加算します。 | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | タイムスタンプに秒数を加算します。 |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | タイムスタンプに時間単位数を加算します。 [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) もご覧ください。 | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | タイムスタンプを協定世界時 (UTC) からターゲット タイム ゾーンに変換します。 | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。 | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | タイムスタンプをソース タイム ゾーンから協定世界時 (UTC) に変換します。 | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | タイムスタンプから月コンポーネントの日付を返します。 | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | タイムスタンプから曜日を返します。 | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | タイムスタンプから年の何日目かを返します。 | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | タイムスタンプから日付を返します。 | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | 現在のタイムスタンプに指定した時刻単位を加えて返します。 [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) もご覧ください。 | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | 現在のタイムスタンプから指定した時刻単位を引いて返します。 [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) もご覧ください。 | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | タイムスタンプの日の開始日時を返します。 | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | タイムスタンプの時刻の開始を返します。 | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | タイムスタンプの月の開始を返します。 | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | タイムスタンプから時間単位数を減算します。 [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) もご覧ください。 | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | 指定したタイムスタンプの `ticks` プロパティの値を返します。 | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | 現在のタイムスタンプを文字列として返します。 | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>ワークフロー関数

以下のワークフロー関数を使うと次のことができます。

* 実行時にワークフロー インスタンスの詳細を取得します。 
* ロジック アプリのインスタンス化に使われた入力を処理します。
* トリガーやアクションからの出力を参照します。

たとえば、あるアクションからの出力を参照し、後のアクションでそのデータを使用できます。 各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。

| ワークフロー関数 | タスク | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | 実行時に現在のアクションの出力を返すか、または他の JSON の名前と値のペアの値を返します。 [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) もご覧ください。 | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | 実行時のアクションの `body` 出力を返します。 [body](../logic-apps/workflow-definition-language-functions-reference.md#body) もご覧ください。 | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | 実行時のアクションの出力を返します。 [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) をご覧ください。 | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | 実行時にアクションの出力を返すか、または他の JSON の名前と値のペアの値を返します。 [action](../logic-apps/workflow-definition-language-functions-reference.md#action) もご覧ください。  | 
| [body](#body) | 実行時のアクションの `body` 出力を返します。 [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) もご覧ください。 | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | *form-data* または *form-encoded* アクション出力内のキー名と一致する値を含む配列を作成します。 | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | アクションの *form-data* 出力または *form-encoded* 出力内のキー名と一致する単一の値を返します。 | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 配列に対する繰り返しアクションの内部で使うと、アクションの現在の繰り返しの間に配列の現在の項目を返します。 | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | for-each または do-until ループの内部で使用すると、指定したループから現在の項目を返します。| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | トリガーまたはアクションを呼び出す "コールバック URL" を返します。 | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | 複数の部分を持つアクションの出力の特定の部分に対する本文を返します。 | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | ロジック アプリの定義で記述されているパラメーターの値を返します。 | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | 実行時に、または他の JSON の名前と値のペアから、トリガーの出力を返します。 [triggerOutputs](#triggerOutputs) および [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) もご覧ください。 | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | 実行時にトリガーの `body` 出力を返します。 [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) をご覧ください。 | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | *form-data* または *form-encoded* トリガー出力のキー名と一致する単一の値を返します。 | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | トリガーの複数部分からなる出力内の特定の部分の本文を返します。 | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | *form-data* または *form-encoded* トリガー出力内のキー名と一致する値を含む配列を作成します。 | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | 実行時のトリガーの出力を返すか、または他の JSON の名前と値のペアの値を返します。 [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) をご覧ください。 | 
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | 指定した変数の値を返します。 | 
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | 実行時にワークフロー自体に関するすべての詳細を返します。 | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>URI 解析関数

URI (Uniform Resource Identifier) を処理して、URI のさまざまなプロパティ値を取得します。以下の URI 解析関数を使用できます。 各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。

| URI 解析関数 | タスク | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | URI (Uniform Resource Identifier) の `host` 値を返します。 | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | URI (Uniform Resource Identifier) の `path` 値を返します。 | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | URI (Uniform Resource Identifier) の `path` と `query` の値を返します。 | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | URI (Uniform Resource Identifier) の `port` 値を返します。 | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | URI (Uniform Resource Identifier) の `query` 値を返します。 | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | URI (Uniform Resource Identifier) の `scheme` 値を返します。 | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>JSON および XML 関数

JSON オブジェクトと XML ノードを処理するには、以下の操作関数を使用できます。 各関数について詳しくは、[アルファベット順のリファレンス記事](../logic-apps/workflow-definition-language-functions-reference.md)をご覧ください。

| 操作関数 | タスク | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | JSON オブジェクトにプロパティとその値または名前と値のペアを追加し、更新されたオブジェクトを返します。 | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | 1 つまたは複数のパラメーターから、最初の null 以外の値を返します。 | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | JSON オブジェクトからプロパティを削除し、更新されたオブジェクトを返します。 | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | JSON オブジェクトのプロパティの値を設定し、更新されたオブジェクトを返します。 | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | XML で XPath (XML Path Language) 式と一致するノードまたは値を調べて、一致するノードまたは値を返します。 | 
||| 

## <a name="next-steps"></a>次の手順

* [ワークフロー定義言語のアクションとトリガー](../logic-apps/logic-apps-workflow-actions-triggers.md)について学習する
* [ワークフローの REST API](https://docs.microsoft.com/rest/api/logic/workflows) を使用してプログラムによってロジック アプリを作成および管理する方法を学習する
