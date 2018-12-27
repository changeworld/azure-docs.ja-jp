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
ms.openlocfilehash: d4d370e6b76fcfc502366642842bfeb923a13991
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38732676"
---
この構成を開始する前に、Azure アカウントにサインインする必要があります。 このコマンドレットは、Azure アカウントのサインイン資格情報をユーザーに求めます。 サインイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。 詳細については、「 [リソース マネージャーでの Windows PowerShell の使用](../articles/powershell-azure-resource-manager.md)」を参照してください。

サインインするには、昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

```powershell
Connect-AzureRmAccount
```

複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

```powershell
Get-AzureRmSubscription
```

使用するサブスクリプションを指定します。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
