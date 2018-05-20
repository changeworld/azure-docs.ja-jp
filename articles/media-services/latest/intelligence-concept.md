---
title: Azure メディア インテリジェンス | Microsoft Docs
description: Azure Media Services を使用すると、AudioAnalyzerPreset と VideoAnalyzerPreset を使用して、音声と画像のコンテンツを分析できます。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: 804a418f6ee88974d6e74a2c18bc5d01b6adf838
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="media-intelligence"></a>メディア インテリジェンス

Azure Media Services REST v3 API を使用して、音声と画像のコンテンツを分析できます。 コンテンツを分析するには、**Transform** を作成し、プリセット (**AudioAnalyzerPreset** または **VideoAnalyzerPreset** のいずれか) を使用する **Job** を送信します。 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** を使用して、音声または画像ファイルから複数の音声分析情報を抽出できます。 出力には、JSON ファイル (すべての分析情報が含まれます) と、音声トランスクリプト用の VTT ファイルが含まれます。 このプリセットには、入力ファイルの言語を [BCP47](https://tools.ietf.org/html/bcp47) 文字列形式で指定するプロパティを指定できます。 音声分析情報には、以下が含まれます。

* 音声の文字起こし – タイムスタンプ付きの発話のトランスクリプト。 複数の言語がサポートされます。
* 話者インデックス – 話者と対応する音声のマッピング。
* 音声のセンチメント分析 – 音声の文字起こしに対して実行されたセンチメント分析の出力。
* キーワード – 音声の文字起こしから抽出されたキーワード。

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** を使用して、ビデオ ファイルから複数の音声と画像の分析情報を抽出できます。 出力には、JSON ファイル (すべての分析情報が含まれます)、画像トランスクリプト用の VTT ファイル、およびサムネールのコレクションが含まれます。 このプリセットには、プロパティとして [BCP47](https://tools.ietf.org/html/bcp47) 文字列 (画像の言語を表します) を指定することもできます。 画像分析情報には、前述のすべての音声分析情報と、次の項目が含まれます。

* 顔追跡 – ビデオに顔が登場している時間。 それぞれの顔には、顔 ID と、対応するサムネイルのコレクションがあります。
* 視覚テキスト – 光学式文字認識を使用して検出されたテキスト。 テキストにはタイムスタンプが付けられ、(音声トランスクリプションに加え) キーワードの抽出でも使用されます。
* キーフレーム – 画像から抽出されたキーフレームのコレクション。
* 視覚コンテンツ モデレーション – 成人向けまたは性的描写としてフラグ付けされている画像の一部。
* 注釈 – 定義済みのオブジェクト モデルに基づいてビデオに注釈を付けた結果。

##  <a name="insightsjson-elements"></a>insights.json 要素

出力には、画像または音声内で検出されたすべての分析情報を持つ JSON ファイル (insights.json) が含まれます。 json には、次の要素が含まれる可能性があります。

### <a name="transcript"></a>transcript

|名前|説明|
|---|---|
|id|行 ID。|
|text|トランスクリプトそのもの。|
|language|トランスクリプトの言語。 各行の言語が異なる可能性があるトランスクリプトをサポートすることを目的としています。|
|instances|この行が出現する時間範囲の一覧。 インスタンスが transcript の場合、instances は 1 つだけあります。|

例:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|名前|説明|
|---|---|
|id|OCR 行 ID。|
|text|OCR テキスト。|
|confidence|認識の信頼度。|
|language|OCR 言語。|
|instances|この OCR が出現する時間範囲の一覧 (同じ OCR が複数回出現する可能性があります)。|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="keywords"></a>keywords

|名前|説明|
|---|---|
|id|キーワード ID。|
|text|キーワードのテキスト。|
|confidence|キーワード認識の信頼度。|
|language|キーワードの言語 (翻訳時)。|
|instances|このキーワードが出現する時間範囲の一覧 (1 つのキーワードが複数回出現する可能性があります)。|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 

```

### <a name="faces"></a>faces

|名前|説明|
|---|---|
|id|顔 ID。|
|name|顔の名前。 "Unknown #0"、識別された著名人、または顧客のトレーニング担当者になることができます。|
|confidence|顔認識の信頼度。|
|description|著名人の場合は、その人物の説明 ("Satya Nadella was born at..")。 |
|thumbnalId|顔のサムネイル ID。|
|knownPersonId|既知の人物の場合は、その内部 ID。|
|referenceId|Bing に登録されている著名人の場合は、その Bing ID。|
|referenceType|現時点では Bing のみ。|
|title|著名人の場合は、その肩書 (例: "Microsoft's CEO")。|
|imageUrl|著名人の場合は、その 画像 の URL。|
|instances|特定の時間範囲の中で顔が出現したインスタンス。 各インスタンスにも、thumbnailsId があります。 |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="labels"></a>labels

|名前|説明|
|---|---|
|id|ラベル ID。|
|name|ラベル名 (例: "Computer"、"TV")。|
|language|ラベル名の言語 (翻訳時)。 BCP-47|
|instances|このラベルが出現する時間範囲の一覧 (1 つのラベルが複数回出現する可能性があります)。 各インスタンスに confidence フィールドがあります。 |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="shots"></a>shots

|名前|説明|
|---|---|
|id|スナップショット ID。|
|keyFrames|ショット内のキー フレームの一覧 (各キー フレームに ID とインスタンスの時間範囲の一覧があります)。|
|instances|このショットの時間範囲の一覧 (ショットのインスタンスは 1 つだけあります)。|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="audioeffects"></a>audioEffects

|名前|説明|
|---|---|
|id|オーディオ エフェクト ID。|
|type|オーディオ エフェクトの種類 (例: 拍手、発話、無音)。|
|instances|このオーディオ エフェクトが出現する時間範囲の一覧。|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```


### <a name="sentiments"></a>sentiments

センチメントは、sentimentType フィールド (肯定/中立/否定) によって集計されます。 例: 0-0.1、0.1-0.2。

|名前|説明|
|---|---|
|id|センチメント ID。|
|averageScore |センチメントの種類 (肯定/中立/否定) が同じすべてのインスタンスのすべてのスコアの平均値。|
|instances|このセンチメントが出現する時間範囲の一覧。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: Azure Media Services を使用してビデオを分析する](analyze-videos-tutorial-with-api.md)
