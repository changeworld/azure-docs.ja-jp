---
title: '障害が発生した Azure ExpressRoute 回線のリセット: PowerShell | Microsoft Docs'
description: この記事は、障害状態になっている ExpressRoute 回線をリセットする際に役立ちます。
documentationcenter: na
services: expressroute
author: anzaman
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 423bc1d6409e5b7fe02339a05d0775f4ff42de49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="reset-a-failed-expressroute-circuit"></a>障害が発生した ExpressRoute 回線のリセット

ExpressRoute 回線での操作が正常に完了しないと、回線が "障害" 状態になることがあります。 この記事は、障害状態の ExpressRoute 回線をリセットする際に役立ちます。

## <a name="reset-a-circuit"></a>回線のリセット

1. Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 詳細については、[Azure PowerShell のインストールおよび構成](/powershell/azure/install-azurerm-ps)をご覧ください。

2. 昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

  ```powershell
  Connect-AzureRmAccount
  ```
3. 複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

  ```powershell
  Get-AzureRmSubscription
  ```
4. 使用するサブスクリプションを指定します。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. 次のコマンドを実行して、障害状態の回線をリセットします。

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

回線はすぐに正常になります。 回線が障害状態のままである場合は、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に対してサポート チケットを申請します。

## <a name="next-steps"></a>次の手順

問題が解消しない場合は [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) にサポート チケットを申請する
