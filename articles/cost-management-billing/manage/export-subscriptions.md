---
title: Azure サブスクリプションの最上位レベルの情報をエクスポートする | Microsoft Docs
description: アカウントに関連付けられたすべての Azure サブスクリプション ID を表示する方法について説明します。
keywords: ''
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 83b6a9db0107cb35448aec491c81c2630e87ddd7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199723"
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
