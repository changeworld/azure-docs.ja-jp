---
title: ドキュメント翻訳の操作の取得
titleSuffix: Azure Cognitive Services
description: 操作の取得メソッドは、送信されたバッチ要求の一覧と各要求の状態を返します。
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: fd7cee564aa3a00e21d1e707d08a18115d519925
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484678"
---
# <a name="document-translation-get-operations"></a>ドキュメント翻訳: 操作の取得

操作の取得メソッドは、送信されたバッチ要求の一覧と各要求の状態を返します。 この一覧には、(サブスクリプションに基づいて) ユーザーから送信されたバッチ要求のみが表示されます。 各要求の状態は、ID で並べ替えられます。

要求の数がページングの上限を超える場合は、サーバー側のページングが使用されます。 応答がページ分割されている場合は結果の一部を示しており、応答の中に継続トークンが含まれています。 継続トークンがない場合は、追加ページがないことを意味します。

$top と $skip のクエリ パラメーターを使用して、返される結果の数とコレクションのオフセットを指定できます。

サーバーでは、クライアントによって指定された値が受け入れられます。 ただし、異なるページ サイズや継続トークンを含む応答を処理できるようクライアントを準備する必要があります。

$top と $skip の両方が含まれているとき、サーバーでは、最初に $skip がコレクションに適用され、それから $top が適用される必要があります。 

> [!NOTE]
> サーバーが $top または $skip を受け入れられない場合、サーバーでは、クエリ オプションを無視するだけではなく、そのことを通知するエラーをクライアントに返す必要があります。 これにより、返されるデータについてクライアントが憶測を立てるリスクが軽減されます。

## <a name="request-url"></a>要求 URL

`GET` 要求の送信先は次のとおりです。
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|いいえ|コレクション内の $skip エントリをスキップします。 $top と $skip の両方が指定されている場合、$skip が先に適用されます。|
|$top|いいえ|コレクション内の $top エントリを受け取ります。 $top と $skip の両方が指定されている場合、$skip が先に適用されます。|

## <a name="request-headers"></a>要求ヘッダー

要求ヘッダーを次に示します。

|ヘッダー|説明|
|--- |--- |
|Ocp-Apim-Subscription-Key|必要な要求ヘッダー|

## <a name="response-status-codes"></a>応答状態コード

要求によって返される可能性のある HTTP 状態コードを次に示します。

|状態コード|説明|
|--- |--- |
|200|OK です。 要求が成功して、すべての操作の状態を返します。 HeadersRetry-After: integerETag: string|
|400|正しくない要求。 無効な要求です。 入力パラメーターを確認してください。|
|401|権限がありません。 資格情報を確認してください。|
|500|内部サーバー エラー。|
|その他の状態コード|<ul><li>要求が多すぎます</li><li>サーバーが一時的に使用できません</li></ul>|

## <a name="get-operations-response"></a>操作の取得の応答

### <a name="successful-get-operations-response"></a>成功した操作の取得の応答

成功した応答では、次の情報が返されます。

|名前|Type|説明|
|--- |--- |--- |
|id|string|操作の ID。|
|createdDateTimeUtc|string|操作が作成された日時。|
|lastActionDateTimeUtc|string|操作の状態が更新された日時。|
|status|String|ジョブまたはドキュメントの可能な状態の一覧: <ul><li>Canceled</li><li>Cancelling</li><li>失敗</li><li>NotStarted</li><li>実行中</li><li>成功</li><li>ValidationFailed</li></ul>|
|まとめ|StatusSummary[]|下の詳細を含む概要です。|
|summary.total|整数 (integer)|ドキュメントの合計数。|
|summary.failed|整数 (integer)|ドキュメントの失敗数。|
|summary.success|整数 (integer)|正常に翻訳されたドキュメントの数。|
|summary.inProgress|整数 (integer)|進行中のドキュメントの数。|
|summary.notYetStarted|整数 (integer)|まだ処理を開始していないドキュメントの数。|
|summary.cancelled|整数 (integer)|キャンセルされたドキュメントの数。|
|summary.totalCharacterCharged|整数 (integer)|課金される文字の合計数。|

### <a name="error-response"></a>エラー応答

|名前|Type|説明|
|--- |--- |--- |
|code|string|高レベルのエラー コードを含む列挙型。 指定できる値<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>権限がありません</li></ul>|
|message|string|高レベルのエラー メッセージを取得します。|
|ターゲット (target)|string|エラーのソースを取得します。 たとえば、無効なドキュメントの場合は "documents" または "document id" になります。|
|innerError|InnerErrorV2|Cognitive Services API のガイドラインに準拠した新しい内部エラー形式。 必須プロパティとして ErrorCode、message、省略可能プロパティとして target、details (キーと値のペア)、inner error (入れ子が可能) が含まれています。|
|innerError.code|string|コード エラー文字列を取得します。|
|innerError.message|string|高レベルのエラー メッセージを取得します。|

## <a name="examples"></a>例

### <a name="example-successful-response"></a>成功した応答の例

以下は成功時の応答の例です。

```JSON
{
  "value": [
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
