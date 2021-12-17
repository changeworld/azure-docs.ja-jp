---
title: Azure Firewall の強制トンネリング
description: 追加処理のためにインターネットへのトラフィックを別のファイアウォールまたはネットワーク仮想アプライアンスにルーティングするように、強制トンネリングを構成することができます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/13/2021
ms.author: victorh
ms.openlocfilehash: b3d52451713c8fc504487aa293d566264f4eadb6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183378"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall の強制トンネリング

新しい Azure ファイアウォールを構成するときに、インターネットへのすべてのトラフィックを、インターネットに直接送信するのではなく、指定された次ホップにルーティングすることができます。 たとえば、BGP を介して、あるいはユーザー定義ルート (UDR) を使用してアドバタイズされた既定のルートを使用して、オンプレミスのエッジ ファイアウォールまたは他のネットワーク仮想アプライアンス (NVA) へトラフィックを強制して、インターネットに渡される前にネットワーク トラフィックを処理することができます。 この構成をサポートするには、強制トンネリング構成を有効にした Azure Firewall を作成する必要があります。 これは、サービス中断を回避するために必須の要件です。 これが既存のファイアウォールである場合は、この構成をサポートするためにファイアウォールを強制トンネリング モードで再作成する必要があります。 詳細については、「[Azure Firewall に関する FAQ](firewall-faq.yml#how-can-i-stop-and-start-azure-firewall)」でファイアウォールを強制トンネリング モードで停止および再起動する場合について参照してください。

## <a name="forced-tunneling-configuration"></a>強制トンネリング構成

強制トンネリングをファイアウォールの作成時に構成するには、強制トンネリング モードを次のようにして有効にします。 強制トンネリングをサポートするため、サービス管理のトラフィックは顧客のトラフィックから分離されます。 **AzureFirewallManagementSubnet** (最小サブネット サイズ /26) という名前の追加の専用サブネットと、それ自体に関連付けられているパブリック IP アドレスが必要です。 

強制トンネリング モードでは、Azure Firewall サービスには、"*運用上*"の目的で管理サブネット (AzureFirewallManagementSubnet) が組み込みされています。 既定では、サービスはシステム提供のルート テーブルを管理サブネットに関連付けします。 このサブネットで許可される唯一のルートはインターネットへの既定のルートであり、"*ゲートウェイ伝達*"ルートを無効にする必要があります。 ファイアウォールを作成するときに、顧客ルート テーブルを管理サブネットに関連付けしないようにしてください。 

:::image type="content" source="media/forced-tunneling/forced-tunneling-configuration.png" alt-text="強制トンネリングについて":::

この構成では、インターネットに渡される前にトラフィックを処理するため、オンプレミスのファイアウォールまたは NVA へのルートを *AzureFirewallSubnet* に含めることができます。 このサブネットで **[ゲートウェイ ルートの伝達]** が有効になっている場合は、BGP を使用してこれらのルートを *AzureFirewallSubnet* に公開することもできます。

たとえば、お使いのオンプレミスのデバイスに到達するための次ホップとして VPN ゲートウェイを使用して、*AzureFirewallSubnet* に既定のルートを作成できます。 または、 **[ゲートウェイ ルートの伝達]** を有効にして、オンプレミス ネットワークへの適切なルートを取得することもできます。

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="仮想ネットワーク ゲートウェイのルート伝達":::

強制トンネリングを有効にすると、SNAT によってインターネットへのトラフィックに AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスが適用され、オンプレミスのファイアウォールからソースが隠されます。

組織でプライベート ネットワークに対してパブリック IP アドレス範囲を使用している場合、Azure Firewall は、SNAT を使用して、トラフィックのアドレスを AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスに変換します。 ただし、パブリック IP アドレス範囲の SNAT が **行われない** ように、Azure Firewall を構成することができます。 詳細については、「[Azure Firewall の SNAT プライベート IP アドレス範囲](snat-private-range.md)」を参照してください。

強制トンネリングをサポートするように Azure Firewall を構成した後は、構成を元に戻すことはできません。 ファイアウォールで他のすべての IP 構成を削除すると、管理 IP 構成も削除され、ファイアウォールの割り当てが解除されます。 管理 IP 構成に割り当てられているパブリック IP アドレスを削除することはできませんが、異なるパブリック IP アドレスを割り当てることはできます。

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure portal を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する](tutorial-hybrid-portal.md)
