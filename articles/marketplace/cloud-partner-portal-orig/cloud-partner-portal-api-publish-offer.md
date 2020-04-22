---
title: オファーの発行 | Azure Marketplace
description: 指定したオファーを発行するための API。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255942"
---
# <a name="publish-an-offer"></a>プランの発行

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、オファーがパートナー センターに移行された後も引き続き機能します。 この統合では、小さな変更が導入されています。 「[Cloud パートナー ポータルの API リファレンス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)」に記載されている変更内容を調べて、パートナー センターへの移行後もコードが引き続き動作することを確認してください。

指定されたオファーの発行プロセスを開始します。 この呼び出しは実行時間の長い操作となります。

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI パラメーター
--------------

|  **名前**      |    **説明**                               |  **データの種類** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | パブリッシャー ID。たとえば、`contoso`      |   String       |
|  offerId       | オファー ID                                 |   String       |
|  api-version   | API の最新バージョン                        |   Date         |
|  |  |

## <a name="header"></a>ヘッダー
------

|  **名前**        |    **Value**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  承認   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>本文の例
------------

### <a name="request"></a>Request

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>要求本文のプロパティ

|  **名前**               |   **説明**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 発行操作の進捗状況を通知するメール アドレスの、コンマ区切りの一覧です。 |
|  |  |


### <a name="response"></a>Response

#### <a name="migrated-offers"></a>移行されたオファー

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>移行されていないオファー

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>応答ヘッダー

|  **名前**             |    **Value**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| 場所    | この操作の状態を取得する相対パス     |
|  |  |


### <a name="response-status-codes"></a>応答状態コード

| **コード** |  **説明**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - 要求は正常に受け入れられました。 応答には、起動された操作を追跡するために使用できる場所が含まれています。 |
| 400   | `Bad/Malformed request` - エラーの応答本文にさらに情報が含まれている場合があります。                                                               |
| 422   | `Un-processable entity` - 発行されるエンティティの検証が失敗したことを示します。                                                        |
| 404   | `Not found` - クライアントが指定したエンティティが存在しません。                                                                              |
|  |  |
