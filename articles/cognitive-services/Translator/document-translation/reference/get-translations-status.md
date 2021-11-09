---
title: 翻訳状態の取得
titleSuffix: Azure Cognitive Services
description: 翻訳状態の取得メソッドは、送信されたバッチ要求の一覧と各要求の状態を返します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/22/2021
ms.openlocfilehash: d16de5ccc4beddef8296211d7e5f0c154f10776d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059017"
---
# <a name="get-translations-status"></a>翻訳状態の取得

翻訳状態の取得メソッドは、送信されたバッチ要求の一覧と各要求の状態を返します。 この一覧には、(リソースに基づいて) ユーザーから送信されたバッチ要求のみが表示されます。

要求の数がページングの上限を超える場合は、サーバー側のページングが使用されます。 応答がページ分割されている場合は結果の一部を示しており、応答の中に継続トークンが含まれています。 継続トークンがない場合は、他にページがないことを意味します。

$top、$skip、$maxpagesize のクエリ パラメーターを使用して、返される結果の数とコレクションのオフセットを指定できます。

$top は、ユーザーがすべてのページで返すレコードの総数を示します。 $skip は、並べ替え方法に基づいて、バッチの一覧からスキップするレコードの数を示します。 既定では、降順の開始時刻で並べ替えを行います。 $maxpagesize は、1 つのページで返される項目の最大数です。 $top 経由で要求された項目が多い場合 (または $top が指定されていない場合に返される項目が多い場合)、次のページへのリンク @nextLink が含まれます。

$orderBy クエリ パラメーターを使用して、返されたリスト ("$orderBy=createdDateTimeUtc asc" や "$orderBy=createdDateTimeUtc desc" など) を並べ替えることができます。 既定の並べ替えは createdDateTimeUtc で降順です。 一部のクエリ パラメーターを使用して、返された一覧をフィルター処理できます (例: "status=Succeeded,Cancelled" は、成功した操作と取り消された操作のみを返します)。 createdDateTimeUtcStart と createdDateTimeUtcEnd は、組み合わせて、または個別に使用して、返されたリストをフィルター処理する datetime の範囲を指定できます。 サポートされているフィルター処理クエリ パラメーターは (status, IDs, createdDateTimeUtcStart, createdDateTimeUtcEnd) です。

サーバーでは、クライアントによって指定された値が受け入れられます。 ただし、異なるページ サイズや継続トークンを含む応答を処理できるようクライアントを準備する必要があります。

$top と $skip の両方が含まれているとき、サーバーでは、最初に $skip がコレクションに適用され、それから $top が適用される必要があります。 

> [!NOTE]
> サーバーが $top または $skip を受け入れられない場合、サーバーでは、クエリ オプションを無視するだけではなく、そのことを通知するエラーをクライアントに返す必要があります。 これにより、返されるデータについてクライアントが憶測を立てるリスクが軽減されます。

## <a name="request-url"></a>要求 URL

