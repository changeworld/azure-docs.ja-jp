---
title: "Azure Migrate について | Microsoft Docs"
description: "Azure Migrate サービスの概要を示します。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 01/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3269b865c4ef3c11a674d7b755faab2bbf5970e3
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="about-azure-migrate"></a>Azure Migrate について

Azure Migrate サービスは、Azure への移行についてオンプレミスのワークロードを評価します。 このサービスで、移行の適合性を評価し、パフォーマンスに基づくサイズを評価して、オンプレミスのマシンを Azure で実行するためのコストを見積もることができます。 リフトアンドシフトでの移行を検討している場合や移行の初期評価段階の場合、このサービスは適しています。 その評価後、Azure Site Recovery や Azure Database Migration などのサービスを使用して、Azure にマシンを移行することができます。

> [!NOTE]
> Azure Migrate は現在プレビュー段階で、運用環境のワークロードがサポートされます。

## <a name="why-use-azure-migrate"></a>Azure Migrate を使用する理由

Azure Migrate によって次のことが可能になります。

- **Azure 対応性を評価する**: オンプレミスのマシンが Azure での実行に適しているかどうかを評価します。 
- **推奨サイズを把握する**: オンプレミス VM のパフォーマンス履歴から、Azure VM の推奨サイズが得られます。 
- **毎月のコストを見積もる**: オンプレミスのマシンを Azure で実行するためのコストを見積もります。  
- **高い信頼性で移行する**: オンプレミスのマシンの依存関係を視覚化して、同時に評価および移行するマシンのグループを作成します。 特定のマシンに関して、またはグループ内のすべてのマシンに関して、依存関係を正確に把握することができます。

## <a name="current-limitations"></a>現時点での制限事項

- 現時点では Azure VM への移行に関して、オンプレミスの VMware 仮想マシン (VM) を評価することができます。

