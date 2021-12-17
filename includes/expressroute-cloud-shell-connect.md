---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 9894787ed29226ad6e8d491b498b082cadd0626b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110690811"
---
 Azure Cloud Shell を使用している場合は、[試してみる] をクリックすると、Azure アカウントに自動的にサインインします。 ローカルにサインインするには、昇格された特権で PowerShell コンソールを開き、コマンドレットを実行して接続します。

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
