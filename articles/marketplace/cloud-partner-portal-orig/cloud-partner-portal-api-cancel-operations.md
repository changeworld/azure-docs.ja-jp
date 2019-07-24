---
title: 操作のキャンセル API | Azure Marketplace
description: 操作をキャンセルします。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 70ffd13be4ba934b423e3bb5344eea0a9c36886c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935560"
---
# <a name="cancel-operation"></a>操作を取り消す 

この API は、現在プランで進行中の操作をキャンセルします。 この API に渡す `operationId` は、[操作の取得 API](./cloud-partner-portal-api-retrieve-operations.md) を使用して取得します。 キャンセルは同期操作であるのが一般的ですが、一部の複雑なシナリオでは、既存の操作をキャンセルするために新しい操作が必要になることがあります。 このケースでは、状態を照会するために使用すべき操作の場所が HTTP 応答本文に格納されます。

要求と共にメール アドレスのコンマ区切りのリストを指定すると、それらのアドレスに操作の進行状況が API から通知されます。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI パラメーター
--------------

|  **Name**    |      **説明**                                  |    **データの種類**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  パブリッシャー ID (例: `contoso`)         |   string          |
| offerId      |  オファー ID                                     |   string          |
| api-version  |  API の現在のバージョン                               |    Date           |
|  |  |  |


<a name="header"></a>ヘッダー
------

|  **Name**              |  **値**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Authorization         |  Bearer <実際のトークン> |
|  |  |


<a name="body-example"></a>本文の例
------------

### <a name="request"></a>Request

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>要求本文のプロパティ

|  **Name**                |  **説明**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | 発行操作の進捗状況を通知するメール ID の、コンマ区切りの一覧です。 |
|  |  |


### <a name="response"></a>Response

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>応答ヘッダー

|  **Name**             |    **値**                       |
|  ---------            |    ----------                      |
| Operation-Location    | 操作の現在の状態を確認するための、クエリ可能な URL。 |
|  |  |


### <a name="response-status-codes"></a>応答状態コード

| **コード**  |  **説明**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK 要求は正常に処理され、操作は同期的にキャンセルされました。 |
|  202      | 受理されました。 要求は正常に処理され、操作のキャンセル処理が行われています。 キャンセル操作の場所は、応答ヘッダーで返されます。 |
|  400      | 要求が無効であるか、形式が正しくありません。 エラーの応答本文にさらに情報が含まれている場合があります。  |
|  403      | アクセスは禁止されています。 クライアントは、要求に指定されている名前空間にアクセスできません。 |
|  404      | 見つかりません。 指定のエンティティが存在しません。 |
|  |  |