> [!NOTE]
> Hyper-V のサポートもロードマップにあり、近日中の実現を目指しています。 それまでの間、Hyper-V のワークロードの移行を計画するときは、[Azure Site Recovery Deployment Planner](http://aka.ms/asr-dp-hyperv-doc) を使うことをお勧めします。 

- 1 回の検出で最大 1000 個の VM を検出でき、1 つのプロジェクトで最大 1500 個の VM を検出できます。 さらに、一度に最大 400 個の VM を評価できます。 さらに多くの VM を検出または評価する必要がある場合は、検出または評価の回数を増やしてください。 [詳細情報](how-to-scale-assessment.md)
- 評価対象となる VM は、vCenter Server (バージョン 5.5、6.0、6.5 のいずれか) で管理されていることが必要です。
- Azure Migrate プロジェクトを作成できるのは米国中西部リージョンに限られます。 ただし、これが他の Azure リージョンへの移行計画に影響することはありません。 移行プロジェクトの場所は単に、オンプレミス環境から検出されたメタデータを保存するためにのみ使用されます。
- Azure Migrate の移行評価では、管理ディスクのみがサポートされます。

## <a name="what-do-i-need-to-pay-for"></a>支払い対象について

Azure Migrate の価格については、[こちら](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/)を参照してください。


## <a name="whats-in-an-assessment"></a>評価の内容

評価は、オンプレミス VM の Azure 適合性を特定し、Azure で VM を実行するための適切な推奨サイズとコスト見積もりを得るのに役立ちます。 評価は、次の表に要約されているプロパティに基づいて行われます。 これらのプロパティは、Azure Migrate ポータルで変更できます。 

**プロパティ** | **詳細**
--- | ---
**ターゲットの場所** | Azure 上の移行先となる場所。 既定では、ターゲットの場所は、米国西部 2 に設定されます。 
**ストレージ冗長** | 移行後に Azure VM で使用されるストレージの種類。 既定値は LRS です。
**価格プラン** | 評価では、ソフトウェア アシュアランスに加入済みかどうかや、[Azure ハイブリッド使用特典](https://azure.microsoft.com/pricing/hybrid-use-benefit/)を利用できるかどうかが考慮されます。 また、適用すべき Azure プランも考慮されるほか、そのプランに加えて適用されるサブスクリプション固有の割引 (%) を指定することができます。 
**[価格レベル]** | Azure VM の[価格レベル (Basic/Standard)](../virtual-machines/windows/sizes-general.md) を指定できます。 これにより、運用環境であるかどうかに応じて適切な Azure VM ファミリに移行することができます。 既定では [Standard](../virtual-machines/windows/sizes-general.md) レベルが使用されます。
**パフォーマンス履歴** | 既定では、オンプレミスのマシンのパフォーマンスが、1 か月の履歴の 95% パーセンタイル値を使用して評価されます。 この設定は変更できます。
**快適性係数** | Azure Migrate では、評価時にバッファー (快適性係数) が考慮されます。 VM のマシン使用率データ (CPU、メモリ、ディスク、ネットワーク) に加えて、このバッファーが適用されます。 快適性係数は、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題に相当します。<br/><br/> たとえば、使用率 20% の 10 コア VM の結果は、通常 2 コア VM になります。 一方、快適性係数を 2.0x とした場合は、結果が 4 コア VM になります。 既定の快適性設定は 1.3x です。


## <a name="how-does-azure-migrate-work"></a>Azure Migrate のしくみ

1.  Azure Migrate プロジェクトを作成します。
2.  Azure Migrate は、コレクター アプライアンスと呼ばれるオンプレミス VM を使用して、オンプレミスのマシンに関する情報を検出します。 このアプライアンスを作成するには、Open Virtualization Appliance (.ova) 形式のセットアップ ファイルをダウンロードし、それをオンプレミスの vCenter Server 上の VM としてインポートします。
3.  vCenter Server のコンソール接続を使用して VM に接続し、接続中に VM の新しいパスワードを指定します。次に、VM でコレクター アプリケーションを実行して検出操作を開始します。
4.  コレクターが、VMware PowerCLI のコマンドレットを使用して VM のメタデータを収集します。 検出はエージェントレスであり、VMware ホストまたは VM には何もインストールされません。 収集されるメタデータには、VM 情報 (コア、メモリ、ディスク、ディスク サイズ、ネットワーク アダプター) が含まれています。 また、CPU とメモリの使用率、ディスク IOPS、ディスクのスループット (MBps)、ネットワーク出力 (MBps) など、VM のパフォーマンス データも収集されます。
5.  このメタデータが Azure Migrate プロジェクトにプッシュされます。 それを Azure Portal で確認することができます。
6.  評価の目的で、検出された VM をグループにまとめます。 たとえば、同じアプリケーションを実行する VM をグループにまとめます。 より正確なグループ分けのために、特定のマシンまたはグループ内のすべてのマシンの依存関係を表示し、グループを絞り込むことができます。
7.  グループを作成したら、そのグループの評価を作成します。 
8.  評価が完了したら、それをポータルで表示することも、Excel 形式でダウンロードすることもできます。



  ![Azure Planner のアーキテクチャ](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>ポート要件とは

次の表は、Azure Migrate の通信に必要なポートをまとめたものです。

|コンポーネント          |通信の対象     |必要なポート  |理由   |
|-------------------|------------------------|---------------|---------|
|コレクター          |Azure Migrate サービス   |TCP 443        |コレクターは、SSL ポート 443 経由でサービスに接続します。|
|コレクター          |vCenter Server          |9443 (既定)   | 既定では、コレクターはポート 9443 で vCenter Server に接続します。 他のポートでサーバーがリッスンしている場合、それをコレクター VM で送信ポートとして構成する必要があります。 |
|オンプレミス VM     | Operations Management Suite (OMS) ワークスペース          |[TCP 443](../log-analytics/log-analytics-windows-agent.md) |MMA エージェントは、TCP 443 を使用して Log Analytics に接続します。 依存関係の視覚化機能を使用していて、Microsoft Monitoring Agent (MMA) エージェントをインストールする場合のみ、このポートが必要になります。 |


  
## <a name="what-happens-after-assessment"></a>評価後の流れ

移行するオンプレミスのマシンを Azure Migrate サービスで評価したら、いくつかのツールを使用して移行を実行できます。

- **Azure Site Recovery**: Azure Site Recovery を使用すると、次のように Azure に移行できます。
  - Azure サブスクリプション、Azure 仮想ネットワーク、ストレージ アカウントなどの Azure リソースを準備します。
  - オンプレミス VMware サーバーの移行の準備をします。 Site Recovery に関する VMware のサポート要件を確認し、VMware サーバーの検出に必要な準備を行います。さらに、移行する VM に Site Recovery Mobility サービスをインストールする準備を行います。 
  - 移行を設定します。 Recovery Services コンテナーの設定、移行元と移行先の設定、レプリケーション ポリシーの設定、レプリケーションの有効化を行います。 ディザスター リカバリーの訓練を実施して、Azure への VM の移行が正しく機能することを確認します。
  - フェールオーバーを実行して、オンプレミスのマシンを Azure に移行します。 
  - Site Recovery の移行のチュートリアルで[詳細](../site-recovery/tutorial-migrate-on-premises-to-azure.md)を確認します。

- **Azure Database Migration**: オンプレミスのマシンで SQL Server、MySQL、Oracle などのデータベースを実行している場合は、Azure Database Migration Service を使用して Azure に移行することができます。 [詳細情報](https://azure.microsoft.com/campaigns/database-migration/)



## <a name="next-steps"></a>次のステップ 
オンプレミスの VMware VM に関する評価を[チュートリアルに従って](tutorial-assessment-vmware.md)作成します。