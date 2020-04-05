---
title: Azure Firewall Manager の信頼されたセキュリティ パートナー (プレビュー) とは
description: Azure Firewall Manager の信頼されたセキュリティ パートナーについて学習する
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436772"
---
# <a name="what-are-trusted-security-partners-preview"></a>信頼されたセキュリティ パートナー (プレビュー) とは

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

Azure Firewall Manager の*信頼されたセキュリティ パートナー (プレビュー)* では、使い慣れた、最高クラスの、サードパーティのサービスとしてのセキュリティ (SECaaS) オファリングを使用して、ユーザーのインターネット アクセスを保護することができます。

クイック構成を使用すると、サポートされているセキュリティ パートナーを使用してハブをセキュリティで保護し、Virtual Network (VNet) またはリージョン内のブランチの場所からのインターネット トラフィックをルーティングしてフィルター処理することができます。 これは、ユーザー定義ルート (UDR) を設定したり、管理したりすることなく、自動ルート管理を使用して行われます。

複数の Azure リージョンで任意のセキュリティ パートナーを使用して構成されたセキュリティで保護されているハブをデプロイし、世界中のリージョンのあらゆる場所でユーザーの接続性とセキュリティを確保できます。 インターネット/SaaS アプリケーションのトラフィックではセキュリティ パートナーのオファリングを、セキュリティで保護されたハブのプライベート トラフィックでは Azure Firewall を使用でき、グローバルに分散されたユーザーとアプリケーションに近い Azure でのセキュリティ エッジの構築を開始できるようになりました。

このプレビューでは、サポートされているセキュリティ パートナーは、**ZScaler** と **iboss** です。 サポートされているリージョンは、WestCentralUS、NorthCentralUS、WestUS、WestUS2、および EastUS です。

![信頼されたセキュリティ パートナー](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>主なシナリオ

次のシナリオでは、セキュリティ パートナーを使用してインターネット トラフィックをフィルター処理できます。

- Virtual Network (VNet) からインターネットに

   Azure 上で実行されているクラウド ワークロードに対して、高度なユーザー対応のインターネット保護を利用します。

- ブランチからインターネットに

   Azure の接続とグローバル分散を利用して、ブランチからインターネットへのシナリオにサードパーティの NSaaS フィルター処理を簡単に追加できます。 Azure Virtual WAN を使用して、グローバルな転送ネットワークとセキュリティ エッジを構築できます。

次のシナリオがサポートされます。
-   サードパーティのパートナー オファリングを使用して VNet からインターネットに。
-   サードパーティのパートナー オファリングを使用してブランチからインターネットに。
-   サードパーティのパートナー オファリングを使用してブランチからインターネットに。Azure Firewall 経由の残りのプライベート トラフィック (スポークからスポーク、スポークからブランチ、ブランチからスポーク)。

次のシナリオはサポートされていません。

- パートナー オファリングを使用した VNet からインターネットは、プライベート トラフィック用の Azure Firewall と組み合わせることはできません。 次の制限事項を参照してください。

## <a name="current-limitations"></a>現在の制限

- VNet からインターネットでは、プライベート トラフィック用の Azure Firewall と、インターネット トラフィック用のパートナー オファリングを追加して混在させることはできません。 インターネット トラフィックは、セキュリティ保護付き仮想ハブでは、Azure Firewall またはサードパーティのセキュリティ パートナー オファリングの両方ではなく、いずれかに送信できます。 
- 仮想ハブごとにセキュリティ パートナーを 1 つだけデプロイできます。 プロバイダーを変更する必要がある場合は、既存のパートナーを削除し、新しいものを追加する必要があります。

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>セキュリティで保護された仮想ハブでのインターネット トラフィック フィルター処理に関するベストプラクティス

通常、インターネット トラフィックには Web トラフィックが含まれます。 しかし、Office 365 (O365) などの SaaS アプリケーションや、Azure Storage、Azure Sql などの Azure パブリック PaaS サービス宛てのトラフィックも含まれます。 これらのサービスへのトラフィックを処理するためのベスト プラクティスの推奨事項を以下に示します。

### <a name="handling-azure-paas-traffic"></a>Azure PaaS トラフィックの処理
 
- トラフィックが Azure PaaS の大部分で構成されており、アプリケーションのリソース アクセスを IP アドレス、FQDN、サービス タグ、または FQDN タグを使用してフィルター処理できる場合は、保護に Azure Firewall を使用します。

- トラフィックが SaaS アプリケーション アクセスで構成されている場合、またはユーザー対応のフィルター処理が必要な場合 (たとえば、仮想デスクトップ インフラストラクチャ (VDI) ワークロードの場合) あるいは高度なインターネット フィルター処理機能が必要な場合は、ハブでサードパーティのパートナー ソリューションを使用します。

![Azure Firewall Manager のすべてのシナリオ](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Office 365 (O365) トラフィックの処理

グローバル分散ブランチの場所のシナリオでは、Azure のセキュリティで保護されたハブに残りのインターネット トラフィックを送信する前に、Office 365 のトラフィックをブランチで直接リダイレクトする必要があります。

Office 365 では、ユーザー エクスペリエンスを成功させるために、ネットワークの待機時間とパフォーマンスが重要になります。 最適なパフォーマンスとユーザー エクスペリエンスに関するこれらの目標を達成するには、お客様は、Azure を介した残りのインターネット トラフィックのルーティングを検討する前に、Office 365 のダイレクトおよびローカル エスケープを実装する必要があります。

[Office 365 ネットワーク接続の原則](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles)では、主な Office365 ネットワーク接続をユーザー ブランチまたはモバイル デバイスからローカルに、また、インターネットを介して最寄りの Microsoft ネットワーク POP (point of presence) に直接ルーティングすることが求められます。

さらに、Office 365 接続はプライバシーを確保するために強力に暗号化されており、パフォーマンス上の理由から、効率的で専用のプロトコルが使用されます。 これにより、これらの接続を従来のネットワーク レベルのセキュリティ ソリューションの対象にすることが非現実的で影響力の強いものとなります。 これらの理由から、お客様には、Azure を介して残りのトラフィックを送信する前に、ブランチから直接 Office 365 トラフィックを送信することを強くお勧めします。 Microsoft では、Azure と Office 365 を統合し、お客様が Office 365 ダイレクトおよびローカルのインターネット ブレークアウトを簡単に有効にできるようにする、複数の SD-WAN ソリューション プロバイダーと提携しています。 詳細については、「[Virtual WAN を使用して O365 ポリシーを設定するにはどうすればよいですか?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)」を参照してください


## <a name="next-steps"></a>次のステップ

[Azure Firewall Manager を使用して、セキュリティで保護されたハブに信頼されたセキュリティ オファリングをデプロイする](deploy-trusted-security-partner.md)。
