---
title: 'Azure VPN Gateway で OpenVPN を構成する方法: PowerShell | Microsoft Docs'
description: Azure VPN Gateway 用に OpenVPN を構成する手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: b915a56b14332bfa885eaccf9a151d08c58dc5b1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973696"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Azure ポイント対サイト VPN Gateway 用に OpenVPN を構成する (プレビュー)

この記事では、Azure VPN Gateway で OpenVPN を構成する方法について説明します。 この記事では、稼働中のポイント対サイト環境が既にあることを前提としています。 ない場合は、手順 1 の指示に従ってポイント対サイト VPN を作成します。

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="vnet"></a>1.ポイント対サイト VPN を作成する

機能しているポイント対サイト環境がまだない場合は、指示に従って作成します。 ネイティブの Azure 証明機関を使用してポイント対サイト VPN ゲートウェイを作成および構成する方法については、[ポイント対サイト VPN の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関するページを参照してください。

## <a name="cmdlets"></a>2.PowerShell コマンドレットのインストール

Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 PowerShell コマンドレットのインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。 この点は重要です。以前のバージョンのコマンドレットには、この演習に必要な最新の値が含まれていません。

## <a name="enable"></a>3.ゲートウェイで OpenVPN を有効にする

ゲートウェイで OpenVPN を有効にします。 ゲートウェイがポイント対サイト (IKEv2 または SSTP) 用に既に構成されていることを確認してから、次のコマンドを実行してください。

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>次の手順

OpenVPN 用にクライアントを構成する方法については、[OpenVPN クライアントの構成](vpn-gateway-howto-openvpn-clients.md)に関するページを参照してください。