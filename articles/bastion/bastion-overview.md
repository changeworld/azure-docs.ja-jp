---
title: Azure Bastion | Microsoft Docs
description: Azure Bastion について説明します。これを使用すると、RDP または SSH ポートを外部に公開することなく、仮想マシンへの安全でシームレスな RDP または SSH 接続を実現できます。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: contperf-fy2q1-portal, ignite-fall-2021
ms.openlocfilehash: 6fbfd04283e5bf89be32c89294ad5d26fb6e5af2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426698"
---
# <a name="what-is-azure-bastion"></a>Azure Bastion とは

Azure Bastion は、ブラウザーと Azure portal を使用して仮想マシンに接続できるようにするサービスで、ユーザーがデプロイします。 Azure Bastion サービスは、お使いの仮想ネットワーク内でプロビジョニングする、フル プラットフォーム マネージド PaaS サービスです。 これにより、TLS 経由で Azure portal から直接、仮想マシンに安全かつシームレスに RDP/SSH 接続できます。 Azure Bastion 経由で接続する場合、仮想マシンにパブリック IP アドレス、エージェント、クライアント ソフトウェアはいずれも不要です。

Bastion は、プロビジョニングされる仮想ネットワーク内のすべての VM に対して安全な RDP および SSH 接続を提供します。 Azure Bastion を使用すると、RDP または SSH を使用した安全なアクセスを提供しながら、お使いの仮想マシンが RDP または SSH ポートを外部に公開しないように保護されます。

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Azure Bastion のアーキテクチャを示す図。":::

## <a name="key-benefits"></a><a name="key"></a>主な利点

* **Azure portal で直接 RDP および SSH を使用する:** シングル クリックのシームレスなエクスペリエンスを使用して、Azure portal 内で直接 RDP および SSH セッションに接続できます。
* **TLS 経由のリモート セッションと RDP または SSH のファイアウォール トラバーサル:** Azure Bastion は、ローカル デバイスに自動的にストリーミングされる HTML5 ベースの Web クライアントを使用します。 ポート 443 で TLS を経由して RDP または SSH セッションに接続するので、企業ファイアウォールを安全にトラバースできます。
* **Azure VM ではパブリック IP が不要:** Azure Bastion は、お使いの Azure 仮想マシンのプライベート IP を使用してその VM への RDP または SSH 接続を開きます。 仮想マシンのパブリック IP は必要ありません。
* **ネットワーク セキュリティ グループ (NSG) を管理する手間はなし**: Azure Bastion は、Azure が提供するフル マネージド プラットフォーム PaaS サービスで、安全な RDP または SSH 接続を提供するよう内部で強化されています。 Azure Bastion サブネットに NSG を適用する必要はありません。 Azure Bastion はプライベート IP 経由で仮想マシンに接続するため、Azure Bastion からの RDP または SSH のみを許可するよう構成できます。 これで、お使いの仮想マシンへの安全な接続が必要になるたびに NSG を管理する手間がなくなります。 NSG の詳細については、「[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules)」を参照してください。
* **ポート スキャンからの保護:** お使いの仮想マシンをパブリック インターネットに公開する必要がないため、この VM は、仮想ネットワーク外部の悪意のあるユーザーによるポート スキャンから保護されます。
* **ゼロデイ攻撃から保護する。一元的な強化:** Azure Bastion は、フル プラットフォームマネージド PaaS サービスです。 これは仮想ネットワークの境界に配置されるため、その仮想ネットワーク内の各仮想マシンを強化することについて心配する必要はありません。 Azure プラットフォームは、Azure Bastion を強化して常に最新の状態にしておくことで、ゼロデイ攻撃から保護します。

## <a name="skus"></a><a name="sku"></a>SKU

Azure Bastion には、Basic と Standard の 2 つの使用可能な SKU が用意されています。 SKU のアップグレード方法を含む詳細については、[構成設定](configuration-settings.md#skus)に関する記事を参照してください。

次の表に、機能と対応する SKU を示します。

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

## <a name="architecture"></a><a name="architecture"></a>アーキテクチャ

Azure Bastion は、仮想ネットワークにデプロイされ、仮想ネットワーク ピアリングをサポートします。 具体的には、Azure Bastion は、ローカルまたはピアリングされた仮想ネットワークに作成された VM に対する RDP または SSH の接続性を管理します。

RDP および SSH は、Azure で実行されているワークロードに接続できる、基本的な手段の一部です。 インターネット経由で RDP または SSH ポートを公開することは望ましくなく、重大な脅威にさらされる面と見なされます。 これは、プロトコルの脆弱性が原因であることがよくあります。 この脅威にさらされる面を含めるには、境界ネットワークの公開される側に bastion ホスト (ジャンプサーバーとも呼ばれます) をデプロイできます。 bastion ホスト サーバーは、攻撃に耐えられるように設計および構成されています。 また、Bastion のサーバーは、踏み台の背後やネットワーク内の奥の方にあるワークロードに対する RDP および SSH 接続も提供しています。

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Azure Bastion のアーキテクチャを示す図。":::

この図は、Azure Bastion のデプロイのアーキテクチャを示しています。 この図の内容は次のとおりです。

* bastion ホストは、プレフィックスが /26 以上の AzureBastionSubnet サブネットを含む仮想ネットワーク内にデプロイされます。
* ユーザーは任意の HTML5 ブラウザーを使用して Azure portal に接続します。
* ユーザーは、接続先の仮想マシンを選択します。
* 1 回クリックすると、ブラウザーで RDP または SSH セッションが開きます。
* Azure VM ではパブリック IP が必要ありません。

## <a name="host-scaling"></a><a name="host-scaling"></a>ホストのスケーリング

Azure Bastion は、ホストの手動スケーリングをサポートしています。 Azure Bastion がサポートできる同時 RDP/SSH 接続数を管理するため、ホスト インスタンス (スケール ユニット) の数を構成できます。 ホスト インスタンス数を増やすと、Azure Bastion でより多くの同時セッションを管理できます。 インスタンス数を減らすと、サポートされる同時セッション数が減少します。 Azure Bastion では、最大 50 個のホスト インスタンスをサポートしています。 この機能は、Azure Bastion Standard SKU でのみ使用できます。

詳細については、[構成設定](configuration-settings.md#instance)に関する記事を参照してください。

## <a name="pricing"></a><a name="pricing"></a>価格

Azure Bastion の価格には、SKU、スケール ユニット、データ転送レートに基づく時間単位の価格の組み合わせが含まれます。 料金情報については、[価格](https://azure.microsoft.com/pricing/details/azure-bastion)に関するページをご覧ください。

## <a name="whats-new"></a><a name="new"></a>新機能

RSS フィードを購読し、[Azure の更新情報](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion)ページで、最新の Azure Bastion 機能の更新を確認します。

## <a name="bastion-faq"></a>Bastion に関する FAQ

よくあるご質問については、Bastion の [FAQ](bastion-faq.md) を参照してください。

## <a name="next-steps"></a>次のステップ

* [チュートリアル: Azure Bastion ホストを作成し、Windows VM に接続する](tutorial-create-host-portal.md)。
* [Learn モジュール: Azure Bastion の概要](/learn/modules/intro-to-azure-bastion/)。
* Azure のその他の重要な[ネットワーク機能](../networking/fundamentals/networking-overview.md)について参照してください。
