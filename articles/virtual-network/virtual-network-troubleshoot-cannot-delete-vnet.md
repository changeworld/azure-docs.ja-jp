---
title: Azure で仮想ネットワークを削除できない | Microsoft Docs
description: Azure で仮想ネットワークを削除できない問題を解決する方法を説明します。
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c2152b872c82c224c786e56db0318c9df994ac25
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801588"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>トラブルシューティング:Azure で仮想ネットワークを削除できない
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=a13a0908-28e0-f9f7-c6c7-301fcd187560" target='_blank'>開始</a></span><span class="has-padding-small">仮想エージェントを利用して <b> 自動化診断を実行し、問題を短時間で解決します。</b></span><span class="has-padding-small"><a href="https://privacy.microsoft.com/privacystatement" target='_blank'><div align="right"><sub>プライバシーに関する声明</sub></div></a></span></p>
Microsoft Azure で仮想ネットワークを削除しようとすると、エラーが表示されることがあります。 この記事では、この問題の解決に役立つトラブルシューティング手順について説明します。 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>トラブルシューティング ガイダンス 

1. [仮想ネットワークで仮想ネットワーク ゲートウェイが実行されていないか確認する](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network)
2. [仮想ネットワークでアプリケーション ゲートウェイが実行されていないか確認する](#check-whether-an-application-gateway-is-running-in-the-virtual-network)
3. [仮想ネットワークで Azure Active Directory ドメイン サービスが有効になっていないか確認する](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network)
4. [仮想ネットワークが他のリソースに接続されていないか確認する](#check-whether-the-virtual-network-is-connected-to-other-resource)
5. [仮想ネットワークに実行中の仮想マシンがないか確認する](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network)
6. [仮想ネットワークが移行の途中で停止していないか確認する](#check-whether-the-virtual-network-is-stuck-in-migration)

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>仮想ネットワークで仮想ネットワーク ゲートウェイが実行されていないか確認する

仮想ネットワークを削除するには、まず仮想ネットワーク ゲートウェイを削除する必要があります。

従来の仮想ネットワークの場合は、Azure Portal で従来の仮想ネットワークの **[概要]** ページに移動します。 仮想ネットワークでゲートウェイが実行中の場合は、 **[VPN 接続]** セクションにゲートウェイの IP アドレスが表示されます。 

![ゲートウェイが実行されていないか確認する](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

仮想ネットワークの場合、仮想ネットワークの **[概要]** ページに移動します。 **[接続デバイス]** に仮想ネットワーク ゲートウェイがあるかどうかを確認します。

![接続デバイスの確認](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

ゲートウェイを削除する前に、まずゲートウェイの**接続**オブジェクトをすべて削除する必要があります。 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>仮想ネットワークでアプリケーション ゲートウェイが実行されていないか確認する

仮想ネットワークの **[概要]** ページに移動します。 **[接続デバイス]** にアプリケーション ゲートウェイがあるかどうかを確認します。

![接続デバイスの確認](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

アプリケーション ゲートウェイがある場合は、仮想ネットワークを削除する間に削除する必要があります。

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>仮想ネットワークで Azure Active Directory ドメイン サービスが有効になっていないか確認する

Azure Active Directory ドメイン サービスが有効になっていて仮想ネットワークに接続されている場合、この仮想ネットワークを削除することはできません。 

![接続デバイスの確認](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

サービスを無効にするには、「[Azure Portal を使用して Azure Active Directory Domain Services を無効にする](../active-directory-domain-services/delete-aadds.md)」をご覧ください。

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>仮想ネットワークが他のリソースに接続されていないか確認する

サーキット リンク、接続、および仮想ネットワークのピアリングを確認します。 これらは、仮想ネットワークを削除できない要因となることがあります。 

次の順序で削除することをお勧めします。

1. ゲートウェイ接続
2. ゲートウェイ
3. IP
4. 仮想ネットワークのピアリング
5. App Service Environment (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>仮想ネットワークに実行中の仮想マシンがないか確認する

仮想ネットワークに仮想マシンがないことを確認します。

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>仮想ネットワークが移行の途中で停止していないか確認する

仮想ネットワークが移行の途中で停止していると、削除できません。 次のコマンドを実行して移行を中止してから、仮想ネットワークを削除します。

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>次のステップ

- [Azure Virtual Network](virtual-networks-overview.md)
- [Azure 仮想ネットワークについてよく寄せられる質問 (FAQ)](virtual-networks-faq.md)
