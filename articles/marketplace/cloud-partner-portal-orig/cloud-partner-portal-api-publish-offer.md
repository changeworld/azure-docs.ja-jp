---
title: プランの発行 | Azure Marketplace
description: 指定したオファーを発行するための API。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64934971"
---
<a name="publish-an-offer"></a>プランの発行
================

指定されたオファーの発行プロセスを開始します。 この呼び出しは実行時間の長い操作となります。

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI パラメーター
--------------

|  **Name**      |    **説明**                               |  **データの種類** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | パブリッシャー ID。たとえば、`contoso`      |   string       |
|  offerId       | オファー ID                                 |   string       |
|  api-version   | API の最新バージョン                        |   Date         |
|  |  |


<a name="header"></a>ヘッダー
------

|  **Name**        |    **値**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Authorization   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>本文の例
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

|  **Name**               |   **説明**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 発行操作の進捗状況を通知するメール アドレスの、コンマ区切りの一覧です。 |
|  |  |


### <a name="response"></a>Response

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>応答ヘッダー

|  **Name**             |    **値**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | 操作の現在の状態を確認するための、クエリ可能な URL。    |
|  |  |


### <a name="response-status-codes"></a>応答状態コード

| **コード** |  **説明**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - 要求は正常に受け入れられました。 応答には、起動された操作を追跡するために使用できる場所が含まれています。 |
| 400   | `Bad/Malformed request` - エラーの応答本文にさらに情報が含まれている場合があります。                                                               |
| 422   | `Un-processable entity` - 発行されるエンティティの検証が失敗したことを示します。                                                        |
| 404   | `Not found` - クライアントが指定したエンティティが存在しません。                                                                              |
|  |  |
