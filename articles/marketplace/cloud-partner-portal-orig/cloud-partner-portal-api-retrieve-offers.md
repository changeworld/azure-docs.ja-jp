---
title: オファーの取得 API | Microsoft Docs
description: この API は、パブリッシャーの名前空間に含まれるオファーを要約した一覧を取得します。
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
ms.openlocfilehash: cf6e2fa296d3d9f152edf22685b930586b4de524
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024816"
---
<a name="retrieve-offers"></a>オファーを取得する
===============

パブリッシャーの名前空間に含まれるオファーを要約した一覧を取得します。

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI パラメーター
--------------

| ** 名前 **        |  **説明**                         |  **データの種類** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | パブリッシャー ID。たとえば、`contoso` |   String    |
|  api-version     | API の最新バージョン                    |    日付        |
|  |  |


<a name="header"></a>ヘッダー
------

|  **名前**        |         **値**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Authorization   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>本文の例
------------

### <a name="response"></a>Response

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>応答本文のプロパティ

|  **名前**       |       **説明**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | オファーの種類を示します                                                                                           |
|  publisherId    | パブリッシャーを一意に示す ID                                                                      |
|  status         | オファーの状態。 使用可能な値の一覧については、下の「[オファーの状態](#offer-status)」を参照してください。                         |
|  id             | パブリッシャーの名前空間内のオファーを一意に示す GUID。                                                    |
|  version        | オファーの現在のバージョン。 バージョン プロパティはクライアントでは変更できません。 発行のたびにインクリメントされます。 |
|  定義     | ワークロードの実際の定義の要約ビューが含まれます。 詳細な定義を取得するには、[Retrieve specific offer](./cloud-partner-portal-api-retrieve-specific-offer.md) API を使用します。 |
|  changedTime    | オファーが最後に変更されたときの UTC 日時                                                                              |
|  |  |


### <a name="response-status-codes"></a>応答状態コード

| **コード**  |  **説明**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - 要求が正常に処理され、発行元のすべてのオファーがクライアントに返されました。  |
|  400      | `Bad/Malformed request` - エラーの応答本文にさらに情報が含まれている場合があります。                                    |
|  403      | `Forbidden` - クライアントは、指定の名前空間にアクセスできません。                                          |
|  404      | `Not found` - 指定のエンティティが存在しません。                                                                 |
|  |  |


### <a name="offer-status"></a>オファーの状態

|  **名前**                    | **説明**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | オファーは発行されていません。                  |
|  NotStarted                  | 新しいオファーですが、開始されていません。                 |
|  WaitingForPublisherReview   | オファーは発行元の承認を待っています。         |
|  実行中                     | オファー提出が処理されています。             |
|  成功                   | オファー提出の処理が完了しています。       |
|  Canceled                    | オファー提出が取り消されました。                   |
|  失敗                      | オファーを提出できませんでした。                         |
|  |  |
