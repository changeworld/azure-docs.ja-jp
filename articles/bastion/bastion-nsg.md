---
title: Azure Bastion で VM と NSG を使用する | Microsoft Docs
description: この記事では、Azure Bastion に NSG アクセスを組み込む方法を説明します
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: 4b64c420290bec687388690f1c901c2ae789a3c8
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707566"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>NSG アクセスと Azure Bastion を使用する

Azure Bastion の使用時にネットワーク セキュリティ グループ (NSG) を使用できます。 詳細については、「[セキュリティ グループ](../virtual-network/security-overview.md)」を参照してください。 

![アーキテクチャ](./media/bastion-nsg/nsg-architecture.png)

この図の内容は次のとおりです。

* Bastion ホストは仮想ネットワークにデプロイされています。
* ユーザーは任意の HTML5 ブラウザーを使用して Azure portal に接続します。
* ユーザーは RDP/SSH で接続するために Azure 仮想マシンに移動します。
* 接続統合 - ブラウザー内でのシングルクリックによる RDP/SSH セッション
* Azure VM ではパブリック IP が必要ありません。

## <a name="nsg"></a>ネットワーク セキュリティ グループ

このセクションでは、ユーザーと Azure Bastion との間のネットワーク トラフィックと、仮想ネットワーク内のターゲット VM へのネットワークトラフィックについて説明します。

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure Bastion は、AzureBastionSubnet に特定してデプロイされます。

* **イグレス トラフィック:**

   * **パブリック インターネットからのイグレス トラフィック:** Azure Bastion によってパブリック IP が作成されます。このパブリック IP では、イグレス トラフィック用にポート 443 が有効になっている必要があります。 AzureBastionSubnet でポート 3389/22 が開かれている必要はありません。
   * **Azure Bastion からのイグレス トラフィックのコントロール プレーン:** コントロール プレーン接続の場合は、**GatewayManager** サービス タグからのポート 443 受信を有効にします。 これにより、コントロール プレーン、つまりゲートウェイ マネージャーから Azure Bastion への通信が可能になります。

* **エグレス トラフィック:**

   * **ターゲット VM へのエグレス トラフィック:** Azure Bastion は、プライベート IP 経由でターゲット VM にリーチします。 NSG では、他のターゲット VM サブネットへのエグレス トラフィックをポート 3389 と 22 に許可する必要があります。
   * **Azure の他のパブリックエンド ポイントへのエグレス トラフィック:** Azure Bastion から Azure 内のさまざまなパブリック エンドポイントに接続できる必要があります (たとえば、診断ログや測定ログを格納するため)。 このため、Azure Bastion には **AzureCloud** サービス タグに対する 443 への送信が必要です。

* **ターゲット VM サブネット:** これは、RDP/SSH で接続するターゲット仮想マシンを含むサブネットです。

   * **Azure Bastion からのイグレス トラフィック:** Azure Bastion は、プライベート IP 経由でターゲット VM にリーチします。 プライベート IP 経由のターゲット VM 側で RDP/SSH ポート (それぞれポート 3389/22) が開かれている必要があります。 ベスト プラクティスとして、この規則に Azure Bastion サブネットの IP アドレス範囲を追加して、ターゲット VM サブネット内のターゲット VM で Bastion によってのみこれらのポートが開かれるよにすることができます。

## <a name="apply"></a>AzureBastionSubnet への NSG の適用

NSG を作成して ***AzureBastionSubnet*** に適用する場合は、NSG に次の規則を追加済みであることを確認してください。 これらのルールを追加しないと、NSG の作成/更新は失敗します。

* **コントロール プレーンの接続:** 443 での GatewayManager からの受信
* **診断ログとその他:** 443 での AzureCloud への送信。 このサービス タグ内で地域タグはまだサポートされていません。
* **ターゲット VM:** 3389 および 22 に対する VirtualNetwork への送信

NSG ルールの例は、この[クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)で参照できます。

## <a name="next-steps"></a>次の手順

Azure Bastion の詳細については、[FAQ](bastion-faq.md) をご覧ください。
