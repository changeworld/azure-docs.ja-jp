---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157446"
---
この構成を開始する前に、Azure アカウントにサインインする必要があります。 このコマンドレットは、Azure アカウントのサインイン資格情報をユーザーに求めます。 サインイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。 詳細については、「 [リソース マネージャーでの Windows PowerShell の使用](../articles/powershell-azure-resource-manager.md)」を参照してください。

サインインするには、昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

```powershell
Connect-AzAccount
```

複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

```powershell
Get-AzSubscription
```

使用するサブスクリプションを指定します。

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
