---
title: Site Recovery とは | Microsoft Docs
description: Azure Site Recovery サービスの概要について説明し、デプロイ シナリオについてまとめます。
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: cfreeman
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2016
ms.author: raynew

---
# <a name="what-is-site-recovery?"></a>Site Recovery とは
Azure Site Recovery へようこそ。 この記事では、Site Recovery サービスの簡単な概要と、どのようにビジネスに貢献できるかについて説明します。

組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリケーション、ワークロード、およびデータの安全と可用性を維持し、できるだけ早く通常の動作状態に復旧させる、ビジネス継続性および障害復旧 (BCDR) の戦略が必要です。 Site Recovery は、この戦略に貢献する Azure サービスです。

Site Recovery は、オンプレミス物理サーバーで実行されているワークロードと仮想マシンのレプリケーションを調整します。 サーバーと VM をプライマリ データセンターからクラウド (Azure) またはセカンダリ データセンターにレプリケートすることができます。 プライマリ サイトで障害が発生した場合は、セカンダリ サイトにフェールオーバーしてアプリとワークロードのアクセス可能性と可用性を維持します。 プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。

## <a name="site-recovery-in-the-azure-portal"></a>Azure ポータルの Site Recovery
Azure には、リソースの作成と操作に関して 2 種類の[デプロイメント モデル](../resource-manager-deployment-model.md)があります。 Azure Resource Manager モデルと、クラシック サービス管理モデルです。 また、ポータルも 2 つあります。クラシック デプロイメント モデルをサポートする [Azure クラシック ポータル](https://manage.windowsazure.com/)と、クラシックおよび Resource Manager の両方のデプロイメント モデルをサポートする [Azure Portal](https://portal.azure.com) です。

* このクラシック ポータルと Azure ポータルの両方で Site Recovery を使用することができます。
* Azure クラシック ポータルでは、クラシック サービス管理モデルで Site Recovery をサポートできます。
* Azure Portal では、クラシック モデルまたは Resource Manager デプロイメントをサポートできます。 

この記事の情報は、クラシック ポータルと Azure ポータルの両方のデプロイに当てはまります。 違いについては、該当する箇所で説明します。

## <a name="why-deploy-site-recovery?"></a>Site Recovery をデプロイする理由
Site Recovery のビジネス上のメリットを次に示します。

* **BCDR を簡略化** - 複数のワークロードのレプリケーション、フェールオーバー、復旧を Azure Portal の 1 つの場所で処理することができます。 Site recovery はレプリケーションとフェールオーバーを調整しますが、アプリケーション データをインターセプトすることや、そのデータに関する情報を持つことはありません。
* **柔軟なレプリケーション機能を提供** - Site Recovery を使用すると、サポートされている Hyper-V VM、VMware VM、および Windows/Linux の物理サーバーで実行されているワークロードをレプリケートできます。
* **簡単なレプリケーション テストの実行** - Site Recovery では、実稼働環境に影響を与えずに障害復旧の演習をサポートするテスト フェールオーバーを実行できます。
* **フェールオーバーと復旧** - 予期された停止の場合はデータ損失ゼロの計画されたフェールオーバーを実行し、予期しない障害の場合は (レプリケーションの頻度に応じた) 最小限のデータ損失で計画外のフェールオーバーを実行することもできます。 フェールオーバー後は、プライマリ サイトにフェールバックできます。 Site Recovery に用意されている復旧計画には、多層アプリケーションのフェールオーバーと復旧をカスタマイズできるように、スクリプトや Azure Automation ブックが含まれています。
* **セカンダリ データセンターの除外** - セカンダリ サイトではなく Azure にワークロードをレプリケートすることができます。 そのため、セカンダリ データセンターを維持するコストや複雑さを避けることができます。 レプリケートされたデータは、元の復元性を十分に備えた状態で Azure Storage に格納されます。 VM は、フェールオーバーが発生したときに、レプリケートされたデータで作成されます。
* **既存の BCDR テクノロジと統合** - Site Recovery は、他の BCDR 機能と統合されています。 たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護でき、可用性グループのフェールオーバーを管理するために SQL Server AlwaysOn のネイティブ サポートも提供されます。

## <a name="what-can-i-replicate?"></a>レプリケート対象
Site Recovery を使用してレプリケートできる対象の概要を次に示します。

![オンプレミス間](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLICATE** | **レプリケート先** |
| --- | --- |
| オンプレミスの VMware VM 上で実行されているワークロード |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [セカンダリ サイト](site-recovery-vmware-to-vmware.md) |
| VMM クラウドで管理されているオンプレミスの Hyper-V VM 上で実行されているワークロード |[Azure](site-recovery-vmm-to-azure.md)<br/><br/> [セカンダリ サイト](site-recovery-vmm-to-vmm.md) |
| VMM クラウドで管理されているオンプレミスの Hyper-V VM 上で実行されているワークロード (SAN ストレージあり) |[セカンダリ サイト](site-recovery-vmm-san.md) |
| オンプレミスの Hyper-V VM 上で実行されているワークロード (VMM なし) |[Azure](site-recovery-hyper-v-site-to-azure.md) |
| オンプレミスの Windows または Linux の物理サーバーで実行されているワークロード |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [セカンダリ サイト](site-recovery-vmware-to-vmware.md) |

## <a name="what-workloads-can-i-protect?"></a>保護できるワークロードとは
Site Recovery では、障害が発生した場合にワークロードとアプリが一貫した方法で引き続き実行されるように、アプリケーション対応の BCDR が有効になります。 Site Recovery で提供されるものは次のとおりです。

* **アプリケーションの整合性スナップショット** - マシンは単一層または複数層のアプリに適したアプリケーションの整合性スナップショットを使用してレプリケーションを行います。 アプリケーションの整合性スナップショットは、ディスク データをキャプチャするだけでなく、メモリのすべてのデータとプロセスのすべてのトランザクションもキャプチャします。
* **近同期レプリケーション** - Site Recovery は、Hyper-V では最短で 30 秒のレプリケーション頻度を提供し、VMware では連続レプリケーションが可能です。
* **柔軟な復旧計画** - 外部スクリプトや手動操作による復旧計画を作成およびカスタマイズすることができます。 Azure Automation Runbook と統合すると、1 回のクリックでアプリケーション スタック全体を復旧できます。
* **SQL Server AlwaysOn との統合**- Site Recovery での復旧計画で可用性グループのフェールオーバーを管理できます。
* **自動化ライブラリ** - 豊富な Azure Automation ライブラリに、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトは、ダウンロードして Site Recovery と統合できます。
* **シンプルなネットワーク管理** - Site Recovery と Azure の高度なネットワーク管理により、IP アドレスの予約、ロード バランサーの構成、効率的なネットワーク切り替えを実現する Azure Traffic Manager の統合など、アプリケーション ネットワーク要件が簡略化されます。

## <a name="next-steps"></a>次のステップ
* 「 [Azure Site Recovery で保護できるワークロード](site-recovery-workload.md)
* 「 [Azure Site Recovery のしくみ](site-recovery-components.md)

<!--HONumber=Oct16_HO2-->


