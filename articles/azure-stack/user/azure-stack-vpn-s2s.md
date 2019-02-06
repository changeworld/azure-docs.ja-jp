---
title: Azure Stack のサイト対サイト VPN 接続を構成する | Microsoft Docs
description: Azure Stack でのサイト対サイト VPN 接続または VNet 対 VNet 接続用の IPsec/IKE ポリシーについて説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 9a14c819acbc46d8a281f73fd4f3185caa8a1e02
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478153"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>サイト間 VPN 接続の IPsec/IKE ポリシーを構成する

この記事では、Azure Stack でサイト対サイト (S2S) VPN 接続用の IPsec/IKE ポリシーを構成する手順について説明します。

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>VPN ゲートウェイ用の IPsec/IKE ポリシーのパラメーター

IPsec/IKE 標準プロトコルでは、幅広い暗号アルゴリズムがさまざまな組み合わせでサポートされています。 Azure Stack でサポートされているパラメーターを確認するには、ご自身のコンプライアンスまたはセキュリティ要件を満たすのに役立つ「 [IPsec/IKE パラメーター](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)」を参照してください。

この記事では、IPsec/IKE ポリシーを作成して構成し、新規または既存の接続に適用する方法を示します。

## <a name="considerations"></a>考慮事項

ポリシーを使用する際は、次の重要な考慮事項に注意してください。

- IPsec/IKE ポリシーは、*Standard*  および  *HighPerformance*  (ルートベース) ゲートウェイ SKU でのみ機能します。

- 特定の接続に対して指定できるポリシーの組み合わせは  **1 つ** だけです。

- IKE (メイン モード) と IPsec (クイック モード) の両方について、すべてのアルゴリズムとパラメーターを指定する必要があります。 ポリシーを部分的に指定することはできません。

- オンプレミスの VPN デバイスでポリシーがサポートされることを、VPN デバイス ベンダーの仕様で確認してください。 ポリシーに対応していない場合、サイト対サイト接続を確立することはできません。

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>パート 1 - IPsec/IKE ポリシーを作成して設定するためのワークフロー

このセクションでは、サイト対サイト VPN 接続に関する IPsec/IKE ポリシーの作成と更新を行うために必要なワークフローについて説明します。

1. 仮想ネットワークと VPN ゲートウェイを作成します。

2. クロスプレミス接続用のローカル ネットワーク ゲートウェイを作成します。

3. 選択したアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーを作成します。

4. IPsec/IKE ポリシーを使用する IPSec 接続を作成します。

5. 既存の接続に対して、IPsec/IKE ポリシーを追加/更新/削除します。

この記事では、次の図に示す IPsec/IKE ポリシーを設定して構成します。

