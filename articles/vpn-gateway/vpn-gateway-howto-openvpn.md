---
title: Azure VPN Gateway で OpenVPN を構成する方法:PowerShell | Microsoft Docs
description: Azure VPN Gateway 用に OpenVPN を構成する手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 50a8c30831ba806d0ea02d4f67b4e672e71e6325
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415087"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Azure ポイント対サイト VPN Gateway 用に OpenVPN を構成する (プレビュー)

この記事では、Azure VPN Gateway で OpenVPN を構成する方法について説明します。 この記事では、稼働中のポイント対サイト環境が既にあることを前提としています。 ない場合は、手順 1 の指示に従ってポイント対サイト VPN を作成します。

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>この機能を登録する

Azure Cloud Shell を使用してこの機能を簡単に登録するには、この手順で **[TryIt]** をクリックします。

>[!NOTE]
>この機能を登録しないと、それを使用できなくなります。
>

**[TryIt]** をクリックした後、Azure Cloud Shell を開くには、次のコマンドをコピーして貼り付けます。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

機能が登録済みと表示されたら、サブスクリプションを Microsoft.Network 名前空間に再登録します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1.ポイント対サイト VPN を作成する

機能しているポイント対サイト環境がまだない場合は、指示に従って作成します。 ネイティブの Azure 証明機関を使用してポイント対サイト VPN ゲートウェイを作成および構成する方法については、[ポイント対サイト VPN の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関するページを参照してください。 

> [!IMPORTANT]
> Basic SKU は OpenVPN ではサポートされていません。

## <a name="cmdlets"></a>2.PowerShell コマンドレットのインストール

Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 PowerShell コマンドレットのインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。 この点は重要です。以前のバージョンのコマンドレットには、この演習に必要な最新の値が含まれていません。

## <a name="enable"></a>3.ゲートウェイで OpenVPN を有効にする

ゲートウェイで OpenVPN を有効にします。 ゲートウェイがポイント対サイト (IKEv2 または SSTP) 用に既に構成されていることを確認してから、次のコマンドを実行してください。

```powershell
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>次の手順

OpenVPN 用にクライアントを構成する方法については、[OpenVPN クライアントの構成](vpn-gateway-howto-openvpn-clients.md)に関するページを参照してください。
