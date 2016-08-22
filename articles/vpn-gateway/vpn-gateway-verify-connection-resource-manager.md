<properties
   pageTitle="ゲートウェイ接続の確認 | Microsoft Azure"
   description="この記事では、Resource Manager デプロイメント モデルでのゲートウェイ接続を確認する方法について説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/03/2016"
   ms.author="cherylmc"/>

# ゲートウェイ接続を確認する

ゲートウェイ接続は、数種類の方法で確認できます。この記事では、Azure ポータルと PowerShell をそれぞれ使用して Resource Manager のゲートウェイ接続の状態を確認する方法を説明します。


## 開始する前に

PowerShell を使用する場合、Azure Resource Manager PowerShell コマンドレットの最新版をインストールする必要があります。PowerShell コマンドレットのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。Resource Manager コマンドレットの使用方法の詳細については、[Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページをご覧ください。

1. PowerShell コンソールを開き、アカウントに接続します。

		Login-AzureRmAccount

2. アカウントのサブスクリプションを確認します。

		Get-AzureRmSubscription 

3. 使用するサブスクリプションを指定します。

		Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 接続を検証する


[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## 次のステップ

- 仮想ネットワークに仮想マシンを追加できます。手順については、[仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md)に関するページを参照してください。

<!---HONumber=AcomDC_0810_2016-->