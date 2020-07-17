---
title: Azure サブスクリプションの最上位レベルの情報をエクスポートする
description: アカウントに関連付けられたすべての Azure サブスクリプション ID を表示する方法について説明します。
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 87135b309f0406528bcada1cd906dd2f8535d1ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202898"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>最上位レベルのサブスクリプション情報のエクスポートと表示
ユーザーの資格情報に関連付けられたサブスクリプション ID のセットを表示する必要がある場合は、[Azure アカウント センターからサブスクリプション情報を含む .json ファイルをダウンロード](https://account.azure.com/subscriptions/download)します。

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

ダウンロードした .json ファイルには、次の情報が含まれています。
- 電子メール:アカウントに関連付けられているメール アドレス。
- Puid:請求先アカウントに関連付けられている一意の識別子。
- SubscriptionIds:アカウントに属するサブスクリプションの一覧。サブスクリプション ID 別に列挙されます。

### <a name="subscriptionsjson-sample"></a>subscriptions.json のサンプル

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
