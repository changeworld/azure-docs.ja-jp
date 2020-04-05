---
title: API の考慮事項 | Azure Marketplace
description: マーケットプレース API を使用する際のバージョン管理、エラー処理、認可の問題。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4e04f521ed2023dfb9cd562549cb2e1bcd319b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288633"
---
# <a name="api-considerations"></a>API の考慮事項


<a name="api-versioning"></a>API のバージョン管理
--------------

API の複数のバージョンが同時に利用可能である可能性があります。 クライアントは、`api-version` パラメーターをクエリ文字列の一部として提供して、起動するバージョンを指定する必要があります。

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

不明または無効な API バージョンが指定された要求に対する応答は、HTTP コード 400 になります。 このエラーは、既知の API バージョンのコレクションを応答本文で返します。

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>エラー
------

API はエラーに応答する際、対応する HTTP 状態コードと、必要に応じて追加情報を、JSON 形式でシリアル化された応答に含めます。
エラー (特に 400 クラスのエラー) が発生したときには、根本的な原因を修正する前に要求を再試行しないようにしてください。 たとえば、上記のサンプル応答では、要求を再送信する前に API バージョンのパラメーターを修正します。

<a name="authorization-header"></a>Authorization header (Authorization ヘッダー)
--------------------

このリファレンスのすべての API では、Azure Active Directory (Azure AD) から取得したベアラー トークンと共に Authorization ヘッダーを渡す必要があります。 有効な応答を受信するにはこのヘッダーが必要です。存在しない場合は、`401 Unauthorized` エラーが返されます。 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
