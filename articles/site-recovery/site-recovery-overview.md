---
title: "Site Recovery とは  | Microsoft Docs"
description: "Azure Site Recovery サービスの概要について説明し、デプロイ シナリオについてまとめます。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 64d085bff08d9a824204851d32504fac3e79024c


---
# <a name="what-is-site-recovery"></a>Site Recovery とは
Azure Site Recovery サービスへようこそ。 この記事では、Site Recovery の概要を簡単に説明します。

組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリケーションとデータの安全および可用性を維持し、できるだけ早く通常の動作状態に復旧させる、ビジネス継続性および障害復旧 (BCDR) の戦略が必要です。

Site Recovery は、オンプレミスの仮想マシンと物理サーバーのレプリケーションを調整することで BCDR 戦略を支援します。 サーバーと VM を、オンプレミスのプライマリ データセンターからクラウド (Azure)、またはセカンダリ データセンターにレプリケートします。

プライマリ サイトで障害が発生した場合は、セカンダリ サイトにフェールオーバーして、ワークロードのアクセス性と可用性を維持します。 プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。

## <a name="site-recovery-in-the-azure-portal"></a>Azure ポータルの Site Recovery
Azure には、リソースの作成と操作に関して 2 種類の[デプロイメント モデル](../resource-manager-deployment-model.md)があります。 Azure Resource Manager モデルと、クラシック サービス管理モデルです。 Azure には、 [Azure クラシック ポータル](https://manage.windowsazure.com/)と [Azure Portal](https://portal.azure.com) の 2 つのポータルがあります。

* Site Recovery は、このクラシック ポータルと Azure Portal の両方にデプロイできます。
* Azure クラシック ポータルでは、クラシック サービス管理モデルで Site Recovery をサポートできます。
* Azure Portal では、クラシック モデルまたは Resource Manager デプロイをサポートできます。

この記事の情報は、クラシック ポータルと Azure ポータルの両方のデプロイに当てはまります。 違いについては、該当する箇所で説明しています。

## <a name="why-deploy-site-recovery"></a>Site Recovery をデプロイする理由
Site Recovery のビジネス上のメリットを次に示します。

* **BCDR を簡略化** - Azure Portal の 1 つの場所から、複数のワークロードをレプリケート、フェールオーバー、および回復できます。 Site Recovery は、アプリケーション データをインターセプトせずに、レプリケーションとフェールオーバーを調整します。
* **柔軟なレプリケーション機能を提供** - サポートされている Hyper-V VM、VMware VM、および Windows/Linux の物理サーバーで実行されている任意のワークロードをレプリケートできます。
* **セカンダリ データセンターの除外** - セカンダリ サイトではなく Azure にワークロードをレプリケートすることができます。 これにより、セカンダリ データセンターの管理に伴うコストと手間が削減されます。 レプリケートされたデータは、元の復元性を備えた状態で Azure Storage に格納されます。 フェールオーバーが発生すると、レプリケートされたデータで Azure VM が作成されます。
* **簡単なレプリケーション テストの実行** - 実稼働環境に影響を与えずに、障害復旧の演習をサポートするテスト フェールオーバーを簡単に実行できます。
* **フェールオーバーと復旧** - 予期された停止の場合はデータ損失ゼロの計画されたフェールオーバーを実行し、予期しない障害の場合は (レプリケーションの頻度に応じた) 最小限のデータ損失で計画外のフェールオーバーを実行することもできます。 プライマリ サイトには、そのサイトが再度使用できるようになった時点でフェールバックできます。
* **複数の VM フェールオーバー** - スクリプトと Azure Automation Runbook を含む復旧計画を設定できます。 復旧計画を使用すると、複数の VM に分散する多層アプリケーションのフェールオーバーと復旧をモデル化およびカスタマイズできます。
* **既存の BCDR テクノロジと統合** - Site Recovery は、他の BCDR テクノロジと統合されています。 たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護でき、可用性グループのフェールオーバーを管理するために SQL Server AlwaysOn のネイティブ サポートも提供されます。

## <a name="what-can-i-replicate"></a>レプリケート対象
Site Recovery を使用してレプリケートできる対象の概要を次に示します。

![オンプレミス間](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLICATE** | **レプリケート先** |
| --- | --- |
| オンプレミスの VMware VM |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [セカンダリ サイト](site-recovery-vmware-to-vmware.md) |
| VMM クラウドで管理されているオンプレミスの Hyper-V VM |[Azure](site-recovery-vmm-to-azure.md)<br/><br/> [セカンダリ サイト](site-recovery-vmm-to-vmm.md) |
| VMM クラウドで管理されているオンプレミスの Hyper-V VM (SAN ストレージあり) |[セカンダリ サイト](site-recovery-vmm-san.md) |
| オンプレミスの Hyper-V VM (VMM なし) |[Azure](site-recovery-hyper-v-site-to-azure.md) |
| オンプレミスの Windows または Linux の物理サーバー |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [セカンダリ サイト](site-recovery-vmware-to-vmware.md) |

## <a name="how-does-site-recovery-protect-workloads"></a>Site Recovery で保護できるワークロード
Site Recovery では、アプリケーション対応レプリケーションを提供するため、障害が発生したときに、ワークロードとアプリが一貫した方法で引き続き実行されます。

* **アプリケーションの整合性スナップショット** - マシンは、単一層または多層アプリに適したアプリケーションの整合性スナップショットを使用してレプリケーションを行います。 アプリケーションの整合性スナップショットは、ディスク データをキャプチャするだけでなく、メモリのすべてのデータとプロセスのすべてのトランザクションもキャプチャします。
* **近同期レプリケーション** - Site Recovery は、Hyper-V では 30 秒のレプリケーション頻度を提供し、VMware では連続レプリケーションが可能です。
* **柔軟な復旧計画** - 外部スクリプトや手動操作による復旧計画を作成およびカスタマイズすることができます。 Azure Automation Runbook と統合すると、1 回のクリックでアプリケーション スタック全体を復旧できます。
* **SQL Server AlwaysOn との統合** - 復旧計画を使用して、可用性グループのフェールオーバーを管理できます。
* **自動化ライブラリ** - 豊富な Azure Automation ライブラリに、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトは、ダウンロードして Site Recovery と統合できます。
* **シンプルなネットワーク管理** - Site Recovery と Azure の高度なネットワーク管理により、IP アドレスの予約、ロード バランサーの構成、効率的なネットワーク切り替えを実現する Azure Traffic Manager の統合など、アプリケーション ネットワーク要件が簡略化されます。

## <a name="next-steps"></a>次のステップ
* 「 [Azure Site Recovery で保護できるワークロード](site-recovery-workload.md)
* 「 [Azure Site Recovery のしくみ](site-recovery-components.md)




<!-----HONumber=Nov16_HO2-->


