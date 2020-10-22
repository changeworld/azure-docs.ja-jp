---
title: Azure でロード バランサーの TCP リセットおよびアイドル タイムアウトを構成する
titleSuffix: Azure Load Balancer
description: この記事では Azure Load Balancer TCP アイドル タイムアウトを構成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649849"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Azure Load Balancer の TCP アイドル タイムアウトを構成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

Azure Load Balancer では、アイドル タイムアウトは 4 分から 120 分に設定されています。 既定では 4 分に設定されています。 アイドル時間がタイムアウト値よりも長い場合、クライアントとクラウド サービス間の TCP または HTTP セッションが維持されるという保証はありません。 TCP アイドル リセットの詳細については、[こちら](load-balancer-tcp-reset.md)を参照してください。

次のセクションでは、パブリック IP とロード バランサー リソースのアイドル タイムアウト設定を変更する方法について説明します。


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>パブリック IP の TCP アイドル タイムアウトを構成する

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` はオプションです。 設定しない場合、既定のタイムアウト時間は 4 分です。 設定できるタイムアウトの範囲は 4 分から 120 分です。

## <a name="set-the-tcp-idle-timeout-on-rules"></a>ルールの TCP アイドル タイムアウトを設定する

ロード バランサーにアイドル タイムアウトを設定するために、'IdleTimeoutInMinutes' は負荷分散規則された規則に設定されています。 次に例を示します。

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>次のステップ

[内部ロード バランサーの概要](load-balancer-internal-overview.md)

[インターネットに接続するロード バランサーの構成の開始](quickstart-load-balancer-standard-public-powershell.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)
