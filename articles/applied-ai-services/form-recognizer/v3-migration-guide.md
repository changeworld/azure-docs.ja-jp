---
title: '方法: アプリケーションを Form Recognizer v2.1 から v3.0 に移行する'
titleSuffix: Azure Applied AI Services
description: この攻略ガイドでは、Form Recognizer API v2.1 と v3.0 の違いについて説明します。 また、新しいバージョンの API に移行するために必要な変更についても説明します。
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: vikurpad
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: fd0c9c1ebaf177f6f10698631b96e2c27825603f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020827"
---
# <a name="form-recognizer-v30-migration--preview"></a>Form Recognizer v3.0 への移行 | プレビュー

> [!IMPORTANT]
>
> Form Recognizer REST API v3.0 では、REST API の要求と分析の応答 JSON で破壊的変更が行われています。

Form Recognizer v3.0 (プレビュー) では、いくつかの新機能が導入されています。

* [Form Recognizer REST API](quickstarts/try-v3-rest-api.md) は、いっそう使いやすいように再設計されました。
* [**一般ドキュメント (v3.0)**](concept-general-document.md) モデルは新しい API であり、テキスト、テーブル、構造、キーと値のペア、名前付きエンティティがフォームとドキュメントから抽出されます。
* [**領収書 (v3.0)**](concept-receipt.md) モデルでは、単一ページのホテルの領収書の処理がサポートされます。
* [**身分証明書 (v3.0)**](concept-id-document.md) モデルでは、米国の運転免許証からの署名、制限、車両分類の抽出がサポートされています。
* [**カスタム モデル API (v3.0)**](concept-custom.md) では、カスタム フォームの署名の検出がサポートされています。

この記事では、Form Recognizer v2.1 と v3.0 の違いと、新しいバージョンの API に移行する方法について説明します。

## <a name="changes-to-the-rest-api-endpoints"></a>REST API エンドポイントの変更

 v3.0 の REST API では、レイアウト分析 (事前構築済みレイアウト) と事前構築済みモデルに **`documentModels`** と **`modelId`** を割り当てることにより、レイアウト分析、事前構築済みモデル、カスタム モデルの分析操作が、操作の単一のペアに結合されています。

### <a name="post-request"></a>POST 要求

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview

```

### <a name="get-request"></a>GET 要求

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}/AnalyzeResult/{resultId}?api-version=2021-07-30-preview
```

### <a name="analyze-operation"></a>分析操作

