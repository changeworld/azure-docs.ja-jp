---
title: ドキュメント翻訳のドキュメント形式の取得メソッド
titleSuffix: Azure Cognitive Services
description: ドキュメント形式の取得メソッドでは、サポートされているドキュメント形式の一覧が返されます。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 3cd74f70416d2049a0fb969371507c292dd47fdf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613818"
---
# <a name="document-translation-get-document-formats"></a>ドキュメント翻訳: ドキュメント形式の取得

ドキュメント形式の取得メソッドでは、ドキュメント翻訳サービスでサポートされているドキュメント形式の一覧が返されます。 一覧には、共通ファイル拡張子と、アップロード API を使用している場合のコンテンツの種類が含まれます。

## <a name="request-url"></a>要求 URL

`GET` 要求の送信先は次のとおりです。
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/documents/formats
```

[カスタム ドメイン名](../get-started-with-document-translation.md#find-your-custom-domain-name)を見つける方法について説明します。

> [!IMPORTANT]
>
> * **ドキュメント翻訳サービスへのすべての API 要求には、カスタム ドメイン エンドポイントが必要です**。
> * ドキュメント翻訳への HTTP 要求を行うために、Azure portal リソースの _[キーとエンドポイント]_ ページで見つかるエンドポイントも、グローバル Translator エンドポイント `api.cognitive.microsofttranslator.com` も使用することはできません。

## <a name="request-headers"></a>要求ヘッダー

要求ヘッダーを次に示します。

|ヘッダー|説明|
|-----|-----|
|Ocp-Apim-Subscription-Key|必要な要求ヘッダー|

## <a name="response-status-codes"></a>応答状態コード

要求によって返される可能性のある HTTP 状態コードを次に示します。

|状態コード|説明|
|-----|-----|
|200|OK です。 サポートされているドキュメント ファイル形式の一覧を返します。|
|500|内部サーバー エラー。|
|その他の状態コード|<ul><li>要求が多すぎます</li><li>サーバーが一時的に使用できません</li></ul>|

## <a name="file-format-response"></a>ファイル形式の応答

### <a name="successful-fileformatlistresult-response"></a>成功した fileFormatListResult の応答

成功した応答では、次の情報が返されます。

|名前|Type|説明|
|--- |--- |--- |
|value|FileFormat []|FileFormat [] には、下に示す詳細が含まれます。|
|value.format|string[]|この形式がサポートされているコンテンツの種類。|
|value.fileExtensions|string[]|この形式がサポートされているファイル拡張子。|
|value.contentTypes|string[]|形式の名前。|
|value.versions|String[]|サポートされているバージョン。|

### <a name="error-response"></a>エラー応答

|名前|Type|説明|
|--- |--- |--- |
 |code|string|高レベルのエラー コードを含む列挙型。 指定できる値<ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>権限がありません</li></ul>|
|message|string|高レベルのエラー メッセージを取得します。|
|innerError|InnerErrorV2|Cognitive Services API のガイドラインに準拠した新しい内部エラー形式。 必須プロパティとして ErrorCode、message、省略可能プロパティとして target、details (キーと値のペア)、inner error (入れ子が可能) が含まれています。|
|innerError.code|string|コード エラー文字列を取得します。|
|innerError.message|string|高レベルのエラー メッセージを取得します。|

## <a name="examples"></a>例

### <a name="example-successful-response"></a>成功した応答の例
以下は成功時の応答の例です。

状態コード:200

```JSON
{
  "value": [
    {
      "format": "PlainText",
      "fileExtensions": [
        ".txt"
      ],
      "contentTypes": [
        "text/plain"
      ],
      "versions": []
    },
    {
      "format": "PortableDocumentFormat",
      "fileExtensions": [
        ".pdf"
      ],
      "contentTypes": [
        "application/pdf"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlPresentation",
      "fileExtensions": [
        ".pptx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.presentationml.presentation"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlSpreadsheet",
      "fileExtensions": [
        ".xlsx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
      ],
      "versions": []
    },
    {
      "format": "OutlookMailMessage",
      "fileExtensions": [
        ".msg"
      ],
      "contentTypes": [
        "application/vnd.ms-outlook"
      ],
      "versions": []
    },
    {
      "format": "HtmlFile",
      "fileExtensions": [
        ".html"
      ],
      "contentTypes": [
        "text/html"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlWord",
      "fileExtensions": [
        ".docx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
      ],
      "versions": []
    }
  ]
}
```

### <a name="example-error-response"></a>エラー応答の例

以下は、エラー応答の例です。 他のエラー コードのスキーマも同じです。

状態コード: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

クイックスタートに従って、ドキュメント翻訳とクライアント ライブラリの使用について学習します。

> [!div class="nextstepaction"]
> [ドキュメント変換を使ってみる](../get-started-with-document-translation.md)