![IPsec/IKE ポリシーを設定して構成する](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>パート 2: サポートされる暗号アルゴリズムとキーの強度

以下の表は、サポートされている暗号アルゴリズムと、Azure Stack のお客様が構成できるキーの強度を一覧にしたものです。

| IPsec/IKEv2                                          | オプション                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2 暗号化                                     | AES256、AES192、AES128、DES3、DES                                        |
| IKEv2 整合性                                      | SHA384、SHA256、SHA1、MD5                                                |
| DH グループ                                             | ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、なし         |
| IPsec 暗号化                                     | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、なし |
| IPsec 整合性                                      | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                       |
| PFS グループ                                            | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、なし                         |
| QM SA の有効期間                                       | (オプション: 指定されていない場合、既定値が使用されます)<br />                         秒 (整数: 最小 300/既定値 27,000 秒)<br />                         キロバイト数 (整数: 最小 1,024/既定値 102,400,000 キロバイト) |                                                                          |
| トラフィック セレクター                                     | Azure Stack ではポリシー ベースのトラフィック セレクターはサポートされていません。         |

- ご使用のオンプレミス VPN デバイスの構成は、Azure IPsec/IKE ポリシーで指定した次のアルゴリズムおよびパラメーターと一致している (または含んでいる) 必要があります。

  - IKE 暗号化アルゴリズム (メイン モード/フェーズ 1)
  - IKE 整合性アルゴリズム (メイン モード/フェーズ 1)
  - DH グループ (メイン モード/フェーズ 1)
  - IPsec 暗号化アルゴリズム (クイック モード/フェーズ 2)
  - IPsec 整合性アルゴリズム (クイック モード/フェーズ 2)
  - PFS グループ (クイック モード/フェーズ 2)
  - SA の有効期間は、ローカルの指定のみとなります。一致している必要はありません。

- IPsec 暗号化アルゴリズム用として GCMAES を使用する場合は、IPsec との整合性を取るため、同じ GCMAES アルゴリズムとキーの長さを選択する必要があります。たとえば、両方に GCMAES128 を使用します。

- 上記の表では、

  - IKEv2 はメイン モードまたはフェーズ 1 に対応しています
  - IPsec はクイック モードまたはフェーズ 2 に対応しています
  - DH グループは、メイン モードまたはフェーズ 1 で使用される Diffie-Hellman グループを指定します
  - PFS グループは、クイック モードまたはフェーズ 2 で使用される Diffie-Hellman グループを指定します

- Azure Stack VPN ゲートウェイでは、IKEv2 メイン モード SA の有効期間は 28,800 秒に固定されています。

以下の表は、カスタム ポリシーでサポートされている、対応する Diffie-Hellman グループを示したものです。

| Diffie-hellman グループ | DHGroup   | PFSGroup      | キーの長さ    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768 ビット MODP  |
| 2                    | DHGroup2  | PFS2          | 1024 ビット MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048 ビット MODP |
| 19                   | ECP256    | ECP256        | 256 ビット ECP   |
| 20                   | ECP384    | ECP384        | 384 ビット ECP   |
| 24                   | DHGroup24 | PFS24         | 2048 ビット MODP |

詳細については、 [RFC3526](https://tools.ietf.org/html/rfc3526)  と  [RFC5114](https://tools.ietf.org/html/rfc5114) を参照してください。

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>パート 3 - IPsec/IKE ポリシーを使用する新しいサイト対サイト VPN 接続を作成する

このセクションでは、IPsec/IKE ポリシーを使用するサイト対サイト VPN 接続を作成する手順について説明します。 以下の手順によって、次の図に示す接続が作成されます。

![サイト対サイト ポリシー](media/azure-stack-vpn-s2s/site-to-site.png)

サイト対サイト VPN 接続を作成するための詳細な手順については、 [サイト対サイト VPN 接続の作成](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)に関する記事をご覧ください。

### <a name="prerequisites"></a>前提条件

開始する前に、以下の前提条件を確認してください。

- Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、 [MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

- Azure Resource Manager PowerShell コマンドレット。 PowerShell コマンドレットのインストールの詳細については、「 [PowerShell for Azure Stack をインストールする](../azure-stack-powershell-install.md) 」を参照してください。

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>手順1 - 仮想ネットワーク、VPN ゲートウェイ、およびローカル ネットワーク ゲートウェイを作成する

#### <a name="1-declare-variables"></a>1.変数の宣言

この練習では、次の変数の宣言から始めます。 運用環境用に構成する場合は、プレースホルダーを実際の値に必ず置き換えてください。

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.サブスクリプションに接続して新しいリソース グループを作成する

リソース マネージャー コマンドレットを使用するように PowerShell モードを切り替えてください。 手順については、「 [ユーザーとして PowerShell を使用して Azure Stack に接続する](azure-stack-powershell-configure-user.md)」をご覧ください。

PowerShell コンソールを開き、アカウントに接続します。 接続するには、次のサンプルを参照してください。

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>手順 3.仮想ネットワーク、VPN ゲートウェイ、およびローカル ネットワーク ゲートウェイを作成する

次の例では、3 つのサブネットを持つ仮想ネットワーク **TestVNet1** と VPN ゲートウェイが作成されます。 値を代入するときは、ゲートウェイ サブネットの名前を必ず **GatewaySubnet** にすることが重要です。 別の名前にすると、ゲートウェイの作成は失敗します。

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>手順 2 - IPsec/IKE ポリシーを使用するサイト対サイト VPNを作成する

#### <a name="1-create-an-ipsecike-policy"></a>1.IPsec/IKE ポリシーの作成

このサンプル スクリプトでは、次のアルゴリズムとパラメーターを使用する IPsec/IKE ポリシーが作成されます。

- IKEv2:AES128、SHA1、DHGroup14
- IPsec:AES256、SHA256、なし、SA の有効期間 14,400 秒および 102,400,000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

IPsec に GCMAES を使用する場合、IPsec 暗号化と整合性の両方で同じ GCMAES アルゴリズムとキーの長さを使用する必要があります。

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2.この IPsec/IKE ポリシーを使用するサイト対サイト VPN を作成する

サイト対サイト VPN 接続を作成し、先ほど作成した IPsec/IKE ポリシーを適用します。

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> 接続に IPsec/IKE ポリシーを指定すると、Azure VPN ゲートウェイは、その特定の接続に対して指定された暗号アルゴリズムとキーの強度を使用する IPsec/IKE 提案のみを送受信します。 接続用のオンプレミスの VPN デバイスで、ポリシーの完全な組み合わせを使用できるか受け入れられることを確認してください。それ以外の場合、サイト対サイト VPN トンネルは確立されません。

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>パート 4 - 接続の IPsec/IKE ポリシーを更新する

前のセクションでは、既存のサイト対サイト接続用の IPsec/IKE ポリシーを管理する方法を示しました。 このセクションでは、接続に対して次の操作を行います。

1. 接続の IPsec/IKE ポリシーを表示する
2. 接続に IPsec/IKE ポリシーを追加する、またはポリシーを更新する
3. 接続から IPsec/IKE ポリシーを削除する

> [!NOTE]
> IPsec/IKE ポリシーは、ルートベースの VPN ゲートウェイである  *Standard*  および  *HighPerformance*  のみでサポートされています。 *Basic* ゲートウェイ SKU では機能しません。

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1.接続の IPsec/IKE ポリシーを表示する

次の例は、接続に構成されている IPsec/IKE ポリシーを取得する方法を示しています。 これらのスクリプトは、前の手順の続きでもあります。

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最後のコマンドによって、接続に構成されている現在の IPsec/IKE ポリシーが表示されます (存在する場合)。 次の例は、接続の出力サンプルです。

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

IPsec/IKE ポリシーが構成されていない場合、`$connection6.policy` コマンドは何も返しません。 これは、接続に IPsec/IKE が構成されていないという意味ではなく、カスタム IPsec/IKE ポリシーがないことを意味します。 実際の接続では、オンプレミスの VPN デバイスと Azure VPN ゲートウェイの間でネゴシエートされる既定のポリシーが使用されます。

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2.IPsec/IKE ポリシーを接続に追加するか、ポリシーを更新する

接続に新しいポリシーを追加する手順と既存のポリシーを更新する手順は同じです。つまり、新しいポリシーを作成した後、新しいポリシーを接続に適用します。

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

接続をもう一度取得して、ポリシーが更新されていることを確認できます。

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最後の行によって、次の例のような出力が表示されます。

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>手順 3.接続から IPsec/IKE ポリシーを削除する

接続からカスタム ポリシーを削除すると、Azure VPN ゲートウェイは [既定の IPsec/IKE 提案](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)に戻り、オンプレミスの VPN デバイスとの再ネゴシエーションが実行されます。

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

同じスクリプトを使用して、接続からポリシーが削除されたかどうかを確認できます。

## <a name="next-steps"></a>次の手順

- [Azure Stack の VPN ゲートウェイ構成設定](azure-stack-vpn-gateway-settings.md)
