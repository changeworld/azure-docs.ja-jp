---
title: Azure Bastion で VM と NSG を使用する
description: Azure Bastion でのネットワーク セキュリティ グループの使用について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: cherylmc
ms.openlocfilehash: adccd5873030adcc5c286ed8d23326796c27ab9c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080980"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>NSG アクセスと Azure Bastion を使用する

Azure Bastion の使用時にネットワーク セキュリティ グループ (NSG) を使用できます。 詳細については、「[セキュリティ グループ](../virtual-network/network-security-groups-overview.md)」を参照してください。

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

この図の内容は次のとおりです。

* Bastion ホストは仮想ネットワークにデプロイされています。
* ユーザーは任意の HTML5 ブラウザーを使用して Azure portal に接続します。
* ユーザーは RDP/SSH で接続するために Azure 仮想マシンに移動します。
* 接続統合 - ブラウザー内でのシングルクリックによる RDP/SSH セッション
* Azure VM ではパブリック IP が必要ありません。

## <a name="network-security-groups"></a><a name="nsg"></a>ネットワーク セキュリティ グループ

このセクションでは、ユーザーと Azure Bastion との間のネットワーク トラフィックと、仮想ネットワーク内のターゲット VM へのネットワークトラフィックについて説明します。

> [!IMPORTANT]
> Azure Bastion リソースで NSG を使用することを選択した場合は、次のイングレスとエグレスのトラフィック規則をすべて作成する **必要があります**。 NSG で次の規則のいずれかを省略すると、将来的に Azure Bastion リソースで必要な更新プログラムが受信できなくなり、その結果、今後リソースでセキュリティの脆弱性が発生します。
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion は、***AzureBastionSubnet*** に対して明示的にデプロイされます。

* **イグレス トラフィック:**

   * **パブリック インターネットからのイグレス トラフィック:** Azure Bastion によってパブリック IP が作成されます。このパブリック IP では、イグレス トラフィック用にポート 443 が有効になっている必要があります。 AzureBastionSubnet でポート 3389/22 が開かれている必要はありません。
   * **Azure Bastion からのイグレス トラフィックのコントロール プレーン:** コントロール プレーン接続の場合は、**GatewayManager** サービス タグからのポート 443 受信を有効にします。 これにより、コントロール プレーン、つまりゲートウェイ マネージャーから Azure Bastion への通信が可能になります。
   * **Azure Bastion データ プレーンからのイングレス トラフィック:** Azure Bastion の基盤コンポーネント間でのデータ プレーン通信については、ポート 8080, 5701 で、**VirtualNetwork** サービス タグから **VirtualNetwork** サービス タグへの受信を有効にします。 これにより、Azure Bastion のコンポーネントが相互に通信できるようになります。
   * **Azure Load Balancer からのイングレス トラフィック:** 正常性プローブの場合は、**AzureLoadBalancer** サービス タグからのポート 443 受信を有効にします。 これにより、Azure Load Balancer は接続を検出できます


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Azure Bastion 接続のインバウンド セキュリティ規則のスクリーンショット。" lightbox="./media/bastion-nsg/inbound.png":::

* **エグレス トラフィック:**

   * **ターゲット VM へのエグレス トラフィック:** Azure Bastion は、プライベート IP 経由でターゲット VM にリーチします。 NSG では、他のターゲット VM サブネットへのエグレス トラフィックをポート 3389 と 22 に許可する必要があります。 Standard SKU の一部としてカスタム ポート機能を使用している場合、NSG では代わりに、他のターゲット VM サブネットへのエグレス トラフィックを、ターゲット VM で開いたカスタム値に許可する必要があります。
   * **Azure Bastion データ プレーンへのエグレス トラフィック:** Azure Bastion の基盤コンポーネント間でのデータ プレーン通信については、ポート 8080, 5701 で、**VirtualNetwork** サービス タグから **VirtualNetwork** サービス タグへの送信を有効にします。 これにより、Azure Bastion のコンポーネントが相互に通信できるようになります。
   * **Azure の他のパブリックエンド ポイントへのエグレス トラフィック:** Azure Bastion から Azure 内のさまざまなパブリック エンドポイントに接続できる必要があります (たとえば、診断ログや測定ログを格納するため)。 このため、Azure Bastion には **AzureCloud** サービス タグに対する 443 への送信が必要です。
   * **インターネットへのエグレス トラフィック:** Azure Bastion は、セッションと証明書の検証のためにインターネットと通信できる必要があります。 そのため、ポート 80 で **インターネット** への送信を有効にすることをお勧めします。


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Azure Bastion 接続のアウトバウンド セキュリティ規則のスクリーンショット。" lightbox="./media/bastion-nsg/outbound.png":::

### <a name="target-vm-subnet"></a>ターゲット VM サブネット
これは、RDP/SSH で接続するターゲット仮想マシンを含むサブネットです。

   * **Azure Bastion からのイグレス トラフィック:** Azure Bastion は、プライベート IP 経由でターゲット VM にリーチします。 RDP または SSH ポート (それぞれポート 3389 または 22。Standard SKU の一部としてカスタム ポート機能を使用している場合は、カスタム ポート値) を、ターゲット VM 側でプライベート IP 経由で開く必要があります。 ベスト プラクティスとして、この規則に Azure Bastion サブネットの IP アドレス範囲を追加して、ターゲット VM サブネット内のターゲット VM で Bastion によってのみこれらのポートが開かれるよにすることができます。


## <a name="next-steps"></a>次のステップ

Azure Bastion の詳細については、[FAQ](bastion-faq.md) をご覧ください。
