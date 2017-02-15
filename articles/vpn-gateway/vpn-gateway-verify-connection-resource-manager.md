---
title: "ゲートウェイ接続の確認 | Microsoft Docs"
description: "この記事では、Resource Manager デプロイメント モデルでのゲートウェイ接続を確認する方法について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 420b193d558d00f574d24c87ab9db6e9e757a952


---
# <a name="verify-a-gateway-connection"></a>ゲートウェイ接続を確認する
ゲートウェイ接続は、数種類の方法で確認できます。 この記事では、Azure ポータルと PowerShell をそれぞれ使用して Resource Manager のゲートウェイ接続の状態を確認する方法を説明します。

## <a name="verify-using-powershell"></a>PowerShell を使用した確認
Azure リソース マネージャー PowerShell コマンドレットの最新版をインストールする必要があります。 PowerShell コマンドレットのインストールについて詳しくは、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。 Resource Manager コマンドレットの使用方法の詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページをご覧ください。

### <a name="step-1-log-in-to-your-azure-account"></a>手順 1: Azure アカウントにログインする
1. 昇格された特権で PowerShell コンソールを開き、アカウントに接続します。
   
        Login-AzureRmAccount
2. アカウントのサブスクリプションを確認します。
   
        Get-AzureRmSubscription 
3. 使用するサブスクリプションを指定します。
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>手順 2: 接続を確認する
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>Azure Portal を使用した確認
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>次のステップ
* 仮想ネットワークに仮想マシンを追加できます。 手順については、 [仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関するページを参照してください。




<!--HONumber=Nov16_HO3-->


