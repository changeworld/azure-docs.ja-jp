---
title: 操作のキャンセル API | Azure Marketplace
description: 操作をキャンセルします。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256435"
---
# <a name="cancel-operation"></a>操作を取り消す

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、プランがパートナー センターに移行された後も引き続き機能します。 この統合では、小さな変更がなされています。 「[Cloud パートナー ポータルの API リファレンス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)」に記載されている変更内容を調べて、パートナー センターへの移行後もコードが引き続き動作することを確認してください。

この API は、現在プランで進行中の操作をキャンセルします。 この API に渡す `operationId` は、[操作の取得 API](./cloud-partner-portal-api-retrieve-operations.md) を使用して取得します。 キャンセルは同期操作であるのが一般的ですが、一部の複雑なシナリオでは、既存の操作をキャンセルするために新しい操作が必要になることがあります。 このケースでは、状態を照会するために使用すべき操作の場所が HTTP 応答本文に格納されます。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI パラメーター

--------------

|  **名前**    |      **説明**                                  |    **データの種類**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  パブリッシャー ID (例: `contoso`)         |   String          |
| offerId      |  オファー ID                                     |   String          |
| api-version  |  API の現在のバージョン                               |    Date           |
|  |  |  |

## <a name="header"></a>ヘッダー
------

|  **名前**              |  **Value**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  承認         |  Bearer <実際のトークン> |
|  |  |

## <a name="body-example"></a>本文の例
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

|  **名前**                |  **説明**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | 発行操作の進捗状況を通知するメール ID の、コンマ区切りの一覧です。 |
|  |  |

### <a name="response"></a>Response

#### <a name="migrated-offers"></a>移行されたオファー

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>移行されていないオファー

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>応答ヘッダー

|  **名前**             |    **Value**                       |
|  ---------            |    ----------                      |
| 場所    | この操作の状態を取得する相対パス。 |
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