`GET` 要求の送信先は次のとおりです。
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches
```

[カスタム ドメイン名](../get-started-with-document-translation.md#find-your-custom-domain-name)を見つける方法について説明します。

> [!IMPORTANT]
>
> * **ドキュメント翻訳サービスへのすべての API 要求には、カスタム ドメイン エンドポイントが必要です**。
> * ドキュメント翻訳への HTTP 要求を行うために、Azure portal リソースの _[キーとエンドポイント]_ ページで見つかるエンドポイントも、グローバル Translator エンドポイント `api.cognitive.microsofttranslator.com` も使用することはできません。

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

|Query parameter (クエリ パラメーター)|/|必須|Type|説明|
|--- |--- |--- |---|---|
|$maxpagesize|query|×|integer int32|$maxpagesize は、1 つのページで返される項目の最大数です。 $top 経由で要求された項目が多い場合 (または $top が指定されていない場合に返される項目が多い場合)、次のページへのリンク @nextLink が含まれます。 クライアントは、$maxpagesize の設定を指定することによって、特定のページ サイズのサーバー駆動のページングを要求できます。 指定したページ サイズがサーバーの既定のページ サイズよりも小さい場合、サーバーでは、この設定を優先する必要があります。|
|$orderBy|query|×|array|コレクションの並べ替えクエリ (例: ' CreatedDateTimeUtc asc '、' CreatedDateTimeUtc desc ')|
|$skip|query|×|integer int32|$skip は、指定した並べ替え方法に基づいて、サーバーによって保持されているレコードの一覧からスキップするレコードの数を示します。 既定では、降順の開始時刻で並べ替えを行います。 クライアントは $top と $skip のクエリ パラメーターを使用して、返される結果の数とコレクションのオフセットを指定できます。 クライアントによって $top と $skip の両方が指定されている場合、サーバーでは、最初に $skip がコレクションに適用され、それから $top が適用される必要があります。注: サーバーが $top や $skip を受け入れられない場合、サーバーでは、クエリ オプションを無視するだけではなく、そのことを通知するエラーをクライアントに返す必要があります。|
|$top|query|×|integer int32|$top は、ユーザーがすべてのページで返すレコードの総数を示します。 クライアントは $top と $skip のクエリ パラメーターを使用して、返される結果の数とコレクションのオフセットを指定できます。 クライアントによって $top と $skip の両方が指定されている場合、サーバーでは、最初に $skip がコレクションに適用され、それから $top が適用される必要があります。注: サーバーが $top や $skip を受け入れられない場合、サーバーでは、クエリ オプションを無視するだけではなく、そのことを通知するエラーをクライアントに返す必要があります。|
|createdDateTimeUtcEnd|query|×|string date-time|項目を取得する終了日時。|
|createdDateTimeUtcStart|query|×|string date-time|項目を取得する開始日時。|
|ids|query|×|array|フィルター処理で使用する ID。|
|statuses|query|×|array|フィルター処理で使用する状態。|

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
|その他の状態コード|<ul><li>要求が多すぎます</li><li>サーバーが一時的に使用できません。</li></ul>|

## <a name="get-translations-status-response"></a>翻訳状態の取得の応答

### <a name="successful-get-translations-status-response"></a>翻訳状態の取得の応答に成功

成功した応答では、次の情報が返されます。

|名前|Type|説明|
|--- |--- |--- |
|@nextLink|string|次のページの URL。 追加のページがない場合は Null。|
|value|TranslationStatus[]|TranslationStatus [] 配列を以下に示します。|
|value.id|string|操作の ID。|
|value.createdDateTimeUtc|string|操作が作成された日時。|
|value.lastActionDateTimeUtc|string|操作の状態が更新された日時。|
|value.status|String|ジョブまたはドキュメントの可能な状態の一覧: <ul><li>Canceled</li><li>Cancelling</li><li>失敗</li><li>NotStarted</li><li>実行中</li><li>成功</li><li>ValidationFailed</li></ul>|
|value.summary|StatusSummary[]|下の詳細を含む概要です。|
|value.summary.total|整数 (integer)|ドキュメントの合計数。|
|value.summary.failed|整数 (integer)|ドキュメントの失敗数。|
|value.summary.success|整数 (integer)|正常に翻訳されたドキュメントの数。|
|value.summary.inProgress|整数 (integer)|進行中のドキュメントの数。|
|value.summary.notYetStarted|整数 (integer)|まだ処理を開始していないドキュメントの数。|
|value.summary.cancelled|整数 (integer)|キャンセルされたドキュメントの数。|
|value.summary.totalCharacterCharged|整数 (integer)|課金される文字の合計数。|

### <a name="error-response"></a>エラー応答

|名前|Type|説明|
|--- |--- |--- |
|code|string|高レベルのエラー コードを含む列挙型。 指定できる値<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>権限がありません</li></ul>|
|message|string|高レベルのエラー メッセージを取得します。|
|ターゲット (target)|string|エラーのソースを取得します。 たとえば、無効なドキュメントの場合 "documents" または "document ID" となります。|
|innerError|InnerTranslationError|Cognitive Services API のガイドラインに準拠した新しい内部エラー形式。 必須プロパティとして ErrorCode、message、省略可能プロパティとして target、details (キーと値のペア)、inner error (入れ子が可能) が含まれています。|
|innerError.code|string|コード エラー文字列を取得します。|
|innerError.message|string|高レベルのエラー メッセージを取得します。|
|innerError.target|string|エラーのソースを取得します。 たとえば、無効なドキュメントの場合 "documents" または "document ID" となります。|

## <a name="examples"></a>例

### <a name="example-successful-response"></a>成功した応答の例

以下は成功時の応答の例です。

```JSON
{
    "value": [
        {
            "id": "36724748-f7a0-4db7-b7fd-f041ddc75033",
            "createdDateTimeUtc": "2021-06-18T03:35:30.153374Z",
            "lastActionDateTimeUtc": "2021-06-18T03:36:44.6155316Z",
            "status": "Succeeded",
            "summary": {
                "total": 3,
                "failed": 2,
                "success": 1,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 0
            }
        },
        {
            "id": "1c7399a7-6913-4f20-bb43-e2fe2ba1a67d",
            "createdDateTimeUtc": "2021-05-24T17:57:43.8356624Z",
            "lastActionDateTimeUtc": "2021-05-24T17:57:47.128391Z",
            "status": "Failed",
            "summary": {
                "total": 1,
                "failed": 1,
                "success": 0,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 0
            }
        },
        {
            "id": "daa2a646-4237-4f5f-9a48-d515c2d9af3c",
            "createdDateTimeUtc": "2021-04-14T19:49:26.988272Z",
            "lastActionDateTimeUtc": "2021-04-14T19:49:43.9818634Z",
            "status": "Succeeded",
            "summary": {
                "total": 2,
                "failed": 0,
                "success": 2,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 21899
            }
        }
    ],
    ""@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0/operations/727BF148-F327-47A0-9481-ABAE6362F11E/documents?$top=5&$skip=15"
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
