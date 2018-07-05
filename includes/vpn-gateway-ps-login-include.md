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
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313678"
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
