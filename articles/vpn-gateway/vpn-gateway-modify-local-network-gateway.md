<properties
   pageTitle="ローカル ネットワーク ゲートウェイ IP アドレスのプレフィックスとゲートウェイ IP の変更 | Microsoft Azure"
   description="この記事では、ローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する手順について説明します。"
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
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更

ローカル ネットワーク ゲートウェイの AddressPrefix または GatewayIPAddress の設定が変わることがあります。次の手順に従うと、ローカル ネットワーク ゲートウェイの設定を変更することができます。これらの設定は、Azure ポータルで変更することもできます。

## 開始する前に
	
Azure リソース マネージャー PowerShell コマンドレットの最新版をインストールする必要があります。PowerShell コマンドレットのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

## IP アドレスのプレフィックスを変更するには

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## ゲートウェイ IP アドレスを変更するには

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## 次のステップ

ゲートウェイの接続を確認することができます。「[Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。

<!---HONumber=AcomDC_0810_2016-->