---
title: "Azure サイト間 VPN で接続できない問題のトラブルシューティング | Microsoft Docs"
description: "サイト間 VPN 接続が突然動作を停止して再接続できなくなる問題のトラブルシューティング方法について説明します。"
services: vpn-gateway
documentationcenter: na
author: genlin
manager: willchen
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/21/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: a72a46e4c2c93fdb3ab1a26c8854adb8cdc488a5
ms.contentlocale: ja-jp
ms.lasthandoff: 06/22/2017

---

# <a name="troubleshooting-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>トラブルシューティング: Azure サイト間 VPN が動作を停止して接続できない

サイト間 VPN 接続は、オンプレミス ネットワークと Microsoft Azure 仮想ネットワークとの間で構成します。 VPN 接続が突然動作を停止して再接続できません。 この記事では、この問題の解決に役立つトラブルシューティング手順について説明します。 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

問題を解決するには、まず、[Azure 仮想ネットワーク ゲートウェイをリセット](vpn-gateway-resetgw-classic.md)し、オンプレミス VPN デバイスからのトンネルをリセットしてみてください。 問題が解決しない場合は、以降の手順に従って問題の原因を特定します。

### <a name="prerequisite-step"></a>事前に必要な手順

Azure 仮想ネットワーク ゲートウェイの種類を確認します。

1. [Azure ポータル](https://portal.azure.com)に移動します。
2. 仮想ネットワーク ゲートウェイの **[概要]** ページで、その種類の情報を確認します。
    
    ![ゲートウェイの概要](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>手順 1 オンプレミス VPN デバイスが検証済みであるかどうかを確認する

1. 使っている [VPN デバイスと OS バージョンが検証済み](vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices-and-device-configuration-guides)であるかどうかを確認します。 検証済みの VPN デバイスではない場合、互換性の問題があるかどうかをデバイスの製造元に問い合わせてください。
2. VPN デバイスが正しく構成されていることを確認します。 詳細については、「[デバイス構成のサンプルの編集](/vpn-gateway-about-vpn-devices.md#editing)」を参照してください。

### <a name="step-2-verify-the-shared-keypsk"></a>手順 2 共有キー (PSK) を確認する

オンプレミス VPN デバイスと仮想ネットワーク VPN の**共有キー**を比較し、両者が一致することを確認します。 

Azure VPN 接続の PSK を確認するには、次のいずれかの方法を使用します。

**Azure ポータル**

1. [仮想ネットワーク ゲートウェイ] に移動し、作成したサイト間接続に移動します。
2. **[設定]** セクションで **[共有キー]** をクリックします。
    
    ![共有キー](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Resource Manager モードの場合

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

クラシックの場合

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>手順 3 VPN ピア IP を確認する

-   Azure の **[ローカル ネットワーク ゲートウェイ]** オブジェクトの IP 定義が、オンプレミス デバイスの IP と一致している必要があります。
-   オンプレミス デバイスに設定されている Azure ゲートウェイの IP 定義が、Azure ゲートウェイの IP と一致している必要があります。

### <a name="step-4-nsg-and-udr-on-gateway-subnet"></a>手順 4 ゲートウェイ サブネットの NSG と UDR

ゲートウェイ サブネットのユーザー定義ルート (UDR) またはネットワーク セキュリティ グループ (NSG) があれば削除し、その結果をテストします。 問題が解決した場合は、適用されている NSG または UDR の設定を確認します。

### <a name="step-5-check-on-premises-vpn-device-external-interface-address"></a>手順 5 オンプレミス VPN デバイスの外部インターフェイスのアドレスを確認する

- VPN デバイスのインターネット接続 IP アドレスが Azure の **[ローカル ネットワーク]** の定義に含まれていると、散発的に接続が切れることがあります。
- デバイスの外部インターフェイスは、インターネットに直接接続されている必要があります。 インターネットとデバイスとの間にネットワーク アドレス変換またはファイアウォールを配置することはできません。
-  仮想 IP が割り当てられるようにファイアウォール クラスタリングを構成する場合は、クラスターを分割し、ゲートウェイから対話できるパブリック インターフェイスに直接 VPN アプライアンスを公開する必要があります。

### <a name="step-6-verify--subnets-match-exactly-azure-policy-based-gateways"></a>手順 6 サブネットが完全に一致することを確認する (Azure のポリシー ベースのゲートウェイ)

-   Azure Virtual Network とそのオンプレミス定義との間でサブネットが完全に一致することを確認します。
-   **ローカル ネットワーク ゲートウェイ**とオンプレミス ネットワークのオンプレミス定義との間でサブネットが完全に一致することを確認します。

### <a name="step-5-verify-azure-gateway-health-probe"></a>手順 7 Azure ゲートウェイの正常性プローブを確認する

1. ブラウザーで https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe にアクセスします。
2. 証明書の警告を無視して続行します。
3. 応答が表示された場合、仮想ネットワーク ゲートウェイは正常であると考えられます。 応答を表示されない場合、ゲートウェイが正常な状態にないか、または問題の原因となっている NSG がゲートウェイ サブネット上に存在します。 応答のサンプル テキストを次に示します。

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-pfs-feature-enabled"></a>手順 8 オンプレミス VPN デバイスで Perfect Forward Secrecy (PFS) 機能が有効になっているかどうかを確認する

接続切断の問題は、Perfect Forward Secrecy 機能によって引き起こされる可能性があります。 VPN デバイスで Perfect Forward Secrecy が有効になっている場合は、その機能を無効にしてください。 そのうえで、仮想ネットワーク ゲートウェイの IPsec ポリシーを更新します。

## <a name="next-steps"></a>次のステップ

-   [仮想ネットワークへのサイト間接続を構成する](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [サイト間 VPN 接続の IPsec/IKE ポリシーを構成する](vpn-gateway-ipsecikepolicy-rm-powershell.md)

