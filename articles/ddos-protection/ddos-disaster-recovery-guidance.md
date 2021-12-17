---
title: Azure DDoS Protection Standard のビジネス継続性 | Microsoft Docs
description: Azure DDoS Protection Standard に影響を与える Azure サービスの中断が発生した場合の対処方法について説明します。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730335"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard - ビジネス継続性

Azure DDoS Protection Standard のビジネス継続性とディザスター リカバリーにより、中断が発生しても業務を継続できます。 この記事では、可用性 (リージョン内) とディザスター リカバリーについて説明します。

## <a name="overview"></a>概要
Azure DDoS Protection Standard を使用すると、仮想ネットワーク内のパブリック IP アドレスが保護されます。 保護は新規または既存の仮想ネットワークで簡単に有効にでき、アプリケーションやリソースの変更は必要ありません。

Virtual Network (VNet) は、クラウド内のユーザーのネットワークを論理的に表したものです。 VNet は、Azure Application Gateway、Azure Firewall、Azure Virtual Machines などのリソースをホストするための信頼の境界として機能します。 これは、1 つのリージョンの範囲内に作成されます。 同じアドレス空間を持つ VNet を異なる 2 つのリージョン (たとえば米国東部と米国西部) に *作成* できますが、2 つのアドレス空間が同じであるため、2 つを互いに接続することはできません。 

## <a name="business-continuity"></a>ビジネス継続性

アプリケーションが中断される原因はさまざまです。 リージョンで、自然災害によりネットワークが完全に切断される可能性や、いくつかのデバイスやサービスの障害により部分的に障害が発生する可能性があります。 保護された VNet への影響は、これらの状況によって異なります。

**Q:リージョン全体の機能が停止した場合、どうすればよいでしょうか?たとえば、自然災害によりリージョンが完全に切り離されたとします。そのリージョンでホストされている仮想ネットワークはどうなりますか?**

A:サービスが中断されている間は、影響を受けたリージョン内の仮想ネットワークとリソースにアクセスできなくなります。

![シンプルな仮想ネットワークの図。](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**Q:別のリージョンで同じ仮想ネットワークを再作成するには、どうすればよいですか。**

A:仮想ネットワークは、非常に軽量なリソースです。 Azure API を呼び出して、別のリージョンに同じアドレス空間を持つ VNet を作成することができます。 影響を受けたリージョンに存在していたのと同じ環境を再作成するには、API を呼び出して、使用していた VNet のリソースを再デプロイします。 また、オンプレミス接続 (ハイブリッド デプロイなど) がある場合は、新しい VPN Gateway をデプロイし、オンプレミスのネットワークに接続する必要があります。

仮想ネットワークを作成するには、「[Create a virtual network](../virtual-network/manage-virtual-network.md#create-a-virtual-network)(仮想ネットワークを作成する) をご覧ください。

**Q:特定のリージョンの VNet のレプリカを別のリージョンに前もって再作成できますか。**

A:はい。同じプライベート IP アドレス空間とリソースを使用して、2 つの異なるリージョンに 2 つの VNet を前もって作成できます。 VNet 内でインターネットに接続するサービスをホストしていた場合は、Traffic Manager をセットアップして、アクティブなリージョンにトラフィックを地理的に分散させることができます。 ただし、ルーティングの問題の原因となるため、同じアドレス空間を持つ 2 つの VNet をオンプレミスのネットワークに接続することはできません。 一方のリージョンで災害が発生し、VNet が失われた場合は、アドレス空間が一致する利用可能なリージョンのもう一方の VNet をオンプレミスのネットワークに接続できます。

仮想ネットワークを作成するには、「[Create a virtual network](../virtual-network/manage-virtual-network.md#create-a-virtual-network)(仮想ネットワークを作成する) をご覧ください。

## <a name="next-steps"></a>次のステップ

- [DDoS 保護プランの作成](manage-ddos-protection.md)方法について説明します。