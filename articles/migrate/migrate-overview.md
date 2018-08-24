---
title: Azure Migrate について | Microsoft Docs
description: Azure Migrate サービスの概要を示します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 08/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8371a160d129586f63b2f14946ed34a8d0637f6c
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714242"
---
# <a name="about-azure-migrate"></a>Azure Migrate について

Azure Migrate サービスは、Azure への移行についてオンプレミスのワークロードを評価します。 このサービスで、オンプレミス マシンの移行の適合性と、パフォーマンスに基づくサイズを評価して、オンプレミスのマシンを Azure で実行するためのコストを見積もることができます。 リフトアンドシフトでの移行を検討している場合や移行の初期評価段階の場合、このサービスは適しています。 その評価後、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) や [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) などのサービスを使用して、Azure にマシンを移行することができます。

## <a name="why-use-azure-migrate"></a>Azure Migrate を使用する理由

Azure Migrate によって次のことが可能になります。

- **Azure 対応性を評価する**: オンプレミスのマシンが Azure での実行に適しているかどうかを評価します。
- **推奨サイズを把握する**: オンプレミス VM のパフォーマンス履歴から、Azure VM の推奨サイズが得られます。
- **毎月のコストを見積もる**: オンプレミスのマシンを Azure で実行するためのコストを見積もります。  
- **高い信頼性で移行する**: オンプレミスのマシンの依存関係を視覚化して、同時に評価および移行するマシンのグループを作成します。

## <a name="current-limitations"></a>現時点での制限事項

