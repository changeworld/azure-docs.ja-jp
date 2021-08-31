---
title: PII 検出コグニティブ スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のエンリッチメント パイプラインのテキストから個人情報を抽出してマスクします。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 71bd45fae729efed6d76ab65fc4ea45944998f45
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862695"
---
# <a name="pii-detection-cognitive-skill"></a>PII 検出コグニティブ スキル

**PII 検出** スキルは、入力テキストから個人情報を抽出し、それをマスクするオプションを提供します。 このスキルでは、Cognitive Services の [Text Analytics](../cognitive-services/text-analytics/overview.md) によって提供される機械学習モデルが使用されます。

> [!NOTE]
> このスキルは Cognitive Services にバインドされており、1 日にインデクサーあたり 20 ドキュメントを超えるトランザクションには[課金対象リソース](cognitive-search-attach-cognitive-services.md)が必要です。 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>データ制限

レコードのサイズは、[`String.Length`](/dotnet/api/system.string.length) で測定して 50,000 文字以下にする必要があります。 データをスキルに送信する前にチャンクに分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターでは大文字と小文字が区別され、すべて任意です。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `defaultLanguageCode` | (省略可能) 言語を明示的に指定しないドキュメントに適用する言語コード。  既定の言語コードが指定されていない場合、既定の言語コードとして英語 (en) が使用されます。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md?tabs=pii)を参照してください。 |
| `minimumPrecision` | 0\.0 から 1.0 の値。 (`piiEntities` 出力の) 信頼度スコアが `minimumPrecision` の設定値よりも小さい場合は、エンティティは返されず、マスクもされません。 既定では、0.0 です。 |
| `maskingMode` | 入力テキスト内で検出された個人情報をマスクするためのさまざまな方法を提供するパラメーター。 次のオプションがサポートされています。 <ul><li>`none` (既定値): マスクは行われず、`maskedText` の出力は返されません。 </li><li> `replace`:検出されたエンティティを `maskingCharacter` パラメーターで指定された文字に置き換えます。 文字は検出されたエンティティの長さに繰り返されます。これにより、オフセットが入力テキストと出力 `maskedText` の両方に正しく対応するようになります。</li></ul> <br/> PIIDetectionSkill がプレビューの間は、`maskingMode` のオプション `redact` もサポートされており、これにより置換なしで検出されたエンティティを完全に削除できました。 `redact` オプションは非推奨になり、今後のスキルではサポートされなくなります。 |
| `maskingCharacter` | `maskingMode` パラメーターが `replace` に設定されている場合に、テキストをマスクするために使用される文字。 次のオプションがサポートされています: `*` (既定値)。 このパラメーターは、`maskingMode` が `replace` に設定されていない場合にのみ `null` できます。 <br/><br/> PIIDetectionSkill がプレビューの間は、`maskingCharacter` の追加のオプション `X` と `#` がサポートされていました。 `X` および `#` オプションはその後で非推奨になっており、今後のスキルではサポートされなくなります。 |
| `domain`   | (省略可能) 文字列値を指定すると、エンティティ カテゴリのサブセットのみが含まれるように PII ドメインが設定されます。 使用可能な値は、`phi` (機密性の高い医療情報のみを検出)、`none` です。 |
| `piiCategories`   | (省略可能) 検出されて返されるエンティティを指定する場合は、オプションの `piiCategories` パラメーター (文字列のリストとして定義) を適切なエンティティ カテゴリと共に使用します。 このパラメーターを使用すると、ドキュメントの言語に対して既定で有効になっていないエンティティを検出することもできます。 使用できるカテゴリの一覧については、[TextAnalytics のドキュメント](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal)を参照してください。  |
| `modelVersion`   | (省略可能) Text Analytics サービスを呼び出すときに使用するモデルのバージョン。 指定しない場合、既定では直近のバージョンになります。 絶対に必要な場合以外は、この値を指定しないことをお勧めします。 詳細については、「[Text Analytics API でのモデルのバージョン管理](../cognitive-services/text-analytics/concepts/model-versioning.md)」を参照してください。 |

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                   |
|---------------|-------------------------------|
| `languageCode`    | レコードの言語を示す文字列。 このパラメーターが指定されていない場合、既定の言語コードがレコードを分析するために使用されます。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md?tabs=pii)を参照  |
| `text`          | 分析するテキスト。          |

## <a name="skill-outputs"></a>スキルの出力

| 出力名      | 説明                   |
|---------------|-------------------------------|
| `piiEntities` | 次のフィールドが含まれる複合型の配列。 <ul><li>テキスト (抽出された実際の PII)</li> <li>type</li><li>subType</li><li>スコア (値が高いほど、実際のエンティティに近づく可能性が高くなります)</li><li>オフセット (入力テキスト内)</li><li>length</li></ul> </br> [使用できる型と subTypes については、こちらを参照してください。](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | `maskingMode` が `none` 以外の値に設定されている場合、この出力は、選択した `maskingMode` で説明されている入力テキストに対して実行されるマスクの結果の文字列になります。  `maskingMode` が `none` に設定されている場合、この出力は表示されません。 |

## <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```

## <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

このスキルの出力のエンティティに対して返されるオフセットは、[Text Analytics API](../cognitive-services/text-analytics/overview.md) から直接返されます。つまり、これらを使用して元の文字列にインデックスを作成する場合は、正しい内容を抽出するために .NET の [StringInfo](/dotnet/api/system.globalization.stringinfo) クラスを使用する必要があります。  [詳細については、こちらで確認できます。](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>エラーと警告

ドキュメントの言語コードがサポートされていない場合、警告が返され、エンティティは抽出されません。
テキストが空の場合、警告が返されます。
テキストが 50,000 文字を超えると、最初の 50,000 文字のみが分析され、警告が発行されます。

スキルから警告が返された場合、出力 `maskedText` が空である可能性があります。これは、その出力を予期している下流のスキルに影響を与える可能性があります。 このため、スキルセットの定義を記述するときは必ず、出力が欠落していることに関連するすべての警告を調べてください。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)