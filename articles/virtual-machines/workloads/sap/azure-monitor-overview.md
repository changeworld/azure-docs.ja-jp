---
title: SAP ソリューション向け Azure Monitor の概要とアーキテクチャ | Microsoft Docs
description: この記事では、SAP ソリューション向け Azure Monitor に関してよく寄せられる質問に対する回答を示します
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 4b5bcb4aecab0247f47aede883e1bfe771262026
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746206"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor for SAP Solutions (プレビュー)

## <a name="overview"></a>概要

この記事では、Azure Monitor for SAP Solutions の仕組みとその機能の概要について説明します。

Azure Monitor for SAP Solutions は、SAP ランドスケープを Azure で実行しているユーザー向けの Azure ネイティブの監視製品です。 製品は、[SAP on Azure Virtual Machines](./hana-get-started.md) と [SAP on Azure Large Instances](./hana-overview-architecture.md) の両方で動作します。

SAP ソリューション向け Azure Monitor を使用すると、Azure インフラストラクチャとデータベースからの利用統計情報を 1 か所で収集し、迅速なトラブルシューティングのために利用統計情報を視覚的に関連付けることができます。

SAP ソリューション向け Azure Monitor は、Azure Marketplace を通じて提供されます。 シンプルで直感的なセットアップ エクスペリエンスが提供されており、SAP ソリューション向け Azure Monitor 用のリソース (**SAP Monitor リソース** と呼ばれます) をわずか数回のクリックでデプロイできます。

コンポーネントに対応する **プロバイダー** を追加することにより、Azure 仮想マシン (VM)、高可用性クラスター、SAP HANA データベース、SAP NetWeaver など、SAP ランドスケープのさまざまなコンポーネントを監視できます。

サポートされているインフラストラクチャ:

- Azure 仮想マシン
- Azure Large Instances

サポートされるデータベース:
- SAP HANA データベース
- Microsoft SQL Server

