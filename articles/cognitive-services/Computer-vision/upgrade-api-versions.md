---
title: Computer Vision API の Read v3.0 へのアップグレード
titleSuffix: Azure Cognitive Services
description: Computer Vision Read API を v2.0 または v2.1 から v3.0 にアップグレードする方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 7a05b04872b4f957e879d93972edc45e2932d059
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364092"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Read v2.x から Read v3.x へのアップグレード

このガイドでは、既存のコンテナーまたはクラウド API コードを、Read v2.x から Read v3.x にアップグレードする方法について説明します。

## <a name="determine-your-api-path"></a>API パスの決定
次の表を使用し、移行先の Read 3.x のバージョンに基づいて、API パス内の **バージョン文字列** を確認します。

|製品の種類| Version | 3\.x API パスのバージョン文字列 |
|:-----|:----|:----|
|サービス | Read 3.0 または 3.1 | それぞれ **v3.0** または **v3.1** |
|サービス | Read 3.2 プレビュー | **v3.2-preview.1** |
|コンテナー | Read 3.0 プレビューまたは Read 3.1 プレビュー | それぞれ **v3.0** または **v3.1-preview.2** |


次に、次のセクションを使用して操作を絞り込み、API パス内の **バージョン文字列** を表の値に置き換えます。 たとえば、**Read v3.2 プレビュー** のクラウドおよびコンテナー バージョンの場合は、API パスを **https://{endpoint}/vision/v3.2-preview.1/read/analyze[?language]** に更新します。

## <a name="servicecontainer"></a>サービス/コンテナー

### `Batch Read File`

|Read 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**バージョン文字列**>/read/analyze[?language]|
    
新しい省略可能な _language_ パラメーターを使用できます。 ドキュメントの言語がわからない場合 (多言語である可能性がある場合) は、これを含めないでください。 

### `Get Read Results`

|Read 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**バージョン文字列**>/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` ステータス フラグ

`Get Read Operation Result` の呼び出しが成功すると、JSON 本文の状態文字列フィールドが返されます。
 
|Read 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API 応答 (JSON) 

JSON の次の変更点に注意してください。
* v2.x では、状態が `Succeeded"` のときに `Get Read Operation Result` によって OCR 認識 JSON が返されます。 v3.0 では、このフィールドは `succeeded` です。
* ページ配列のルートを取得するには、JSON 階層を `recognitionResults` から `analyzeResult`/`readResults` に変更します。 ページごとの行と単語の JSON 階層は変更されていないため、コードを変更する必要はありません。
* ページ角度の `clockwiseOrientation` は、`angle` に名前が変更されました。範囲は、0 から 360 度から、-180 から 180 度に変更されました。 ほとんどの数学関数ではいずれの範囲も処理できるので、コードに応じて、変更を加える必要がある場合とない場合があります。

v3.0 API では、必要に応じて利用できる次の機能強化も導入されています。
* 処理の期間を追跡できるように、`createdDateTime` と `lastUpdatedDateTime` が追加されました。 詳細については、ドキュメントを参照してください。 
* `version` は、結果の生成に使用された API のバージョンを示します。
* 単語ごとの `confidence` が追加されました。 この値は、値 0.95 が、95% の確率で認識が正しいことを意味するように調整されています。 信頼度スコアを使用すると、人によるレビューに送信するテキストを選択できます。 
    
    
2\.X の出力形式は次のとおりです。 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
v3.0 では、次のように調整されています。
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>サービスのみ

### `Recognize Text`
`Recognize Text` は "*プレビュー*" 操作であり、*Computer Vision API のすべてのバージョンで非推奨* になっています。 `Recognize Text` から `Read` (v3.0) または `Batch Read File` (v2.0、v2.1) に移行する必要があります。 v3.0 の `Read` には、テキスト認識に適した新しいモデルと追加機能が含まれているため、これが推奨されます。 `Recognize Text` から `Read` にアップグレードするには、次の手順を実行します。

|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/<**バージョン文字列**>/read/analyze[?language]|
    
_mode_ パラメーターは、`Read` ではサポートされていません。 手書きのテキストと印刷されたテキストの両方が自動的にサポートされます。
    
v3.0 では、新しい省略可能な _language_ パラメーターを使用できます。 ドキュメントの言語がわからない場合 (多言語である可能性がある場合) は、これを含めないでください。 

### `Get Recognize Text Operation Result`

|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/<**バージョン文字列**>/read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` ステータス フラグ
`Get Recognize Text Operation Result` の呼び出しが成功すると、JSON 本文の状態文字列フィールドが返されます。 
 
|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API 応答 (JSON)

JSON の次の変更点に注意してください。    
* v2.x では、状態が `Succeeded` のときに `Get Read Operation Result` によって OCR 認識 JSON が返されます。 v3.x では、このフィールドは `succeeded` です。
* ページ配列のルートを取得するには、JSON 階層を `recognitionResult` から `analyzeResult`/`readResults` に変更します。 ページごとの行と単語の JSON 階層は変更されていないため、コードを変更する必要はありません。

v3.0 API では、必要に応じて利用できる次の機能強化も導入されています。 詳細については、API リファレンスを参照してください。
* 処理の期間を追跡できるように、`createdDateTime` と `lastUpdatedDateTime` が追加されました。 詳細については、ドキュメントを参照してください。 
* `version` は、結果の生成に使用された API のバージョンを示します。
* 単語ごとの `confidence` が追加されました。 この値は、値 0.95 が、95% の確率で認識が正しいことを意味するように調整されています。 信頼度スコアを使用すると、人によるレビューに送信するテキストを選択できます。 
* `angle` は、-180 から 180 度の角度で測定された、テキストの時計回りの一般的な向きです。
* `width` と `"height"` により、ドキュメントのサイズが示されます。また、`"unit"` では、それらのサイズの単位 (ドキュメントの種類に応じてピクセルまたはインチ) が示されます。
* `page` では、複数ページのドキュメントがサポートされています。
* `language` は、ドキュメントの入力言語です (省略可能な _language_ パラメーターで指定)。


2\.X の出力形式は次のとおりです。 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
v3.x では、次のように調整されています。
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>コンテナーのみ

### `Synchronous Read`

|Read 2.0 |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<**バージョン文字列**>/read/syncAnalyze[?language]|
