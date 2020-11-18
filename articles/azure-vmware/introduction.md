---
title: はじめに
description: Azure で VMware ベースのワークロードをデプロイして管理するための、Azure VMware Solution の機能とメリットについて学習します。
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 1875f535a5ca3e346362409c143da82cb8c07a12
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488589"
---
# <a name="what-is-azure-vmware-solution"></a>Azure VMware Solution とは

Azure VMware Solution は、Azure でプライベート クラウドを提供します。 プライベート クラウドには、専用のベアメタル Azure インフラストラクチャから構築された vSphere クラスターが含まれます。 プライベート クラウド クラスターは、3 台から 16 台のホストに拡張できます。また、1 つのプライベート クラウドに複数のクラスターを含めることができます。 すべてのプライベート クラウドには、vCenter Server、vSAN、vSphere、NSX-T がプロビジョニングされます。 オンプレミス環境からワークロードを移行し、新しい仮想マシンを作成またはデプロイし、プライベート クラウドから Azure サービスを利用できます。

Azure VMware Solution は、VMware によって検証されたソリューションで、拡張機能とアップグレードが継続的に検証およびテストされます。 プライベート クラウドのインフラストラクチャとソフトウェアは Microsoft によって管理および保守されるため、ユーザーはプライベート クラウドでのワークロードの開発と実行に専念できます。

次の図は、Azure、Azure サービス、およびオンプレミス環境におけるプライベート クラウドと VNet の間の隣接性を示しています。 プライベート クラウドから Azure サービスまたは VNet へのネットワーク アクセスにより、SLA に基づく Azure サービス エンドポイントの統合が提供されます。 オンプレミス環境からのプライベート クラウド アクセスでは、ExpressRoute Global Reach を使用して、プライベートでセキュリティで保護された接続を実現します。

![Azure とオンプレミスへの Azure VMware Solution プライベート クラウドの隣接性の画像](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>ホスト、クラスター、およびプライベート クラウド

Azure VMware Solution プライベート クラウドとクラスターは、ベアメタルのハイパーコンバージド Azure インフラストラクチャ ホストから構築されます。 ハイエンドのホストには、576 GB の RAM と 2 基の Intel 18 コア 2.3 GHz プロセッサが搭載されています。 HE ホストには、2 つの vSAN ディスクグループがあり、合計 15.36 TB (SSD) の未加工の vSAN 容量階層と 3.2 TB (NVMe) の vSAN キャッシュ階層があります。

新しいプライベート クラウドは、Azure portal または Azure CLI を介してデプロイされます。

## <a name="networking"></a>ネットワーク

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

ネットワークと相互接続の詳細については、[ネットワークの概念](concepts-networking.md)に関する記事を参照してください。

## <a name="access-and-security"></a>アクセスとセキュリティ

Azure VMware Solution プライベート クラウドでは、セキュリティを強化するために、vSphere のロールベースのアクセス制御が使用されます。 vSphere SSO LDAP 機能は、Azure Active Directory と統合できます。 ID と特権の詳細については、[アクセスと ID の概念](concepts-identity.md)に関する記事を参照してください。

vSAN の保存データの暗号化は既定で有効になっており、vSAN データストアのセキュリティを提供するために使用されます。 詳細については、[ストレージの概念](concepts-storage.md)に関する記事を参照してください。

## <a name="host-and-software-lifecycle-maintenance"></a>ホストとソフトウェアのライフサイクル メンテナンス

Azure VMware Solution プライベート クラウドと VMware ソフトウェアを定期的にアップグレードすることで、お使いのプライベート クラウドで最新のセキュリティ、安定性、および機能セットが確保されます。 プラットフォームのメンテナンスとアップグレードの詳細については、[アップグレードの概念](concepts-upgrades.md)に関する記事を参照してください。

## <a name="monitoring-your-private-cloud"></a>プライベート クラウドの監視

Azure VMware Solution をサブスクリプションにデプロイすると、[Azure Monitor ログ](../azure-monitor/overview.md)が自動的に生成されます。 また、プライベート クラウド内の各仮想マシンでログを収集することもできます。 Azure VMware Solution プライベート クラウドで実行されている Linux および Windows 仮想マシンに [MMA エージェントをダウンロードしてインストール](../azure-monitor/platform/log-analytics-agent.md#installation-options)したり、[Azure Diagnostics 拡張機能](../azure-monitor/platform/diagnostics-extension-overview.md)を有効にしたりすることができます。 通常は仮想マシンで実行するクエリと同じものを実行することもできます。 クエリの作成の詳細については、「[Azure Monitor ログの概要](../azure-monitor/platform/data-platform-logs.md)」を参照してください。 Azure VMware Solution における監視のパターンは、IaaS プラットフォーム内の Azure Virtual Machines に似ています。 追加情報と操作方法については、「[Azure Monitor を使用して Azure 仮想マシンを監視する](../azure-monitor/insights/monitor-vm-azure.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

次の手順では、重要な[プライベート クラウドとクラスターの概念](concepts-private-clouds-clusters.md)について学習します。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