SAP ソリューション向け Azure Monitor では、Log Analytics や[ブック](../../../azure-monitor/visualize/workbooks-overview.md)などの [Azure Monitor](../../../azure-monitor/overview.md) の既存機能を使用して、追加の監視機能が提供されます。 Azure Monitor for SAP Solutions によって提供される既定のブックを編集することで、[カスタム ビジュアル](../../../azure-monitor/visualize/workbooks-overview.md#getting-started)を作成できます。 [カスタム クエリ](../../../azure-monitor/logs/log-analytics-tutorial.md)の記述や[カスタム アラート](../../../azure-monitor/alerts/alerts-log.md)の作成を、Azure Log Analytics ワークスペースを使用して行います。 [柔軟なリテンション期間](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)を活用し、監視データをチケット発行システムに接続します。

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Azure Monitor for SAP Solutions で収集されるデータ

Azure Monitor for SAP Solutions でのデータ収集は、構成するプロバイダーによって異なります。 パブリック プレビューの間は、次のデータが収集されます。

高可用性 Pacemaker クラスター利用統計情報:
- ノード、リソース、および STONITH ブロック デバイス (SBD) の状態
- Pacemaker の場所の制約
- クォーラム投票とリングの状態
- [Others](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA 利用統計情報:
- CPU、メモリ、ディスク、ネットワークの使用
- HANA システム レプリケーション (HSR)
- HANA バックアップ
- HANA ホストの状態
- インデックス サーバー ロールとネーム サーバー ロール

Microsoft SQL Server 利用統計情報:
- CPU、メモリ、ディスクの使用
- ホスト名、SQL インスタンス名、SAP システム ID
- 経時的なバッチ要求、コンパイル、ページの予測保持期間
- 経時的な最もコストの高い SQL ステートメントの上位 10 個
- SAP システムで最大のテーブルの上位 12 個
- SQL Server エラー ログに記録された問題
- 経時的なブロック プロセスと SQL 待機統計

オペレーティング システムのテレメトリ (Linux) 
- CPU 使用状況、フォークの数、実行中およびブロックされたプロセス 
- メモリ使用状況と分布 (使用済み、キャッシュ、バッファー) 
- スワップ使用状況、ページング、スワップ率 
- ファイル システムの使用、ブロック デバイスごとの読み取りおよび書き込みバイト数 
- ブロック デバイスあたりの読み取り/書き込み待機時間 
- 実行中の I/O 数、永続メモリの読み取り/書き込みバイト数 
- ネットワーク パケットの入出力、ネットワークの入出力バイト数 

SAP NetWeaver テレメトリ:

- ディスパッチャー、ICM、ゲートウェイ、メッセージ サーバー、Enqueue Server、IGS Watchdog のインスタンス プロセスの可用性を含む、SAP システムおよびアプリケーション サーバーの可用性
- 作業プロセス使用の統計と傾向
- エンキュー ロックの統計と傾向
- キュー使用の統計と傾向

## <a name="data-sharing-with-microsoft"></a>Microsoft とのデータ共有

Azure Monitor for SAP Solutions では、SAP on Azure のサポートを強化するために、システム メタデータが収集されます。 PII/EUII は収集されません。

Azure Monitor for SAP Solutions リソースを作成するときに Microsoft とのデータ共有を有効にするには、ドロップダウンから *[共有]* を選択します。 データ共有を有効にすることをお勧めします。 データ共有によってお客様の環境に関する情報が Microsoft サポートおよびエンジニアリング チームに伝えられるため、お客様のミッション クリティカルな SAP on Azure ソリューションに対するサポートを向上させることができます。

## <a name="architecture-overview"></a>アーキテクチャの概要

次の図は、Azure Monitor for SAP Solutions で SAP HANA データベースから利用統計情報が収集される方法の概要を示しています。 このアーキテクチャは、SAP HANA が Azure VM または Azure Large Instances のどちらにデプロイされているかにかかわらず、同じです。

![SAP ソリューション向け Azure Monitor のアーキテクチャ](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

アーキテクチャの主要なコンポーネントは次のとおりです。
- Azure portal – お客様の出発点となります。 Azure portal 内のマーケットプレースに移動して、Azure Monitor for SAP Solutions を見つけることができます。
- Azure Monitor for SAP Solutions リソースは、監視利用統計情報を表示するためのランディング プレースです。
- 管理対象リソース グループ – SAP ソリューション向け Azure Monitor リソースのデプロイの一部として自動的にデプロイされます。 管理対象リソース グループにデプロイされたリソースは、利用統計情報の収集に役立ちます。 デプロイされる主要なリソースとその用途は次のとおりです。
   - Azure 仮想マシン: "*コレクター VM*" とも呼ばれ、Standard_B2ms VM です。 この VM の主な目的は、"*監視ペイロード*" をホストすることです。 監視ペイロードとは、ソース システムから利用統計情報を収集し、そのデータを監視フレームワークに転送するロジックのことです。 上の図では、監視ペイロードには、SQL ポート経由で SAP HANA データベースに接続するためのロジックが含まれています。
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md):このリソースは、SAP HANA データベースの資格情報を安全に保持し、[プロバイダー](./azure-monitor-providers.md)に関する情報を格納するためにデプロイされます。
   - Log Analytics ワークスペース: 利用統計情報が格納される宛先です。
      - 視覚化は、[Azure ブック](../../../azure-monitor/visualize/workbooks-overview.md)を使用して Log Analytics の利用統計情報を基に構築されます。 視覚化をカスタマイズできます。 また、ブックやブック内の特定の視覚化を Azure ダッシュボードにピン留めして自動的に更新することもできます。 更新頻度は最大で 30 分ごとです。
      - SAP モニター リソースと同じサブスクリプション内の既存のワークスペースを使用するには、このオプションをデプロイ時に選択します。
      - Kusto クエリ言語 (KQL) を使用して、Log Analytics ワークスペース内の生テーブルに対する[クエリ](../../../azure-monitor/logs/log-query-overview.md)を実行できます。 "*カスタム ログ*" を調べます。

> [!Note]
> お客様は、管理対象リソース グループにデプロイされている VM への修正プログラムの適用とメンテナンスを行う必要があります。

> [!Tip]
> Azure Monitor for SAP Solutions 用のリソースと同じ Azure サブスクリプション内にデプロイされた既存の Log Analytics ワークスペースがある場合、それを利用統計情報の収集に使用することを選択できます。

### <a name="architecture-highlights"></a>アーキテクチャの特長

アーキテクチャの主な特徴を次に示します。
 - **複数インスタンス** - Azure Monitor for SAP Solutions の単一のリソースを使用して、VNET 内の複数の SAP SID を対象に、特定のコンポーネントの種類 (HANA データベース、高可用性 (HA) クラスター、Microsoft SQL Server、SAP NetWeaver など) の複数のインスタンスに対するモニターを作成できます。
 - **複数プロバイダー** - 上のアーキテクチャの図では、例として SAP HANA プロバイダーが示されています。 同様に、対応するコンポーネント (HANA DB、HA クラスター、Microsoft SQL Server、SAP NetWeaver など) 用に追加のプロバイダーを構成して、それらのコンポーネントからデータを収集できます。
 - **オープンソース** - SAP ソリューション向け Azure Monitor のソース コードは、[GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions) で入手できます。 プロバイダーのコードを参照し、製品の詳細を確認したり、フィードバックを投稿または共有したりすることができます。
 - **拡張可能なクエリ フレームワーク** - 利用統計情報を収集するための SQL クエリは、[JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) で記述されています。 さらに多くの利用統計情報を収集するための SQL クエリを簡単に追加できます。 この記事の最後にあるリンクを使用してフィードバックを残すことで、特定のテレメトリ データを Azure Monitor for SAP Solutions に追加するようにリクエストできます。 担当のアカウントチームに連絡して、フィードバックを残すこともできます。

## <a name="pricing"></a>価格
SAP ソリューション向け Azure Monitor は無料の製品です (ライセンス料金はありません)。 管理対象リソース グループ内の基になるコンポーネントのコストと、データ インジェストとデータ保持に関連する消費コストの支払いは、お客様の責任になります。 [Azure VM の価格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)、[Azure Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)、[Azure ストレージ アカウントの価格](https://azure.microsoft.com/pricing/details/storage/queues/)、[Azure Log Analytics とアラートの価格](https://azure.microsoft.com/pricing/details/monitor/)の詳細については、標準の Azure の価格に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Monitor for SAP Solutions のプロバイダーの詳細。

> [!div class="nextstepaction"]
> [Azure Monitor for SAP Solutions のプロバイダー](azure-monitor-providers.md)
