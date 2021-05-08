---
title: ドキュメント翻訳のキャンセル操作メソッド
titleSuffix: Azure Cognitive Services
description: キャンセル操作メソッドでは、現在処理中またはキューに入っている操作がキャンセルされます。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 39730f118dd93a972f238f85ef890f4dc54ca91a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613821"
---
# <a name="document-translation-cancel-operations"></a>ドキュメント翻訳: キャンセル操作

現在処理中またはキューに入っているジョブをキャンセルします。 操作は、既に完了または失敗したか、キャンセル中の場合はキャンセルされません。 無効な要求が返されます。 翻訳が完了したすべてのドキュメントはキャンセルされず、課金されます。 すべての保留中ドキュメントについては、可能な場合はキャンセルされます。

## <a name="request-url"></a>要求 URL

`DELETE` 要求の送信先は次のとおりです。
```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

[カスタム ドメイン名](../get-started-with-document-translation.md#find-your-custom-domain-name)を見つける方法について説明します。

> [!IMPORTANT]
>
> * **ドキュメント翻訳サービスへのすべての API 要求には、カスタム ドメイン エンドポイントが必要です**。
> * ドキュメント翻訳への HTTP 要求を行うために、Azure portal リソースの _[キーとエンドポイント]_ ページで見つかるエンドポイントも、グローバル Translator エンドポイント `api.cognitive.microsofttranslator.com` も使用することはできません。

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

|Query parameter (クエリ パラメーター)|必須|説明|
|-----|-----|-----|
|id|はい|操作 ID。|

## <a name="request-headers"></a>要求ヘッダー

要求ヘッダーを次に示します。

|ヘッダー|説明|
|-----|-----|
|Ocp-Apim-Subscription-Key|必要な要求ヘッダー|

## <a name="response-status-codes"></a>応答状態コード

要求によって返される可能性のある HTTP 状態コードを次に示します。

| 状態コード| 説明|
|-----|-----|
|200|OK です。 キャンセル要求が送信されました|
|401|権限がありません。 資格情報を確認してください。|
|404|見つかりません。 リソースが見つかりません。 
|500|内部サーバー エラー。
|その他の状態コード|<ul><li>要求が多すぎます</li><li>サーバーが一時的に使用できません</li></ul>|

## <a name="cancel-operations-response"></a>キャンセル操作の応答

### <a name="successful-response"></a>成功応答

成功した応答では、次の情報が返されます。

|名前|Type|説明|
|--- |--- |--- |
|id|string|操作の ID。|
|createdDateTimeUtc|string|操作が作成された日時。|
|lastActionDateTimeUtc|string|操作の状態が更新された日時。|
|status|String|ジョブまたはドキュメントの可能な状態の一覧: <ul><li>Canceled</li><li>Cancelling</li><li>失敗</li><li>NotStarted</li><li>実行中</li><li>成功</li><li>ValidationFailed</li></ul>|
|まとめ|StatusSummary|下の詳細を含む概要です。|
|summary.total|整数 (integer)|ドキュメントの合計数。|
|summary.failed|整数 (integer)|ドキュメントの失敗数。|
|summary.success|整数 (integer)|正常に翻訳されたドキュメントの数。|
|summary.inProgress|整数 (integer)|進行中のドキュメントの数|
|summary.notYetStarted|整数 (integer)|まだ処理を開始していないドキュメントの数。|
|summary.cancelled|整数 (integer)|キャンセルされた数。|
|summary.totalCharacterCharged|整数 (integer)|API によって課金される文字数合計。|

### <a name="error-response"></a>エラー応答

|名前|Type|説明|
|--- |--- |--- |
|code|string|高レベルのエラー コードを含む列挙型。 指定できる値<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>権限がありません</li></ul>|
|message|string|高レベルのエラー メッセージを取得します。|
|ターゲット (target)|string|エラーのソースを取得します。 たとえば、無効なドキュメントの "documents" または "document id" になります。|
|innerError|InnerErrorV2|Cognitive Services API のガイドラインに準拠した新しい内部エラー形式。 必須プロパティとして ErrorCode、message、省略可能プロパティとして target、details (キーと値のペア)、inner error (入れ子が可能) が含まれています。|
|innerError.code|string|コード エラー文字列を取得します。|
|inner.Eroor.message|string|高レベルのエラー メッセージを取得します。|

## <a name="examples"></a>例

### <a name="example-successful-response"></a>成功した応答の例

次の JSON オブジェクトは、成功時の応答の例です。

状態コード:200

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

状態コード: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
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
