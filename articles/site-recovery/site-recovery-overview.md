<properties
	pageTitle="Site Recovery とは | Microsoft Azure" 
	description="Azure Site Recovery サービスの概要と、サービスをデプロイする方法について説明します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  Site Recovery とは

Azure Site Recovery へようこそ。 この記事では、まず、Site Recovery サービスの簡単な概要と、Site Recovery がビジネス継続性と障害復旧 (BCDR) 戦略にどのように貢献するのかについて説明します。

## 概要

組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリ、ワークロード、およびデータの実行と利用可能な状態を維持し、できるだけ早く通常の動作状態に復旧させる方法を決定する BCDR の戦略が必要です。BCDR 戦略は、災害発生時にビジネス データを安全かつ回復可能な状態に維持し、ワークロードが継続的に利用可能な状態に保たれるようなものである必要があります。

Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持します。プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。詳細については、「[Site Recovery とは](site-recovery-overview.md)」をご覧ください。

## Azure ポータルの Site Recovery

Azure には、リソースの作成と操作に関して、Azure Resource Manager モデルとクラシック サービス管理モデルの 2 種類の[デプロイメント モデル](../resource-manager-deployment-model.md)があります。また、ポータルも 2 つあります。クラシック デプロイメント モデルをサポートする [Azure クラシック ポータル](https://manage.windowsazure.com/)と、両方のデプロイメント モデルをサポートする [Azure ポータル](https://portal.azure.com)です。

このクラシック ポータルと Azure ポータルの両方で Site Recovery を使用することができます。Azure クラシック ポータルでは、クラシック サービス管理モデルで Site Recovery をサポートできます。Azure ポータルでは、クラシック モデルまたは Resource Manager デプロイメントをサポートできます。Azure ポータルのデプロイの詳細については、[こちら](site-recovery-overview.md#site-recovery-in-the-azure-portal)をご覧ください。

この記事の情報は、クラシック ポータルと Azure ポータルの両方のデプロイに当てはまります。違いについては、該当する箇所で説明します。


## Site Recovery を使用する理由 

Site Recovery のビジネス上のメリットを次に示します。

- **BCDR 戦略を簡素化** - Site Recovery では、1 か所から複数のビジネス ワークロードとアプリのレプリケーション、フェールオーバー、および復旧を簡単に処理できます。Site recovery はレプリケーションとフェールオーバーを調整しますが、アプリケーション データをインターセプトすることや、そのデータに関する情報を持つことはありません。
- **柔軟なレプリケーション機能を提供** - Site Recovery を使用すると、Hyper-V 仮想マシン、VMware 仮想マシン、および Windows か Linux の物理サーバーで実行されているワークロードをレプリケートできます。
- **簡単なフェールオーバーと復旧** - Site Recovery では、実稼働環境に影響を与えずに障害復旧の演習をサポートするテスト フェールオーバーを実行できます。また、予期された停止の場合はデータ損失ゼロの計画されたフェールオーバーを実行し、予期しない停止の場合は (レプリケーションの頻度に応じた) 最小限のデータ損失で計画外のフェールオーバーを実行することもできます。フェールオーバー後は、プライマリ サイトにフェールバックできます。Site Recovery に用意されている復旧計画には、多層アプリケーションのフェールオーバーと復旧をカスタマイズできるように、スクリプトや Azure Automation ブックが含まれています。
- **セカンダリ データセンターを排除** - オンプレミスのセカンダリ サイトまたは Azure にレプリケートできます。障害復旧のためのレプリケーション先として Azure を使用すると、セカンダリ サイトの管理に伴うコストと手間が削減され、レプリケートされたデータは元の復元性を十分に備えた状態で Azure Storage に格納されます。
- **既存の BCDR テクノロジと統合** - Site Recovery は、その他のアプリケーションの BCDR 機能と連携します。たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護でき、SQL Server AlwaysOn による可用性グループのフェールオーバーの管理のネイティブ サポートが提供されます。

## レプリケート対象

Site Recovery を使用してレプリケートできる対象の概要を次に示します。

![オンプレミス間](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **レプリケート元 (オンプレミス)** | **レプリケート先** | **記事**
---|---|---|---
VMware VM | VMware サーバー | Azure | [詳細情報](site-recovery-vmware-to-azure-classic.md)
VMware VM | VMware サーバー | セカンダリの VMware サイト | [詳細情報](site-recovery-vmware-to-vmware.md) 
Hyper-V VM | VMM クラウド内の Hyper-V ホスト | Azure | [詳細情報](site-recovery-vmm-to-azure.md) 
Hyper-V VM | VMM クラウド内の Hyper-V ホスト | セカンダリの VMM サイト | [詳細情報](site-recovery-vmm-to-vmm.md)
Hyper-V VM | VMM クラウド内の Hyper-V ホストと SAN ストレージ| SAN ストレージを使用するセカンダリ VMM サイト | [詳細情報](site-recovery-vmm-san.md)
Hyper-V VM | Hyper-V ホスト (VMM なし) | Azure | [詳細情報](site-recovery-hyper-v-site-to-azure.md)
Windows または Linux の物理サーバー | 物理サーバー | Azure | [詳細情報](site-recovery-vmware-to-azure-classic.md)
Windows または Linux の物理サーバーで実行されているワークロード | 物理サーバー | セカンダリ データセンター | [詳細情報](site-recovery-vmware-to-vmware.md) 


## 保護できるワークロードとは

Site Recovery は、障害が発生した場合にワークロードとアプリが一貫した方法で引き続き実行されるように、アプリケーション対応の BCDR に役立ちます。Site Recovery で提供されるものは次のとおりです。

- **アプリケーションの整合性スナップショット** - 単一層または N 層のアプリに適したアプリケーションの整合性スナップショットを使用したレプリケーション。
- **近同期レプリケーション** - レプリケーション頻度は Hyper-V や VMware の連続レプリケーションではわずか 30 秒間です。
- **SQL Server AlwaysOn との統合** - Site Recovery での復旧計画で可用性グループのフェールオーバーを管理できます。
- **柔軟な復旧計画** - 外部スクリプト、手動操作、Azure Automation Runbook を使用して復旧計画を作成およびカスタマイズし、1 回のクリックでアプリケーション スタック全体を復旧できるようにします。
- **自動化ライブラリ** - 豊富な Azure Automation に、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトは、ダウンロードして Site Recovery と統合できます。
- **シンプルなネットワーク管理** - Site Recovery と Azure の高度なネットワーク管理により、IP アドレスの予約、ロード バランサーの構成、効率的なネットワーク切り替えを実現する Azure Traffic Manager の統合など、アプリケーション ネットワーク要件が簡略化されます。


## 次のステップ

- 「[Azure Site Recovery で保護できるワークロード](site-recovery-workload.md)」で詳細情報をお読みください。
- 「[Azure Site Recovery のしくみ](site-recovery-components.md)」で Site Recovery のアーキテクチャの詳細をお読みください。
 

<!---HONumber=AcomDC_0720_2016-->