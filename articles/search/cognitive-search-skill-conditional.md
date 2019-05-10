---
title: 条件付きコグニティブ検索スキル (Azure Search) | Microsoft Docs
description: フィルター処理、既定値の作成、値のマージを可能にする条件付きスキル。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027687"
---
#   <a name="conditional-skill"></a>条件付きスキル

**条件付きスキル**を使用すると、ブール演算を必要とするさまざまなシナリオで、出力に割り当てるデータを決定できます。 これらのシナリオには、条件に基づくフィルター処理、既定値の割り当て、データのマージなどがあります。

次の擬似コードは、条件付きスキルで達成される内容を説明しています。

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> このスキルは Cognitive Services API にバインドされていないため、この使用に対しては課金されません。 ただし、1 日あたりの毎日のエンリッチメントの数を少数に制限する**無料**リソースのオプションをオーバーライドするには、引き続き [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)する必要があります。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>評価済みフィールド

このスキルは、入力が評価済みフィールドなので特別です。

式の有効な値を次に示します。

-   注釈パス (式内のパスは、"$(" と ")" で区切る必要があります) <br/>
    次に例を示します。
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  リテラル (文字列、数値、true、false、null) <br/>
    次に例を示します。
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  比較演算子を使用する式 (==、!=、>=、>、<=、<) <br/>
    次に例を示します。
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   ブール演算子を使用する式 (&&、||、!、^) <br/>
    次に例を示します。
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   数値演算子を使用する式 (+、-、\*、/、%) <br/>
    次に例を示します。 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

サポートされている評価があるため、条件付きスキルは小規模の変換シナリオに使用できます。 例については、サンプルの[スキル定義 4](#transformation-examples) を参照してください。

## <a name="skill-inputs"></a>スキルの入力
入力では大文字と小文字が区別されます。

| 入力      | 説明 |
|-------------|-------------|
| condition   | この入力は、評価する条件を表す[評価済みフィールド](#evaluated-fields)です。 この条件はブール値 (true または false) に評価されます。   <br/>  次に例を示します。 <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | この入力は[評価済みフィールド](#evaluated-fields)です。 条件が true に評価される場合に返される値。 定数の文字列は ' ' の引用符に囲まれて返されます。 <br/>サンプル値: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | この入力は[評価済みフィールド](#evaluated-fields)です。 条件が false に評価される場合に返される値。  <br/>サンプル値: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>スキルの出力
'ourput' という 1 つの出力があります。 条件が false の場合は whenFalse の値が返され、条件が true の場合は whenTrue の値が返されます。

## <a name="examples"></a>例

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>サンプル スキル定義 1:"French" のドキュメントのみを返すドキュメントのフィルター処理

次の出力は、ドキュメントの言語がフランス語の場合、文の配列 ("/document/frenchSentences") を返します。 言語がフランス語ではない場合、その値は null に設定されます。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
"/document/frenchSentences" が別のスキルの "*コンテキスト*" として使用される場合、そのスキルは "/document/frenchSentences" が null に設定されていない場合にのみ実行されます。


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>サンプル スキル定義 2:既定値が存在しない場合に設定する。

次の出力は、言語が設定されていない場合に、ドキュメントの言語 (つまり "es") に設定される注釈 ("/document/languageWithDefault") を作成します。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>サンプル スキル定義 3:2 つの異なるフィールドの値を単一のフィールドにマージする

この例では、一部の文には *frenchSentiment* プロパティがあります。 *frenchSentiment* プロパティが null の場合は常に *englishSentiment* 値を使用するようにします。 単に *sentiment* ("/document/sentiment/*/sentiment") というメンバーに出力を割り当てます。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-examples"></a>変換例
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>サンプル スキル定義 4:単一フィールドでデータ変換を実行する

この例では、0 から 1 の間のセンチメントを受け取り、-1 から 1 の間になるように変換します。 これは、条件付きスキルを使用して実行できる小さな数学変換です。

この特定の例では、条件が常に true なので、スキルの条件付きの側面は使用しません。 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```


## <a name="special-considerations"></a>特別な考慮事項
一部のパラメーターは評価されるので、ドキュメント化されたパターンに従って特に注意する必要がある点に注意してください。 式は等号 "=" で始め、パスは "$(" と ")" で区切る必要があります。 エバリュエーターで文字列と実際のパスおよび演算子とを区別する際に役立つので、文字列は必ず "一重引用符" で囲んでください。 また、演算子の周囲には必ず空白を入れてください (たとえば、パス内の * は乗算演算子とは意味が異なります)。


## <a name="next-steps"></a>次の手順

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