- 現時点では Azure VM への移行に関して、オンプレミスの VMware 仮想マシン (VM) だけを評価することができます。 VMware VM は、vCenter Server (バージョン 5.5、6.0、または 6.5) で管理する必要があります。
- Hyper-V 仮想マシンと物理サーバーを評価する必要がある場合、Hyper-V には [Azure Site Recovery Deployment Planner](http://aka.ms/asr-dp-hyperv-doc) を、物理マシンには Microsoft の [パートナーのツール](https://azure.microsoft.com/migration/partners/)を使用してください。
- 1 回の検出で最大 1,500 個の VM を検出でき、1 つのプロジェクトで最大 1,500 個の VM を検出できます。 さらに、一度に最大 1,500 個の VM を評価できます。
- より大規模な環境を検出する場合は、検出を分割して、複数のプロジェクトを作成できます。 [詳細情報](how-to-scale-assessment.md) Azure Migrate では、サブスクリプションあたり最大 20 個のプロジェクトをサポートしています。
- Azure Migrate プロジェクトを作成できるのは、米国中西部または米国東部リージョンに限られます。 このことが、対象となる Azure リージョンへの移行計画の可否に影響することはありません。 移行プロジェクトの場所は単に、オンプレミス環境から検出されたメタデータを保存するためにのみ使用されます。
- Azure Migrate の移行評価では、マネージド ディスクのみがサポートされます。


## <a name="what-do-i-need-to-pay-for"></a>支払い対象について

Azure Migrate の価格について、[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。


## <a name="whats-in-an-assessment"></a>評価の内容

評価の設定は、必要に応じてカスタマイズできます。 次の表は、評価のプロパティをまとめたものです。

**プロパティ** | **詳細**
--- | ---
**ターゲットの場所** | Azure 上の移行先となる場所。<br/><br/>現在 Azure Migrate は 30 のリージョンをサポートしています。 [リージョンを確認](https://azure.microsoft.com/global-infrastructure/services/)してください。 既定では、ターゲット リージョンが "米国西部 2" に設定されます。
**ストレージの種類** | Azure に割り当てるディスクの種類。 サイズ変更の設定基準が**オンプレミス準拠**のときに適用されます。 ターゲット ディスクの種類は、Premium (既定) マネージド ディスクまたは Standard マネージド ディスクとして指定します。 サイズ変更がパフォーマンス ベースの場合、VM のパフォーマンス データに基づいて、ディスクのサイズ変更のレコメンデーションが自動的に行われます。 
**サイズ変更の設定基準** | サイズは、オンプレミス VM の**パフォーマンス履歴**に基づいて変更するか、またはパフォーマンス履歴を考慮せずに "**オンプレミス準拠**" (既定) にすることができます。 
**Azure オファー** | 登録されている [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate はそれに応じてコストを見積もります。
**Azure ハイブリッド特典** | ソフトウェア アシュアランスがあり、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-use-benefit/)を利用してコストの割引を受ける資格があるかどうか。
**予約インスタンス** |  Azure に[予約インスタンス](https://azure.microsoft.com/pricing/reserved-vm-instances/)があるかどうか。 Azure Migrate はそれに応じてコストを見積もります。
**VM のアップタイム** | Azure において VM が動作する期間。 それに応じてコスト見積もりが行われます。
**[価格レベル]** | ターゲット Azure VM の[価格レベル (Basic/Standard)](../virtual-machines/windows/sizes-general.md)。 たとえば、運用環境の移行を計画している場合は、Standard レベルを検討します。この場合、VM の待ち時間は短くなりますが、コストは高くなります。 一方、テスト環境の場合は、Basic レベルを使用することが可能です。この場合、待ち時間は長くなり、コストは安くなります。 既定では [Standard](../virtual-machines/windows/sizes-general.md) レベルが使用されます。
**パフォーマンス履歴** | 既定では、オンプレミスのマシンのパフォーマンスが、過去 1 日のパフォーマンス履歴の 95% パーセンタイル値を使用して評価されます。 
**VM シリーズ** | サイズ見積もりに使用する VM シリーズ。 たとえば、Azure で A シリーズ VM に移行する予定がない運用環境がある場合は、リストまたはシリーズから A シリーズを除外することができます。 サイズ変更は、選択したシリーズに基づいてのみ実行されます。   
**快適性係数** | Azure Migrate では、評価時にバッファー (快適性係数) が考慮されます。 VM のマシン使用率データ (CPU、メモリ、ディスク、ネットワーク) に加えて、このバッファーが適用されます。 快適性係数は、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題に相当します。<br/><br/> たとえば、使用率 20% の 10 コア VM の結果は、通常 2 コア VM になります。 一方、快適性係数を 2.0x とした場合は、結果が 4 コア VM になります。 既定の快適性設定は 1.3x です。


## <a name="how-does-azure-migrate-work"></a>Azure Migrate のしくみ

1.  Azure Migrate プロジェクトを作成します。
2.  Azure Migrate は、コレクター アプライアンスと呼ばれるオンプレミス VM を使用して、オンプレミスのマシンに関する情報を検出します。 このアプライアンスを作成するには、Open Virtualization Appliance (.ova) 形式のセットアップ ファイルをダウンロードし、それをオンプレミスの vCenter Server 上の VM としてインポートします。
3. vCenter Server から VM に接続します。接続の過程で、その新しいパスワードを指定します。
4. VM でコレクターを実行して検出を開始します。
5. コレクターが、VMware PowerCLI のコマンドレットを使用して VM のメタデータを収集します。 検出はエージェントレスであり、VMware ホストまたは VM には何もインストールされません。 収集されるメタデータには、VM 情報 (コア、メモリ、ディスク、ディスク サイズ、ネットワーク アダプター) が含まれています。 また、CPU とメモリの使用率、ディスク IOPS、ディスクのスループット (MBps)、ネットワーク出力 (MBps) など、VM のパフォーマンス データも収集されます。
5.  このメタデータが Azure Migrate プロジェクトにプッシュされます。 それを Azure Portal で確認することができます。
6.  評価の目的で、検出された VM をグループにまとめます。 たとえば、同じアプリケーションを実行する VM をグループにまとめます。 より正確なグループ分けのために、特定のマシンまたはグループ内のすべてのマシンの依存関係を表示し、グループを絞り込むことができます。
7.  グループの定義後、その評価を作成します。
8.  評価が完了したら、それをポータルで表示することも、Excel 形式でダウンロードすることもできます。

  ![Azure Migrate のアーキテクチャ](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>ポート要件とは

次の表は、Azure Migrate の通信に必要なポートをまとめたものです。

コンポーネント | 通信先 |  詳細
--- | --- |--- 
コレクター  | Azure Migrate サービス | コレクターは、SSL ポート 443 経由でサービスに接続します。
コレクター | vCenter Server | 既定では、コレクターはポート 443 で vCenter Server に接続します。 他のポートでサーバーがリッスンしている場合、それをコレクター VM で送信ポートとして構成する必要があります。 
オンプレミス VM | Log Analytics ワークスペース | [TCP 443] | [Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) は TCP ポート 443 を使用して Log Analytics に接続します。 MMA エージェントを必要とする依存関係の視覚化機能を使用している場合のみ、このポートが必要になります。 


## <a name="what-happens-after-assessment"></a>評価後の流れ

オンプレミスのマシンを評価したら、いくつかのツールを使用して移行を実行できます。

- **Azure Site Recovery**: Azure Site Recovery を使用して Azure に移行することができます。 そのためには、ストレージ アカウントや仮想ネットワークなど、必要な [Azure コンポーネントを準備](../site-recovery/tutorial-prepare-azure.md)します。 オンプレミスで、[VMware 環境を準備](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md)します。 すべての準備が整ったら、Azure へのレプリケーションを設定して有効にし、VM を移行します。 [詳細情報](../site-recovery/vmware-azure-tutorial.md)。
- **Azure Database Migration**: オンプレミスのマシンで SQL Server、MySQL、Oracle などのデータベースを実行している場合は、[Azure Database Migration Service](../dms/dms-overview.md) を使用して Azure に移行することができます。 


## <a name="next-steps"></a>次の手順

- オンプレミスの VMware VM に関する評価を[チュートリアルに従って](tutorial-assessment-vmware.md)作成します。
- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。
