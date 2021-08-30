---
title: Azure Australia の Azure VPN Gateway
description: Azure Australia に VPN Gateway を実装して ISM に準拠するようにし、オーストラリア政府機関を効果的に保護する
author: emilyre
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: yvettep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0221f28df5ddf53df0aad298cd6692c279b5fb75
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2021
ms.locfileid: "117029040"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure Australia の Azure VPN Gateway

パブリック クラウドにおける重要なサービスは、クラウド リソースおよびサービスから既存のオンプレミス システムへのセキュリティで保護された接続です。 Azure でこの機能を提供するサービスは、Azure VPN Gateway です。 この記事では、オーストラリア通信電子局 (ASD) の [Information Security Manual (ISM) 統制](https://acsc.gov.au/infosec/ism/)に準拠するように VPN ゲートウェイを構成するときの、重要な考慮事項について概説します。

VPN ゲートウェイは、Azure 内の仮想ネットワークと別のネットワークとの間で暗号化されたトラフィックを送信するために使用されます。 VPN ゲートウェイでは、次の 3 つのシナリオに対応しています。

- サイト間 (S2S)
- ポイント対サイト (P2S)
- ネットワーク間

この記事では、S2S VPN ゲートウェイに焦点を当てます。 図 1 は、S2S VPN ゲートウェイの構成例を示しています。

![複数サイト接続を使用した VPN ゲートウェイ](media/vpngateway-multisite-connection-diagram.png)

*図 1 – Azure S2S VPN Gateway*

## <a name="key-design-considerations"></a>設計上の主な考慮事項

オーストラリア政府のお客様に Azure を接続するには、次の 3 つのネットワーク オプションがあります。

- ICON
- Azure ExpressRoute
- パブリック インターネット

Australian Cyber Security Centre の [Azure のコンシューマー ガイド](https://servicetrust.microsoft.com/viewpage/Australia)では、VPN Gateway (または同等の PROTECTED 認定サードパーティ サービス) を 3 つのネットワーク オプションと組み合わせて使用することが推奨されています。 この推奨事項は、接続が、暗号化と整合性についての ISM 統制に準拠していることを確認することです。

### <a name="encryption-and-integrity"></a>暗号化と整合性

既定では、VPN は IKE ハンドシェイクの一部として接続の確立中に、暗号化と整合性のアルゴリズムとパラメーターをネゴシエートします。 IKE ハンドシェイク中、VPN ゲートウェイがイニシエーターまたはレスポンダーのどちらであるかによって、構成と優先度が決まります。 この指定は、VPN デバイスを介して制御されます。 接続の最終的な構成は、VPN デバイスの構成によって制御されます。 確認された VPN デバイスとその構成の詳細については、[VPN サービス](../vpn-gateway/vpn-gateway-about-vpn-devices.md)に関するページを参照してください。

VPN ゲートウェイでは、接続のカスタム IPsec/IKE ポリシーを構成して、暗号化と整合性を制御できます。

### <a name="resource-operations"></a>リソース操作

VPN ゲートウェイでは、パブリック インターネット経由で Azure と Azure 以外の環境の間の接続を作成します。 ISM には、接続の明示的な承認に関連する統制があります。 既定では、VPN ゲートウェイを使用して、承認されていないトンネルをセキュリティで保護された環境に作成することができます。 組織では Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、VPN ゲートウェイとその接続を作成および変更できるユーザーを制御することが重要です。 Azure には VPN ゲートウェイを管理するための組み込みロールがないため、カスタム ロールが必要です。

所有者、共同作成者、ネットワーク共同作成者の各ロールへのアクセスは厳密に制御されます。 また、より詳細なアクセス制御には Azure AD Privileged Identity Management を使用することをお勧めします。

### <a name="high-availability"></a>高可用性

Azure VPN ゲートウェイでは、複数の接続を使用して、同じオンプレミス環境に対して複数のオンプレミス VPN デバイスをサポートできます。 図 1 を参照してください。

Azure の仮想ネットワークには、独立構成、アクティブ/パッシブ構成、またはアクティブ/アクティブ構成でデプロイできる複数の VPN ゲートウェイを含めることができます。

[高可用性構成](../vpn-gateway/vpn-gateway-highlyavailable.md)ですべての VPN ゲートウェイをデプロイすることをお勧めします。 例として、アクティブ/パッシブ モードまたはアクティブ/アクティブ モードの 2 つの VPN ゲートウェイに接続された 2 つのオンプレミス VPN デバイスがあります。 図 2 を参照してください。

![VPN ゲートウェイの冗長接続](media/dual-redundancy.png)

*図 2 – アクティブ/アクティブ VPN ゲートウェイと 2 つの VPN デバイス*

### <a name="forced-tunneling"></a>強制トンネリング

強制トンネリングでは、検査および監査のために、インターネットへのすべてのトラフィックを VPN ゲートウェイ経由でオンプレミス環境にリダイレクトします (つまり、強制的に戻します)。 強制トンネリングを使用しない場合、Azure の VM からインターネットへのトラフィックは、トラフィックを検査または監査できるオプションを使用せずに、Azure ネットワーク インフラストラクチャからパブリック インターネットへ直接トラバースします。 強制トンネリングは、組織で環境に Secure Internet Gateway (SIG) を使用する必要がある場合に重要です。

## <a name="detailed-configuration"></a>詳細な構成

### <a name="service-attributes"></a>サービス属性

オーストラリア政府向けに構成された S2S 接続用の VPN ゲートウェイには、次の属性が必要です。

|属性 | 必須|
|--- | --- |
|gatewayType | "VPN"|
|

PROTECTED のために ISM 統制に準拠するのに必要な属性設定は、次のとおりです。

|属性 | 必須|
|--- |---|
|vpnType |"RouteBased"|
|vpnClientConfiguration/vpnClientProtocols | "IkeV2"|
|

Azure VPN ゲートウェイでは、IPsec および IKE プロトコル標準のさまざまな暗号アルゴリズムがサポートされています。 既定のポリシーでは、さまざまなサードパーティ VPN デバイスとの相互運用性が最大に設定されます。 その結果、IKE ハンドシェイク中に非準拠の構成がネゴシエートされる可能性があります。 [カスタムの IPsec/IKE ポリシー](../vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md) パラメーターを VPN ゲートウェイの vpnClientConfiguration に適用し、接続が、Azure へのオンプレミス環境接続の ISM 統制を満たすようにすることを強くお勧めします。 主な属性を次の表に示します。

|属性|推奨|必須|
|---|---|---|
|saLifeTimeSeconds|< 14,400 秒|> 300 秒|
|saDataSizeKilobytes| |> 1,024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14、DHGroup24、ECP256、ECP384|DHGroup2|
|pfsGroup|PFS2048、PFS24、ECP256、ECP384||
|

前の表の dhGroup と pfsGroup では、他の設定を使用できる場合でも、ECP256 と ECP384 が優先されます。

### <a name="related-services"></a>関連サービス

Azure VPN ゲートウェイを設計して構成する場合は、いくつかの関連サービスが存在し、それらを構成する必要もあります。

|サービス | 必要な操作|
|--- | ---|
|仮想ネットワーク | VPN ゲートウェイは仮想ネットワークに接続されます。 新しい VPN ゲートウェイを作成する前に、仮想ネットワークを作成します。|
|パブリック IP アドレス | S2S VPN ゲートウェイでは、オンプレミスの VPN デバイスと VPN ゲートウェイ間の接続を確立するためのパブリック IP アドレスが必要です。 S2S VPN ゲートウェイを作成する前に、パブリック IP アドレスを作成します。|
|Subnet | VPN ゲートウェイ用の仮想ネットワークのサブネットを作成します。|
|

## <a name="implementation-steps-using-powershell"></a>PowerShell を使用した実装手順

### <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

1. カスタム ロールを作成します。 たとえば、virtualNetworkGateway 共同作成者です。 VPN ゲートウェイの作成と変更が許可されるユーザーに割り当てられるロールを作成します。 カスタム ロールでは、次の操作を許可する必要があります。

   Microsoft.Network/virtualNetworkGateways/*  
   Microsoft.Network/connections/*  
   Microsoft.Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft.Network/publicIPAddresses/*  
   Microsoft.Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. VPN ゲートウェイとオンプレミス環境への接続を作成および管理することが許可されているユーザーに、カスタム ロールを追加します。

### <a name="create-a-vpn-gateway"></a>VPN ゲートウェイの作成

これらの手順は、仮想ネットワークが既に作成されていることを前提としています。

1. 新しいパブリック IP アドレスを作成します。
2. VPN ゲートウェイのサブネットを作成します。
3. VPN ゲートウェイの IP 構成ファイルを作成します。
4. VPN ゲートウェイを作成します。
5. オンプレミス VPN デバイス用のローカル ネットワーク ゲートウェイを作成します。
6. IPsec ポリシーを作成します。 この手順は、カスタム IPsec/IKE ポリシーを使用していることを前提としています。
7. IPsec ポリシーを使用して、VPN ゲートウェイとローカル ネットワーク ゲートウェイの間に接続を作成します。

### <a name="enforce-tunneling"></a>トンネリングを強制する

強制トンネリングが必要な場合は、VPN ゲートウェイを作成する前に、次のようにします。

1. ルート テーブルとルート規則を作成します。
2. ルート テーブルを適切なサブネットに関連付けます。

VPN ゲートウェイを作成した後、次のようにします。

- GatewayDefaultSite を VPN ゲートウェイのオンプレミス環境に設定します。

### <a name="example-powershell-script"></a>PowerShell サンプル スクリプト

カスタム IPsec/IKE ポリシーを作成するために使用される PowerShell スクリプトの例では、オーストラリアの PROTECTED セキュリティ分類のための ISM 統制に準拠します。

仮想ネットワーク、VPN ゲートウェイ、およびローカル ゲートウェイが存在することを前提としています。

#### <a name="create-an-ipsecike-policy"></a>IPsec/IKE ポリシーの作成

次のサンプル スクリプトは、次のアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーを作成します。

- IKEv2: AES256、SHA256、DHGroup ECP256
- IPsec: AES256、SHA256、PFS ECP256、SA の有効期間 14,400 秒、102,400,000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>カスタム IPsec/IKE ポリシーを使用して S2S VPN 接続を作成する

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>次のステップ

この記事では、オーストラリア政府の PROTECTED データを送信中にセキュリティ保護するために、Information Security Manual に指定されている要件を満たすための VPN Gateway の具体的な構成について説明しました。 VPN ゲートウェイを構成する手順については、以下を参照してください。

- [Azure Virtual Network Gateway の概要](../vpn-gateway/index.yml)  
- [VPN ゲートウェイとは](../vpn-gateway/vpn-gateway-about-vpngateways.md)  
- [PowerShell を使用したサイト間 VPN 接続を備えた仮想ネットワークの作成](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)  
- [VPN ゲートウェイを作成して管理する](../vpn-gateway/tutorial-create-gateway-portal.md)
