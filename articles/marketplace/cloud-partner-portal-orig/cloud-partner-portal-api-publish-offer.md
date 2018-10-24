---
title: オファーの発行 | Microsoft Docs
description: 指定したオファーを発行するための API。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cb1293a771a137f4df7e36a2b412f68b384f16ef
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808544"
---
<a name="publish-an-offer"></a>プランの発行
================

指定されたオファーの発行プロセスを開始します。 この呼び出しは実行時間の長い操作となります。

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI パラメーター
--------------

|  **名前**      |    **説明**                               |  **データの種類** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | パブリッシャー ID。たとえば、`contoso`      |   String       |
|  offerId       | オファー ID                                 |   String       |
|  api-version   | API の最新バージョン                        |   日付         |
|  |  |


<a name="header"></a>ヘッダー
------

|  **名前**        |    **値**          |
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

|  **名前**               |   **説明**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 発行操作の進捗状況を通知するメール アドレスの、コンマ区切りの一覧です。 |
|  |  |


### <a name="response"></a>Response

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>応答ヘッダー

|  **名前**             |    **値**                                                                 |
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