* 要求ペイロードと呼び出しパターンは変更されていません。
* 分析操作では、入力ドキュメントとコンテンツ固有の構成が指定され、応答の Operation-Location ヘッダーを介して分析結果 URL が返されます。
* GET 要求を使用してこの分析結果 URL をポーリングし、分析操作の状態を調べます (要求の推奨最小間隔は 1 秒です)。
* 成功すると、状態は succeeded に設定され、[analyzeResult](#changes-to-analyze-result) が応答本文で返されます。 エラーが発生した場合は、状態は failed に設定され、エラーが返されます。

| モデル | v2.1 | v3.0 |
|:--| :--| :--|
| **要求 URL のプレフィックス**| **https://<Form Recognizer のエンドポイント>/formrecognizer/v2.1**  | **https://<Form Recognizer のエンドポイント>/formrecognizer** |
|🆕 **一般ドキュメント**|該当なし|/documentModels/prebuilt-document:analyze |
| **レイアウト**| /layout/analyze |/documentModels/prebuilt-layout:analyze|
|**カスタム**| /custom/<モデル ID>/analyze    |/documentModels/<モデル ID>:analyze |
| **請求書** | /prebuilt/invoice/analyze    | /documentModels/prebuilt-invoice:analyze |
| **Receipt** | /prebuilt/receipt/analyze    | /documentModels/prebuilt-receipt:analyze |
| **身分証明書** | /prebuilt/idDocument/analyze |  /documentModels/prebuilt-idDocument:analyze |
|**名刺**| /prebuilt/businessCard/analyze| /documentModels/prebuilt-businessCard:analyze|

### <a name="analyze-request-body"></a>分析要求の本文

分析対象のコンテンツは、要求本文で提供されます。 要求を作成するには、URL または base64 でエンコードされたデータを使用できます。

  パブリックにアクセスできる Web URL を指定するには、Content-Type を  **application/json** に設定して、次の JSON 本文を送信します。

  ```json
  {
    "urlSource": "{urlPath}"
  }
  ```

base64 エンコードは、Form Recognizer v3.0 でもサポートされています。

```json
{
  "base64Source": "{base64EncodedContent}"
}
```

### <a name="additional-parameters"></a>追加パラメーター

引き続きサポートされるパラメーター:

* ページ
* locale

サポートされなくなったパラメーター: 

* includeTextDetails

新しい応答形式はいっそうコンパクトで、常に完全な出力が返されます。

## <a name="changes-to-analyze-result"></a>結果の分析に関する変更

分析応答は、複数ページの要素をサポートするため、次の最上位レベルの結果にリファクタリングされています。

* ページ
* テーブル
* keyValuePairs
* entities
* スタイル
* ドキュメント

> [!NOTE]
>
> analyzeResult 応答の変更には、ページのプロパティから analyzeResult 内の最上位レベルのプロパティへの移動など、多くの変更が含まれます。

```json

{
// Basic analyze result metadata
"apiVersion": "2021-07-30-preview", // REST API version used
"modelId": "prebuilt-invoice", // ModelId used
"stringIndexType": "textElements", // Character unit used for string offsets and lengths:
// textElements, unicodeCodePoint, utf16CodeUnit // Concatenated content in global reading order across pages.
// Words are generally delimited by space, except CJK (Chinese, Japanese, Korean) characters.
// Lines and selection marks are generally delimited by newline character.
// Selection marks are represented in Markdown emoji syntax (:selected:, :unselected:).
"content": "CONTOSO LTD.\nINVOICE\nContoso Headquarters...", "pages": [ // List of pages analyzed
{
// Basic page metadata
"pageNumber": 1, // 1-indexed page number
"angle": 0, // Orientation of content in clockwise direction (degree)
"width": 0, // Page width
"height": 0, // Page height
"unit": "pixel", // Unit for width, height, and bounding box coordinates
"spans": [ // Parts of top-level content covered by page
{
"offset": 0, // Offset in content
"length": 7 // Length in content
}
], // List of words in page
"words": [
{
"text": "CONTOSO", // Equivalent to $.content.Substring(span.offset, span.length)
"boundingBox": [ ... ], // Position in page
"confidence": 0.99, // Extraction confidence
"span": { ... } // Part of top-level content covered by word
}, ...
], // List of selectionMarks in page
"selectionMarks": [
{
"state": "selected", // Selection state: selected, unselected
"boundingBox": [ ... ], // Position in page
"confidence": 0.95, // Extraction confidence
"span": { ... } // Part of top-level content covered by selection mark
}, ...
], // List of lines in page
"lines": [
{
"content": "CONTOSO LTD.", // Concatenated content of line (may contain both words and selectionMarks)
"boundingBox": [ ... ], // Position in page
"spans": [ ... ], // Parts of top-level content covered by line
}, ...
]
}, ...
], // List of extracted tables
"tables": [
{
"rowCount": 1, // Number of rows in table
"columnCount": 1, // Number of columns in table
"boundingRegions": [ // Bounding boxes potentially across pages covered by table
{
"pageNumber": 1, // 1-indexed page number
"boundingBox": [ ... ], // Bounding box
}
],
"spans": [ ... ], // Parts of top-level content covered by table // List of cells in table
"cells": [
{
"kind": "stub", // Cell kind: content (default), rowHeader, columnHeader, stub, description
"rowIndex": 0, // 0-indexed row position of cell
"columnIndex": 0, // 0-indexed column position of cell
"rowSpan": 1, // Number of rows spanned by cell (default=1)
"columnSpan": 1, // Number of columns spanned by cell (default=1)
"content": "SALESPERSON", // Concatenated content of cell
"boundingRegions": [ ... ], // Bounding regions covered by cell
"spans": [ ... ] // Parts of top-level content covered by cell
}, ...
]
}, ...
], // List of extracted key-value pairs
"keyValuePairs": [
{
"key": { // Extracted key
"content": "INVOICE:", // Key content
"boundingRegions": [ ... ], // Key bounding regions
"spans": [ ... ] // Key spans
},
"value": { // Extracted value corresponding to key, if any
"content": "INV-100", // Value content
"boundingRegions": [ ... ], // Value bounding regions
"spans": [ ... ] // Value spans
},
"confidence": 0.95 // Extraction confidence
}, ...
], // List of extracted entities
"entities": [
{
"category": "DateTime", // Primary entity category
"subCategory": "Date", // Secondary entity category
"content": "11/15/2019", // Entity content
"boundingRegions": [ ... ], // Entity bounding regions
"spans": [ ... ], // Entity spans
"confidence": 0.99 // Extraction confidence
}, ...
], // List of extracted styles
"styles": [
{
"isHandwritten": true, // Is content in this style handwritten?
"spans": [ ... ], // Spans covered by this style
"confidence": 0.95 // Detection confidence
}, ...
], // List of extracted documents
"documents": [
{
"docType": "prebuilt-invoice", // Classified document type (model dependent)
"boundingRegions": [ ... ], // Document bounding regions
"spans": [ ... ], // Document spans
"confidence": 0.99, // Document splitting/classification confidence // List of extracted fields
"fields": {
"VendorName": { // Field name (docType dependent)
"type": "string", // Field value type: string, number, array, object, ...
"valueString": "CONTOSO LTD.",// Normalized field value
"content": "CONTOSO LTD.", // Raw extracted field content
"boundingRegions": [ ... ], // Field bounding regions
"spans": [ ... ], // Field spans
"confidence": 0.99 // Extraction confidence
}, ...
}
}, ...
]
}



```

## <a name="build-or-train-model"></a>モデルの構築またはトレーニング

モデル オブジェクトの新しい API には 2 つの更新が含まれています

* ```modelId``` は、モデルで人間が読むことのできる名前に設定できるプロパティです。
* ```modelName``` の名前が ```description``` に変更されました

```build``` 操作は、モデルをトレーニングするために呼び出されます。 要求ペイロードと呼び出しパターンは変更されていません。 構築操作では、モデルとトレーニング データセットを指定し、応答の Operation-Location ヘッダーで結果が返されます。 GET 要求を使用してこのモデル操作 URL をポーリングし、構築操作の状態を調べます (要求の推奨最小間隔は 1 秒です)。 v2.1 とは異なり、この URL はモデルのリソースの場所ではありません。 代わりに、モデルの URL は、特定の modelId から作成でき、応答の resourceLocation プロパティから取得することもできます。 成功すると、状態が ```succeeded``` に設定され、結果にカスタム モデル情報が含まれます。 エラーが発生した場合、状態は ```failed``` に設定され、エラーが返されます。

次のコードは、SAS トークンを使用した構築要求の例です。 プレフィックスまたはフォルダー パスを設定するときの、末尾のスラッシュに注意してください。

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:build?api-version=2021-09-30-preview

{
  "modelId": {modelId},
  "description": "Sample model",
  "azureBlobSource": {
    "containerUrl": "https://{storageAccount}.blob.core.windows.net/{containerName}?{sasToken}",
    "prefix": "{folderName/}"
  }
}
```

## <a name="changes-to-compose-model"></a>モデルの構成に関する変更

モデルの構成は、単一レベルの入れ子に制限されるようになりました。 構成されたモデルはカスタム モデルと一貫性が保たれ、```modelId``` プロパティと ```description``` プロパティが追加されています。

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:compose?api-version=2021-09-30-preview
{
  "modelId": "{composedModelId}",
  "description": "{composedModelDescription}",
  "componentModels": [
    { "modelId": "{modelId1}" },
    { "modelId": "{modelId2}" },
  ]
}

```

## <a name="changes-to-copy-model"></a>モデルのコピーに関する変更

コピー モデルの呼び出しパターンは変更されていません。

* ```authorizeCopy``` を呼び出してターゲット リソースでコピー操作を承認します。 現在は POST 要求。
* ```copy-to``` を呼び出し、ソース リソースに承認を送信してモデルをコピーします
* 返された操作をポーリングして、操作が正常に完了したことを検証します

モデル コピー関数に対する変更は次のものだけです。

* ```authorizeCopy``` に対する HTTP アクションが POST 要求になりました。
* 承認ペイロードには、コピー要求を送信するために必要なすべての情報が含まれています。

***コピーを承認する***

```json
POST https://{targetHost}/formrecognizer/documentModels:authorizeCopy?api-version=2021-09-30-preview
{
  "modelId": "{targetModelId}",
  "description": "{targetModelDescription}",
}
```

承認アクションからの応答本文を使用して、コピーの要求を作成します。

```json
POST https://{sourceHost}/formrecognizer/documentModels/{sourceModelId}:copy-to?api-version=2021-09-30-preview
{
  "targetResourceId": "{targetResourceId}",
  "targetResourceRegion": "{targetResourceRegion}",
  "targetModelId": "{targetModelId}",
  "targetModelLocation": "https://{targetHost}/formrecognizer/documentModels/{targetModelId}",
  "accessToken": "{accessToken}",
  "expirationDateTime": "2021-08-02T03:56:11Z"
}
```

## <a name="changes-to-list-models"></a>モデルの一覧表示に関する変更

モデルの一覧表示が拡張され、事前構築済みモデルとカスタム モデルが返されるようになりました。 事前構築済みモデルの名前はすべて、```prebuilt-``` で始まります。 状態が成功のモデルのみが返されます。 失敗したモデルまたは進行中のモデルを一覧表示するには、[一覧表示操作](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels)に関するページを参照してください。

***モデル一覧表示要求の例***

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels?api-version=2021-09-30-preview
```

## <a name="change-to-get-model"></a>モデルの取得に関する変更

モデルの取得に事前構築済みモデルが含まれるようになったので、取得操作からは ```docTypes``` ディクショナリが返されます。 各ドキュメントの種類は、その名前、省略可能な説明、フィールド スキーマ、および省略可能なフィールド信頼度によって記述されます。 フィールド スキーマでは、そのドキュメントの種類で返される可能性のあるフィールドの一覧が記述されています。

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-09-30-preview
```

## <a name="new-get-info-operation"></a>新しい情報取得操作

サービスに対する ```info``` 操作からは、カスタム モデルの数とカスタム モデルの制限が返されます。

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/info? api-version=2021-09-30-preview
```

***応答のサンプル***

```json
{
  "customDocumentModels": {
    "count": 5,
    "limit": 100
  }
}
```

## <a name="next-steps"></a>次のステップ

この移行ガイドでは、既存の Form Recognizer アプリケーションを、v3.0 API を使用するようにアップグレードする方法について説明しました。 すべての GA 機能については引き続き 2.1 API を使用し、プレビュー機能の場合は 3.0 API を使用してください。

* [新しい REST API を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
* [Form Recognizer とは](overview.md)
* [Form Recognizer のクイックスタート](./quickstarts/try-sdk-rest-api.md)
