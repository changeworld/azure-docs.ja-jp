---
title: v2 API によって生成される Azure Video Indexer 出力を調べる | Microsoft Docs
description: このトピックでは、v2 API によって生成される Video Indexer の出力を調べます。
services: cognitive services
documentationcenter: ''
author: juliako
manager: cfowler
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 43cc02417fad8a2fa46bd309235951393cd55b8a
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "40187379"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>v2 API によって生成される Video Indexer の出力を調べる

> [!Note]
> Video Indexer V1 API は 2018 年 8 月 1 日に非推奨になりました。 今後は Video Indexer v2 API を使用してください。 <br/>Video Indexer v2 API を使用して開発する場合は、[こちら](https://api-portal.videoindexer.ai/)の手順を参照してください。 

**Get Video Index** API を呼び出して、応答の状態が OK になると、応答コンテンツとして詳細な JSON 出力が表示されます。 JSON コンテンツには、指定されたビデオの分析情報の詳細が含まれています。 分析情報には、トランスクリプト、ocr、顔、トピック、ブロックなどのディメンションが含まれます。ディメンションには、各ディメンションがいつビデオに表示されたかを示す時間の範囲のインスタンスが含まれます。  

Video Indexer ポータルのビデオの **[再生]** ボタンを押して、ビデオの要約された分析情報を視覚的に確認することもできます。 詳しくは、「[View and edit video insights](video-indexer-view-edit.md)」(ビデオの分析情報の表示と編集) をご覧ください。

![洞察](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

この記事では、**Get Video Index** API によって返される JSON コンテンツを確認します。 

> [!NOTE]
> Video Indexer のすべてのアクセス トークンの有効期限は 1 時間です。


## <a name="root-elements"></a>ルート要素

|Name|説明|
|---|---|
|accountId|プレイリストの VI アカウント ID|
|id|プレイリストの ID|
|name|プレイリストの名前|
|description|プレイリストの説明|
|userName|プレイリストを作成したユーザーの名前|
|created|プレイリストの作成時刻|
|privacyMode|プレイリストのプライバシー モード (秘密/公開)|
|state|プレイリストの状態 (アップロード完了、処理中、失敗、検疫済み)|
|isOwned|プレイリストが現在のユーザーによって作成されたかどうかを示します。|
|isEditable|現在のユーザーにプレイリストを編集する権限があるかどうかを示します。|
|isBase|プレイリストがベースのプレイリスト (ビデオ) か、他のビデオによって構成されたプレイリスト (派生) かどうかを示します。|
|durationInSeconds|プレイリストの合計期間|
|summarizedInsights|1 つの [summarizedInsights](#summarizedinsights) が含まれます。
|videos|プレイリストを構築する[ビデオ](#videos)の一覧<br/>プレイリストが他のビデオの時間範囲で構成されている場合 (派生)、この一覧のビデオには、含まれる時間範囲からのデータのみが含まれます。|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

このセクションには、分析情報の概要が表示されます。

|Attribute | 説明|
|---|---|
|name|ビデオの名前 Azure Monitor など|
|shortId|ビデオの ID 63c6d532ff など|
|privacyMode|内訳には、次のいずれかのモードを含めることができます: **秘密**、**公開**。 **公開** - アカウント内のすべてのユーザーと、ビデオへのリンクを持っているユーザーがビデオを見ることができます。 **秘密** - ビデオは、アカウント内のすべてのユーザーに表示されます。|
|duration|分析情報が発生した時刻を示す 1 つの期間が含まれます。 期間は秒単位です。|
|thumbnailVideoId|サムネイルの取得元のビデオの ID。
|thumbnailId|ビデオのサムネイル ID。 実際のサムネイルを取得するには、Get-Thumbnail を呼び出し (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail)、thumbnailVideoId と thumbnailId に渡します。|
|faces|0 以上の顔を含めることができます。 詳しくは、「[顔](#faces)」をご覧ください。|
|keywords|0 個以上のキーワードを含めることができます。 詳しくは、「[キーワード](#keywords)」をご覧ください。|
|sentiments|0 個以上のセンチメントを含めることができます。 詳しくは、「[センチメント](#sentiments)」をご覧ください。|
|audioEffects| 0 個以上の audioEffects を含めることができます。 詳しくは、「[audioEffects](#audioeffects)」をご覧ください。|
|labels| 0 以上のラベルを含めることができます。 詳細については、「[ラベル](#labels)」をご覧ください。|
|brands| 0 以上のブランドを含めることができます。 詳しくは、「[ブランド](#brands)」をご覧ください。|
|statistics | 詳しくは、「[統計](#statistics)」をご覧ください。|

## <a name="videos"></a>videos

|Name|説明|
|---|---|
|accountId|ビデオの VI アカウント ID|
|id|ビデオの ID|
|name|ビデオの名前
|state|ビデオの状態 (アップロード完了、処理中、処理済み、失敗、検疫済み)|
|processingProgress|処理中の進行状況 (20% など)|
|failureCode|処理に失敗した場合の失敗コード ('UnsupportedFileType' など)|
|failureMessage|処理に失敗した場合のエラー メッセージ|
|externalId|ビデオの外部 ID (ユーザーが指定した場合)|
|externalUrl|ビデオの外部 URL (ユーザーが指定した場合)|
|metadata|ビデオの外部メタデータ (ユーザーが指定した場合)|
|isAdult|ビデオが手動でレビューされ、成人向けビデオとして識別されたかどうかを示します。|
|insights|分析情報オブジェクト 詳細については、「[分析情報](#insights)」を参照してください。|
|thumbnailId|ビデオのサムネイル ID。 実際のサムネイルを取得するには、Get-Thumbnail を呼び出し (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail)、ビデオ ID と thumbnailId を渡します。|
|publishedUrl|ビデオをストリーミングする URL|
|publishedUrlProxy|ビデオのストリーミング元の URL (Apple デバイス向け)|
|viewToken|ビデオ ストリーミング用の短命表示トークン|
|sourceLanguage|ビデオのソース言語|
|language|ビデオの実際の言語 (翻訳)|
|indexingPreset|ビデオのインデックス付けに使用するプリセット|
|streamingPreset|ビデオの発行に使用するプリセット|
|linguisticModelId|ビデオの書き起こしに使用する CRIS モデル|
|statistics | 詳細については、「[統計](#statistics)」を参照してください。|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

分析情報は一連のディメンションです (トラン スクリプト行、顔、ブランドなど)。ここでは、各ディメンションは一意の要素 (face1、face2、face3 など) の一覧で、各要素は独自のメタデータと、そのインスタンスの一覧を持ちます (追加のオプションのメタデータを持つ時間範囲)。

顔は ID、名前、サムネイル、その他のメタデータ、およびその一時的なインスタンスの一覧を持ちます (00:00:05 – 00:00:10、00:01:00 - 00:02:30、00:41:21 – 00:41:49 など)。各テンポラル インスタンスは、追加のメタデータを持つことができます。 顔の四角形の座標などです (20,230,60,60)。

|Version|コードのバージョン|
|---|---|
|sourceLanguage|ビデオのソース言語 (マスター言語は 1 つと仮定)。 [BCP 47](https://tools.ietf.org/html/bcp47) 文字列の形式です。|
|language|(ソース言語から翻訳された) 分析情報言語です。 [BCP 47](https://tools.ietf.org/html/bcp47) 文字列の形式です。|
|transcript|[トランスクリプト](#transcript) ディメンション|
|ocr|[ocr](#ocr) ディメンション|
|keywords|[キーワード](#keywords) ディメンション|
|blocks|1 つ以上の[ブロック](#blocks)を含めることができます。|
|faces|[顔](#faces)ディメンション|
|labels|[ラベル](#labels) ディメンション|
|shots|[ショット](#shots) ディメンション|
|brands|[ブランド](#brands) ディメンション|
|audioEffects|[audioEffects](#audioEffects) ディメンション|
|sentiments|[センチメント](#sentiments) ディメンション|
|visualContentModeration|[visualContentModeration](#visualcontentmoderation) ディメンション|
|textualConentModeration|[textualConentModeration](#textualconentmoderation) ディメンション|

例:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualConentModeration": ...
}
```

#### <a name="blocks"></a>blocks

Attribute | 説明
---|---
id|ブロックの ID|
instances|このブロックの時間範囲の一覧|

#### <a name="transcript"></a>transcript

|Name|説明|
|---|---|
|id|行 ID。|
|text|トランスクリプトそのもの。|
|language|トランスクリプトの言語。 各行の言語が異なる可能性があるトランスクリプトをサポートすることを目的としています。|
|instances|この行が出現する時間範囲の一覧。 インスタンスがトランスクリプトの場合、instance は 1 つだけあります。|

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

#### <a name="ocr"></a>ocr

|Name|説明|
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

#### <a name="keywords"></a>keywords

|Name|説明|
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

#### <a name="faces"></a>faces

|Name|説明|
|---|---|
|id|顔 ID。|
|name|顔の名前。 "Unknown #0"、識別された著名人、または顧客のトレーニング担当者になることができます。|
|confidence|顔認識の信頼度。|
|description|著名人の説明 |
|thumbnalId|その顔のサムネイルの ID|
|knownPersonId|既知の人物の場合は、その内部 ID|
|referenceId|Bing に登録されている著名人の場合は、その Bing ID|
|referenceType|現時点では Bing のみ。|
|title|著名人の場合は、その肩書 (例: "Microsoft の CEO")|
|imageUrl|著名人の場合は、その 画像 の URL|
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

#### <a name="labels"></a>labels

|Name|説明|
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

#### <a name="shots"></a>shots

|Name|説明|
|---|---|
|id|スナップショット ID。|
|keyFrames|ショット内のキー フレームの一覧 (各キー フレームに ID とインスタンスの時間範囲の一覧があります)。 キー フレームのインスタンスには、keyFrame のサムネイル ID 付きサムネイル フィードがあります。|
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
          "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
       "thumbnailId": "00000000-0000-0000-0000-000000000000",   
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
          "thumbnailId": "00000000-0000-0000-0000-000000000000",        
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

#### <a name="brands"></a>brands

音声からテキスト トランスクリプトまたはビデオ OCR への変換で検出されたビジネスおよび製品ブランド名 これには、ブランドまたはロゴ検出の画像認識は含まれません。

|Name|説明|
|---|---|
|id|ブランド ID|
|name|ブランド名|
|referenceId | ブランド Wikipedia の URL のサフィックス たとえば、"Target_Corporation" は [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) のサフィックスです。
|referenceUrl | ブランドの Wikipedia の URL (ある場合) たとえば、[https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) です。
|description|ブランドの説明|
|tags|このブランドに関連付けられていた定義済みタグの一覧|
|confidence|Video Indexer ブランド検出機能の信頼度の値 (0-1)|
|instances|このブランドの時間範囲の一覧。 各インスタンスは、このブランドがトランスクリプトまたは OCR に表示されたかどうかを示す brandType を持ちます。|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistics

|Name|説明|
|---|---|
|CorrespondenceCount|ビデオ内の通知の数|
|WordCount|話者あたり単語の数|
|SpeakerNumberOfFragments|ビデオでの話者のフラグメントの数|
|SpeakerLongestMonolog|話者の最も長いモノローグ。 モノローグでの話者の沈黙がある場合、それも含まれます。 モノローグの先頭と末尾の無音は削除されます。| 
|SpeakerTalkToListenRatio|計算は、ビデオの合計時間で割られた話者のモノローグに費やされた時間に基づきます (間の無音は含みません)。 時間は、小数点第 3 位に丸められます。|

#### <a name="audioeffects"></a>audioEffects

|Name|説明|
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

#### <a name="sentiments"></a>sentiments

センチメントは、sentimentType フィールド (肯定/中立/否定) によって集計されます。 例: 0-0.1、0.1-0.2。

|Name|説明|
|---|---|
|id|センチメント ID。|
|averageScore |センチメントの種類 (肯定/中立/否定) が同じすべてのインスタンスのすべてのスコアの平均値。|
|instances|このセンチメントが出現する時間範囲の一覧。|
|sentimentType |種類として、'Positive'、'Neutral'、'Negative' があります。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration ブロックには、Video Indexer で成人向けコンテンツが含まれる可能性があると判断された時間範囲が含まれます。 visualContentModeration が空の場合、特定された成人向けコンテンツはありません。

成人向けまたはわいせつなコンテンツを含むことが検出されたビデオでは、秘密ビューしか利用できない場合があります。 ユーザーは、コンテンツの人間によるレビューの要求を送信できます。この場合、IsAdult 属性に、人間によるレビューの結果が含まれます。

|Name|説明|
|---|---|
|id|ビジュアル コンテンツ モデレーションの ID|
|adultScore|(コンテンツ モデレーターからの) 成人スコア|
|racyScore|(コンテンツ モデレーションからの) わいせつスコア|
|instances|このビジュアル コンテンツ モデレーションが発生した時間範囲の一覧|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualconentmoderation"></a>textualConentModeration 

|Name|説明|
|---|---|
|id|テキスト コンテンツ モデレーションの ID|
|bannedWordsCount |禁止された単語の数|
|bannedWordsRatio |単語の合計数の比率|


## <a name="next-steps"></a>次の手順

[Video Indexer API](https://api-portal.videoindexer.ai)

アプリケーションにウィジェットを埋め込む方法については、「[Embed Video Indexer widgets into your applications](video-indexer-embed-widgets.md)」(アプリケーションに Video Indexer ウィジェットを埋め込む) を参照してください。 

