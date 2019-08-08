---
title: Azure Australia の Azure VPN Gateway
description: Azure Australia に VPN Gateway を実装して ISM に準拠するようにし、オーストラリア政府機関を効果的に保護する
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571239"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure Australia の Azure VPN Gateway

パブリック クラウドにおける重要なサービスは、クラウド リソースおよびサービスから既存のオンプレミス システムへのセキュリティで保護された接続です。  Azure でこの機能を提供するサービスは、Azure VPN Gateway (VPN Gateway) です。 この記事では、VPN Gateway を構成する際に、オーストラリア通信電子局 (ASD) の [Information Security Manual Controls](https://acsc.gov.au/infosec/ism/) (ISM) に準拠するための主要な考慮事項について概説します。

VPN Gateway は、Azure 内の仮想ネットワークと別のネットワークとの間で暗号化されたトラフィックを送信するために使用されます。  VPN ゲートウェイでは、次の 3 つのシナリオに対応しています。

- **サイト間** (S2S)
- **ポイント対サイト** (P2S)
- **VNet 間**

この記事では、S2S VPN ゲートウェイに焦点を当てます。 図 1 は、サイト間 VPN ゲートウェイの構成例を示しています。

![複数サイト接続を使用した VPN Gateway](media/vpngateway-multisite-connection-diagram.png)

*図 1 - Azure サイト間 VPN Gateway*

## <a name="key-design-considerations"></a>設計上の主な考慮事項

オーストラリア政府のお客様に Azure を接続するには、次の 3 つのネットワーク オプションがあります。

- **ICON**
- **ExpressRoute**
- **パブリック インターネット**

Australian Cyber Security Centre の『[Azure のコンシューマー ガイド](https://servicetrust.microsoft.com/viewpage/Australia)』では、接続が暗号化と整合性についての ISM 統制に準拠するようにするために、VPN Gateway (または同等の保護された認定サードパーティ サービス) を 3 つのネットワーク オプションと組み合わせて使用することが推奨されています。

### <a name="encryption-and-integrity"></a>暗号化と整合性

既定では、VPN は IKE ハンドシェイクの一部として接続の確立中に、暗号化と整合性のアルゴリズムとパラメーターをネゴシエートします。  IKE ハンドシェイク中、VPN Gateway がイニシエーターまたはレスポンダーのどちらであるかによって、構成と優先度が決まります (これは VPN デバイスを介して制御されます)。  接続の最終的な構成は、VPN デバイスの構成によって制御されます。  検証された VPN デバイスとその構成の詳細については、[VPN デバイスの概要](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)に関するページを参照してください。

VPN ゲートウェイは、接続のカスタム IPsec/IKE ポリシーを構成することによって、暗号化と整合性を制御できます。

### <a name="resource-operations"></a>リソース操作

VPN Gateway は、パブリック インターネット経由で Azure と Azure 以外の環境の間の接続を作成します。  ISM には、接続の明示的な承認に関連する統制があります。  既定では、VPN ゲートウェイを使用して、セキュリティで保護された環境に承認されていないトンネルを作成することができます。  そのため、組織は Azure ロール ベースのアクセス制御 (RBAC) を使用して、VPN Gateway とその接続を作成および変更できるユーザーを制御することが重要です。  Azure には VPN Gateway を管理するための "組み込み" ロールはありません。そのため、カスタム ロールが必要になります。

"所有者"、"共同作成者"、"ネットワーク共同作成者" の各ロールへのアクセスは厳密に制御されています。  また、より詳細なアクセス制御には Azure AD Privileged Identity Management を使用することをお勧めします。

### <a name="high-availability"></a>高可用性

Azure VPN Gateway では、複数の接続 (図1を参照) を使用して、同じオンプレミス環境に対して複数のオンプレミス VPN デバイスをサポートできます。  

Azure の仮想ネットワークには、独立構成、アクティブ/パッシブ構成、またはアクティブ/アクティブ構成でデプロイできる複数の VPN Gateway を含めることができます。

すべての VPN ゲートウェイを[高可用性構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)で展開することをお勧めします。たとえば、2 つのオンプレミス VPN デバイスを、アクティブ/パッシブ モードまたはアクティブ/アクティブ モードのいずれかで 2 つの VPN ゲートウェイに接続します (図 2 を参照)。

![VPN Gateway の冗長接続](media/dual-redundancy.png)

*図 2 - アクティブ/アクティブ VPN ゲートウェイと 2 つの VPN デバイス*

### <a name="forced-tunneling"></a>強制トンネリング

強制トンネリングは、検査および監査の目的で、インターネットへのすべてのトラフィックを VPN Gateway 経由でオンプレミスの環境に "強制的に" リダイレクトします。 強制トンネリングを使用しない場合、Azure の VM からインターネットへのトラフィックは、トラフィックを検査または監査できるオプションを使用せずに、Azure ネットワーク インフラストラクチャからパブリック インターネットへ直接トラバースします。  これは、組織が環境に Secure Internet Gateway (SIG) を使用する必要がある場合に重要です。

## <a name="detailed-configuration"></a>詳細な構成

### <a name="service-attributes"></a>サービス属性

オーストラリア政府向けに構成されたサイト間接続用の VPN ゲートウェイには、次の属性が必要です。

|Attribute | 必須|
|--- | --- |
|gatewayType | "VPN"|
|

ISM 統制に準拠して保護するために必要な属性設定は、次のとおりです。

|Attribute | 必須|
|--- |---|
|vpnType |"RouteBased"|
|vpnClientConfiguration/vpnClientProtocols | "IkeV2"|
|

Azure VPN Gateway では、IPsec および IKE 標準プロトコルを使用した幅広い暗号アルゴリズムがサポートされています。  既定のポリシー セットでは、幅広いサード パーティ製 VPN デバイスとの相互運用性が最大化できます。  その結果、IKE ハンドシェイク中に非対応の構成がネゴシエートされる可能性があります。  したがって、[カスタムの IPsec/IKE ポリシー](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) パラメーターを VPN Gateway の vpnClientConfiguration に適用して、接続が Azure へのオンプレミス環境接続の ISM 統制に対応するようにすることを強くお勧めします。  主な属性は次のとおりです。

|Attribute|推奨|必須|
|---|---|---|
|saLifeTimeSeconds|< 14400 秒|> 300 秒|
|saDataSizeKilobytes| |> 1024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14、DHGroup24、ECP256、ECP384|DHGroup2|
|pfsGroup|PFS2048、PFS24、ECP256、ECP384||
|

*上記の表の dhGroup と pfsGroup では、他の設定を使用できる場合でも、ECP256 と ECP384 が優先されます。*

### <a name="related-services"></a>関連サービス

Azure VPN Gateway の設計と構成を行う場合、いくつかの関連サービスも存在している必要があり、かつ構成する必要があります。

|Service | 必要な操作|
|--- | ---|
|Virtual Network | VPN Gateway は仮想ネットワークに接続されます。  新しい VPN Gateway を作成する前に、仮想ネットワークを作成する必要があります。|
|パブリック IP アドレス | S2S VPN Gateway は、オンプレミスの VPN デバイスと VPN Gateway 間の接続を確立するためのパブリック IP アドレスを必要とします。  S2S VPN Gateway を作成する前に、パブリック IP アドレスを作成する必要があります。|
|Subnet | VPN Gateway には、仮想ネットワークのサブネットを作成する必要があります。|
|

## <a name="implementation-steps-using-powershell"></a>PowerShell を使用した実装手順

### <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)

1. カスタム ロール (たとえば、virtualNetworkGateway 共同作成者) を作成します。  VPN Gateway の作成と変更が許可されるユーザーに割り当てられるロールを作成します。 カスタム ロールでは、次の操作を許可する必要があります。

   Microsoft.Network/virtualNetworkGateways/*  
   Microsoft.Network/connections/*  
   Microsoft.Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft.Network/publicIPAddresses/*  
   Microsoft.Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. VPN Gateway とオンプレミス環境への接続を作成および管理することが許可されているユーザーに、カスタム ロールを追加します。

### <a name="create-vpn-gateway"></a>VPN Gateway を作成する

*これらの手順は、仮想ネットワークが既に作成されていることを前提としています*

1. 新しいパブリック IP アドレスを作成する
2. VPN Gateway サブネットを作成する
3. VPN Gateway IP 構成を作成する
4. VPN Gateway を作成する
5. オンプレミス VPN デバイス用のローカル ネットワーク ゲートウェイを作成する
6. IPsec ポリシーを作成する (カスタム IPsec/IKE ポリシーの使用が前提)
7. IPsec ポリシーを使用して VPN Gateway とローカル ネットワーク ゲートウェイの間に接続を作成する

### <a name="enforce-tunneling"></a>トンネリングを強制する

強制トンネリングが必要な場合は、VPN Gateway を作成する前に、次の手順を実行します。

1. ルート テーブルおよびルート規則を作成する
2. ルートテーブルを適切なサブネットに関連付ける

VPN Gateway を作成した後、次の手順を実行します。

1. GatewayDefaultSite を VPN Gateway のオンプレミス環境に設定する

### <a name="example-powershell-script"></a>PowerShell サンプル スクリプト

オーストラリアの PROTECTED セキュリティ分類のための ISM 統制に準拠するカスタム IPSEC/IKE ポリシーを作成するための PowerShell スクリプトの例を示します。

仮想ネットワーク、VPN Gateway、およびローカル ゲートウェイが存在することを前提としています。

#### <a name="create-an-ipsecike-policy"></a>IPsec/IKE ポリシーの作成

次のサンプル スクリプトは、次のアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーを作成します。

- IKEv2:AES256、SHA256、DHGroup ECP256
- IPsec:AES256、SHA256、PFS ECP256、SA の有効期間 14,400 秒および 102400000 KB

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

## <a name="next-steps"></a>次の手順

この記事では、オーストラリア政府の PROTECTED データを送信中にセキュリティ保護するために、Information Security Manual (ISM) に指定されている要件を満たすための VPN Gateway の具体的な構成について説明しました。 VPN Gateway を構成するための詳細な手順については、次のとおりです。

- [Azure Virtual Network Gateway の概要](https://docs.microsoft.com/azure/vpn-gateway/)  
- [VPN Gateway とは](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [PowerShell を使用してサイト間 VPN 接続を持つ VNet を作成する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [VPN ゲートウェイを作成して管理する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)