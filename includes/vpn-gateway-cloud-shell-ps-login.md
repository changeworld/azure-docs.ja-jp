---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77133594"
---
昇格された特権で PowerShell コンソールを開きます。

Azure PowerShell をローカルで実行している場合は、お使いの Azure アカウントに接続してください。 *Connect-AzAccount* コマンドレットでは、資格情報の入力を求められます。 認証後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。 代わりに Azure Cloud Shell を使用している場合は、*Connect-AzAccount* を実行する必要はありません。 Azure Cloud Shell によって、Azure アカウントに自動的に接続されます。

```azurepowershell
Connect-AzAccount
```

複数のサブスクリプションがある場合は、Azure サブスクリプションの一覧を取得します。

```azurepowershell-interactive
Get-AzSubscription
```

使用するサブスクリプションを指定します。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```