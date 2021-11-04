---
title: 言語検出を行う方法
titleSuffix: Azure Cognitive Services
description: この記事では、言語検出を使って、書き表されたテキストの言語を検出する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 55584c58ebba3faf3d9517cf65827fd784d331a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091815"
---
# <a name="how-to-use-language-detection"></a>言語検出を使用する方法

言語検出機能を使うと、テキストを評価し、ドキュメントが書かれた言語を示す言語識別子を取得できます。

言語検出は、言語が不明な任意のテキストを収集するコンテンツ ストアに役立ちます。 この分析の結果を解析して、入力ドキュメントでいずれの言語が使用されるかを特定できます。 応答では、モデルの信頼度を反映する 0 から 1 の範囲のスコアも返されます。

言語検出機能では、さまざまな言語、変異形、方言のほか、一部の地方言語や文化言語も検出できます。

> [!TIP]
> [クイックスタートの記事](../quickstart.md)に従って、この機能を使い始めることができます。 また、[Language Studio](../../language-studio.md) を使うと、コードを記述しなくても要求の例を作成できます。

## <a name="determine-how-to-process-the-data-optional"></a>データの処理方法を決定する (省略可能)

### <a name="specify-the-language-detection-model"></a>言語検出モデルを指定する

既定では、使用できる最新の AI モデルがテキストで使われます。 モデルの特定のバージョンを使うように API 要求を構成することもできます。 指定したモデルを使って、言語検出操作が行われます。

| サポートされているバージョン | 最新バージョン |
|--|--|
|  `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |

### <a name="input-languages"></a>入力言語

評価対象のドキュメントを送信すると、[サポートされている言語](../language-support.md)のいずれかでテキストが書かれたかどうかの判定が、言語検出によって試みられます。  

使用頻度の低い言語で表されるコンテンツがある場合、言語検出機能を試して、コードが返されるかどうか確認できます。 検出できない言語の応答は `unknown` です。

## <a name="submitting-data"></a>データの送信

> [!TIP]
> 言語検出には [Docker コンテナー](use-containers.md)を使えるため、オンプレミスで API を使うことができます。

要求が受信されると分析が実行されます。 1 分間および 1 秒間に送信できる要求のサイズと数については、以下のデータ制限をご覧ください。

言語検出機能の同期的な使用はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。

この機能を非同期的に使うと、API の結果は、応答で示される要求取り込み時刻から 24 時間利用できます。 この時間が経過すると、結果は消去され、取得できなくなります。


## <a name="getting-language-detection-results"></a>言語検出の結果の取得

言語検出から結果を取得するときは、結果をアプリケーションにストリーミングしたり、ローカル システム上のファイルに出力を保存したりできます。

言語検出からは、送信したドキュメントごとに 1 つの主要な言語と、その [ISO 639-1](https://www.iso.org/standard/22109.html) 名、人間が判読できる名前、信頼度スコアが返されます。 正のスコア 1 は、分析で可能な最も高い信頼度レベルを示します。

### <a name="ambiguous-content"></a>あいまいなコンテンツ

場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `countryHint` パラメーターを使用して、[ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) 国および地域コードを指定できます。 API では、国の既定のヒントとして "US" が使われます。 この動作を削除するには、この値を空の文字列(`countryHint = ""`) に設定して、このパラメーターをリセットします。

たとえば、英語とフランス語の両方に共通の "Impossible" が、限られたコンテキストと共に指定されている場合、応答は "US" の国/地域ヒントに基づきます。 テキストがフランスに由来することがわかっている場合は、それをヒントとして指定することができます。

**入力**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "impossible"
        },
        {
            "id": "2",
            "text": "impossible",
            "countryHint": "fr"
        }
    ]
}
```

言語検出モデルには、より適切な判断を行うためのコンテキストが追加されました。 

**出力**

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

アナライザーで入力を解析できない場合は、`(Unknown)` が返されます。 たとえば、数字だけで構成されるテキスト文字列を送信するような場合です。

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": 0.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

### <a name="mixed-language-content"></a>混合言語コンテンツ

同じドキュメント内に混合言語のコンテンツが含まれている場合は、コンテンツの最大の表現で言語が返されますが、評価の肯定度は低くなります。 評価には、評価の限界強度が反映されます。 次の例では、入力は英語、スペイン語、フランス語の混在です。 アナライザーは各セグメント内の文字をカウントして、主要な言語を特定します。

**入力**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
    ]
}
```

**出力**

結果の出力は主要な言語で構成され、スコアは 1.0 未満となります。この数値が小さいほど、信頼度レベルは低くなります。

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 0.88
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

## <a name="data-limits"></a>データ制限

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ (同期) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測された 5,120 文字。 |
| 要求ごとの最大文字数 (非同期)  | 送信ドキュメント全体で 125,000 文字。[StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測されます。 |
| 要求全体の最大サイズ | 1 MB。  |
| 要求あたりのドキュメントの最大数 | 1000 |

ドキュメントが文字数制限を超えている場合、使用しているエンドポイントに応じて、API の動作は異なります。

* 非同期: 要求内のいずれかのドキュメントが最大サイズを超えている場合、API によってその全体が拒否されて、`400 bad request` エラーが返されます。
* 同期: 最大サイズを超えるドキュメントは API によって処理されず、無効ドキュメント エラーが返されます。 API 要求に複数のドキュメントが含まれている場合、API は、それらが文字数制限内であれば処理を続行します。

### <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://aka.ms/unifiedLanguagePricing)によって異なります。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="see-also"></a>関連項目

* [言語検出の概要](../overview.md)
