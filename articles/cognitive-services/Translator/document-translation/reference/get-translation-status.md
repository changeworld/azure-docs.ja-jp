---
title: 翻訳状態の取得
titleSuffix: Azure Cognitive Services
description: 翻訳状態の取得メソッドは、ドキュメント翻訳要求の状態を返します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.openlocfilehash: f892f9d5e681b7895f7e3d9dc053a9abf0ff00e1
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061984"
---
# <a name="get-translation-status"></a>翻訳状態の取得

翻訳状態の取得メソッドは、ドキュメント翻訳要求の状態を返します。 状態には、要求の全体的な状態と、その要求の一部として翻訳されているドキュメントの状態が含まれます。

## <a name="request-url"></a>要求 URL

`GET` 要求の送信先は次のとおりです。
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/{id}
```

[カスタム ドメイン名](../get-started-with-document-translation.md#find-your-custom-domain-name)を見つける方法について説明します。

> [!IMPORTANT]
>
> * **ドキュメント翻訳サービスへのすべての API 要求には、カスタム ドメイン エンドポイントが必要です**。
> * ドキュメント翻訳への HTTP 要求を行うために、Azure portal リソースの _[キーとエンドポイント]_ ページで見つかるエンドポイントも、グローバル Translator エンドポイント `api.cognitive.microsofttranslator.com` も使用することはできません。


## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

|Query parameter (クエリ パラメーター)|必須|説明|
|--- |--- |--- |
|id|はい|操作 ID。|

## <a name="request-headers"></a>要求ヘッダー

要求ヘッダーを次に示します。

|ヘッダー|説明|
|--- |--- |
|Ocp-Apim-Subscription-Key|必要な要求ヘッダー|

## <a name="response-status-codes"></a>応答状態コード

要求によって返される可能性のある HTTP 状態コードを次に示します。

|状態コード|説明|
|--- |--- |
|200|OK です。 要求が成功して、バッチ翻訳操作の状態を返します。 HeadersRetry-After: integerETag: string|
|401|権限がありません。 資格情報を確認してください。|
|404|リソースが見つかりません。|
|500|内部サーバー エラー。|
|その他の状態コード|<ul><li>要求が多すぎます</li><li>サーバーが一時的に使用できません。</li></ul>|

## <a name="get-translation-status-response"></a>翻訳状態の取得の応答

### <a name="successful-get-translation-status-response"></a>翻訳状態の取得の応答に成功

成功した応答では、次の情報が返されます。

|名前|Type|説明|
|--- |--- |--- |
|id|string|操作の ID。|
|createdDateTimeUtc|string|操作が作成された日時。|
|lastActionDateTimeUtc|string|操作の状態が更新された日時。|
|status|String|ジョブまたはドキュメントの可能な状態の一覧: <ul><li>Canceled</li><li>Cancelling</li><li>失敗</li><li>NotStarted</li><li>実行中</li><li>成功</li><li>ValidationFailed</li></ul>|
|まとめ|StatusSummary|下の詳細を含む概要です。|
|summary.total|整数 (integer)|総数。|
|summary.failed|整数 (integer)|失敗数|
|summary.success|整数 (integer)|成功した数。|
|summary.inProgress|整数 (integer)|進行中の数。|
|summary.notYetStarted|整数 (integer)|まだ開始されていない数。|
|summary.cancelled|整数 (integer)|取り消された数。|
|summary.totalCharacterCharged|整数 (integer)|API によって課金される文字数合計。|

### <a name="error-response"></a>エラー応答

|名前|Type|説明|
|--- |--- |--- |
|code|string|高レベルのエラー コードを含む列挙型。 指定できる値<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>権限がありません</li></ul>|
|message|string|高レベルのエラー メッセージを取得します。|
|ターゲット (target)|string|エラーのソースを取得します。 たとえば、無効なドキュメントの場合 "documents" または "document id" となります。|
|innerError|InnerTranslationError|Cognitive Services API のガイドラインに準拠した新しい内部エラー形式。 必須プロパティとして ErrorCode、message、省略可能プロパティとして target、details (キーと値のペア)、inner error (入れ子が可能) が含まれています。|
|innerError.code|string|コード エラー文字列を取得します。|
|innerError.message|string|高レベルのエラー メッセージを取得します。|
|innerError.target|string|エラーのソースを取得します。 たとえば、無効なドキュメントの場合は "documents" または "document id" になります。|

## <a name="examples"></a>例

### <a name="example-successful-response"></a>成功した応答の例

次の JSON オブジェクトは、成功時の応答の例です。

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>エラー応答の例

次の JSON オブジェクトは、エラー応答の例です。 他のエラー コードのスキーマも同じです。

状態コード: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

クイックスタートに従って、ドキュメント翻訳とクライアント ライブラリの使用について学習します。

> [!div class="nextstepaction"]
> [ドキュメント変換を使ってみる](../get-started-with-document-translation.md)
