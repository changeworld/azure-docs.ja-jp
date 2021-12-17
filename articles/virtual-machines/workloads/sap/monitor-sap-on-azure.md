---
title: SAP on Azure の監視 (プレビュー) | Microsoft Docs
description: SAP on Azure を監視する方法については、こちらでご確認ください。
author: mamccrea
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.custom: subject-monitoring
ms.date: 10/13/2021
ms.author: mamccrea
ms.openlocfilehash: cf68247c9d259aff6ffe6d2e7cd9be76eea6f237
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063272"
---
# <a name="monitor-sap-on-azure-preview"></a>SAP on Azure の監視 (プレビュー)

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 

このアーティクルでは Azure Monitor for SAP Solutions を使用して Azure で実行されている SAP を監視する方法について説明します。 Azure Monitor for SAP Solutions は、[Azure Monitor](../../../azure-monitor/overview.md) インフラストラクチャの特定部分を使用します。

## <a name="overview"></a>概要

Azure Monitor for SAP Solutions は、SAP ランドスケープを Azure で実行しているユーザー向けの Azure ネイティブの監視製品です。 これは、[SAP on Azure Virtual Machines](./hana-get-started.md) と [SAP on Azure Large Instances](./hana-overview-architecture.md) の両方で動作します。

Azure Monitor for SAP Solutions を使用すると、Azure インフラストラクチャとデータベースからの利用統計情報を 1 か所で収集し、迅速なトラブルシューティングのためにデータを視覚的に関連付けることができます。

コンポーネントに対応する **プロバイダー** を追加することにより、Azure 仮想マシン (VM)、高可用性クラスター、SAP HANA データベース、SAP NetWeaver など、SAP ランドスケープのさまざまなコンポーネントを監視できます。 詳細は、「[Azure portal を使用して Azure Monitor for SAP Solutions をデプロイする](azure-monitor-sap-quickstart.md)」を参照してください。

サポートされているインフラストラクチャ:

- Azure 仮想マシン
- Azure Large Instances

サポートされるデータベース:
- SAP HANA データベース
- Microsoft SQL Server

Azure Monitor for SAP Solutions は、[Azure Monitor](../../../azure-monitor/overview.md) の機能のうち、[Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md) と [Workbook](../../../azure-monitor/visualize/workbooks-overview.md) を使用します。 これを使用すると、次のことができます。

- Azure Monitor for SAP Solutions によって提供される既定の Workbooks を編集することで、[カスタム視覚化](../../../azure-monitor/visualize/workbooks-overview.md#getting-started)を作成できます。 
- [カスタム クエリ](../../../azure-monitor/logs/log-analytics-tutorial.md)を書き込みます。
- Azure Log Analytics ワークスペースを使用して、[カスタム アラート](../../../azure-monitor/alerts/alerts-log.md)を作成します。 
- Azure Monitor Logs/Log Analytics の[柔軟な保有期間の範囲](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)を活用します。 
- 監視データをチケット システムに接続します。

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Azure Monitor for SAP Solutions で収集されるデータ

Azure Monitor for SAP Solutions は、他の多くの Azure リソースのように Azure Monitor メトリクスやリソース ログ データを収集しません。 代わりに、Azure Monitor Logs システムにカスタム ログを直接送信します。このシステムでは、Log Analytics の組み込み機能を使用できます。

Azure Monitor for SAP Solutions でのデータ収集は、構成するプロバイダーによって異なります。 パブリック プレビュー中は、次のデータが収集されます。

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
- データベースの拡張
- 上位テーブル
- 使用するファイル システム

Microsoft SQL Server 利用統計情報:
- CPU、メモリ、ディスクの使用
- ホスト名、SQL インスタンス名、SAP システム ID
- 経時的なバッチ要求、コンパイル、ページの予測保持期間
- 経時的な最もコストの高い SQL ステートメントの上位 10 個
- SAP システムで最大のテーブルの上位 12 個
- SQL Server エラー ログに記録された問題
- 経時的なブロック プロセスと SQL 待機統計

オペレーティング システムのテレメトリ (Linux): 
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
- **Azure portal** – お客様の始点になります。 Azure portal 内のマーケットプレースに移動して、Azure Monitor for SAP Solutions を見つけることができます。
- **Azure Monitor for SAP Solutions リソース** – 監視テレメトリを表示するためのランディング プレースです。
- **管理対象リソース グループ** – Azure Monitor for SAP Solutions リソース デプロイの一部として自動的にデプロイされます。 管理対象リソース グループにデプロイされたリソースは、テレメトリの収集に役立ちます。 デプロイされる主なリソースとその用途は次のとおりです。
   - **Azure 仮想マシン**: *コレクター VM* とも呼ばれる、Standard_B2ms VM です。 この VM の主な目的は、"*監視ペイロード*" をホストすることです。 監視ペイロードとは、ソース システムから利用統計情報を収集し、そのデータを監視フレームワークに転送するロジックのことです。 前の図では、監視ペイロードには、SQL ポート経由で SAP HANA データベースに接続するためのロジックが含まれています。
   - **[Azure Key Vault](../../../key-vault/general/basic-concepts.md)** : このリソースは、SAP HANA データベースの資格情報を安全に保持し、[プロバイダー](./azure-monitor-providers.md)に関する情報を格納するためにデプロイされます。
   - **Log Analytics ワークスペース**: 利用統計情報が格納される宛先です。
      - 視覚化は、[Azure ブック](../../../azure-monitor/visualize/workbooks-overview.md)を使用して Log Analytics の利用統計情報を基に構築されます。 視覚化をカスタマイズできます。 また、ブックやブック内の特定の視覚化を Azure ダッシュボードにピン留めして自動的に更新することもできます。 更新頻度は最大で 30 分ごとです。
      - SAP モニター リソースと同じサブスクリプション内の既存のワークスペースを使用するには、このオプションをデプロイ時に選択します。
      - Kusto クエリ言語 (KQL) を使用して、Log Analytics ワークスペース内の生テーブルに対する[クエリ](../../../azure-monitor/logs/log-query-overview.md)を実行できます。 "*カスタム ログ*" を調べます。

> [!Note]
> お客様は、管理対象リソース グループにデプロイされている VM への修正プログラムの適用とメンテナンスを行う必要があります。

> [!Tip]
> Azure Monitor for SAP Solutions 用のリソースと同じ Azure サブスクリプション内にデプロイされた既存の Log Analytics ワークスペースがある場合、それを利用統計情報の収集に使用できます。

### <a name="architecture-highlights"></a>アーキテクチャの特長

アーキテクチャの主な特徴を次に示します。
 - **複数インスタンス** - Azure Monitor for SAP Solutions の単一のリソースを使用して、VNET 内の複数の SAP SID を対象に、特定のコンポーネントの種類 (HANA データベース、高可用性 (HA) クラスター、Microsoft SQL Server、SAP NetWeaver など) の複数のインスタンスに対するモニターを作成できます。
 - **複数プロバイダー** - 上のアーキテクチャの図では、例として SAP HANA プロバイダーが示されています。 同様に、対応するコンポーネントに対してさらに多くのプロバイダーを設定し、それらのコンポーネントからデータを収集できます。 たとえば、HANA DB、HA クラスター、Microsoft SQL server、SAP NetWeaver などです。 
 - **オープンソース** - SAP ソリューション向け Azure Monitor のソース コードは、[GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions) で入手できます。 プロバイダーのコードを参照し、製品の詳細を確認したり、フィードバックを投稿または共有したりすることができます。
 - **拡張可能なクエリ フレームワーク** - 利用統計情報を収集するための SQL クエリは、[JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) で記述されています。 さらに多くの利用統計情報を収集するための SQL クエリを簡単に追加できます。 特定の利用統計情報を Azure Monitor for SAP Solutions に追加するように要求できます。 これを行うには、このアーティクルの最後にあるリンクを使用してフィードバックを残します。 担当のアカウントチームに連絡して、フィードバックを残すこともできます。

## <a name="analyzing-metrics"></a>メトリックの分析

Azure Monitor for SAP Solutions はメトリックをサポートしていません。 

## <a name="analyzing-logs"></a>ログの分析

Azure Monitor for SAP Solutions は、リソース ログまたはアクティビティ ログをサポートしていません。 Azure Monitor Logs で使用され、Log Analytics によってクエリ可能なテーブルの一覧については、「[SAP on Azure データ リファレンスの監視](monitor-sap-on-azure-reference.md#azure-monitor-logs-tables)」を参照してください。 

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!IMPORTANT]
> Azure Monitor for SAP Solutions のメニューから **[ログ]** を選択すると、Log Analytics は、クエリ スコープを現在の Azure Monitor for SAP Solutions に設定して開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のアカウントのデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../../../azure-monitor/logs/scope.md)」を参照してください。

Kusto クエリを使用すると、Monitor SAP on Azure リソースを監視できます。 指定された時間範囲のカスタム ログからデータを提供するサンプル クエリを次に示します。 時間の範囲と行数を指定します。 この例では、選択した時間範囲に対して 5 行のデータを取得します。 

```Kusto
custom log name 
| take 5

```

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。

Azure portal を使用して Azure Monitor for SAP Solutions でアラートを構成できます。 詳細は、「[Azure portal を使用して Azure Monitor for SAP Solutions でアラートを構成する](azure-monitor-alerts-portal.md)」を参照してください。

## <a name="create-azure-monitor-for-sap-solutions-resources"></a>Azure Monitor for SAP Solutions リソースを作成する

Azure Monitor for SAP Solutions をデプロイし、プロバイダーを構成するためのオプションがいくつかあります。 
- Azure Monitor for SAP Solutions は、Microsoft Azure portal からすぐにデプロイできます。 詳細は、「[Azure portal を使用して Azure Monitor for SAP Solutions をデプロイする](azure-monitor-sap-quickstart.md)」を参照してください。
- Azure PowerShell を使用する。 詳細は、「[Azure PowerShell と Azure Monitor for SAP Solutions をデプロイする](azure-monitor-sap-quickstart-powershell.md)」を参照してください。
- CLI 拡張機能を使用します。 詳細は、「[SAP HANA Command Module](https://github.com/Azure/azure-hanaonazure-cli-extension#sapmonitor)」を参照してください。

## <a name="pricing"></a>価格
SAP ソリューション向け Azure Monitor は無料の製品です (ライセンス料金はありません)。 管理対象リソース グループの基にコンポーネントのコストを支払う必要があります。 また、データの使用量と保有期間に関連する従量課金コストも支払う必要があります。 詳細は、標準 Azure の価格を参照してください。
- [Azure VM の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Azure キー コンテナーの価格](https://azure.microsoft.com/pricing/details/key-vault/)
- [Azure ストレージ アカウントの料金](https://azure.microsoft.com/pricing/details/storage/queues/)
- [Azure Log Analytics とアラートの価格](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="next-steps"></a>次のステップ

- Azure Monitor for SAP Solutions に関連するカスタム ログの一覧と、関連するデータ タイプの情報については、「[Monitor SAP on Azure データ リファレンス](monitor-sap-on-azure-reference.md)」を参照してください。
- Azure Monitor for SAP Solutions で利用できるプロバイダーについては、「[Azure Monitor for SAP Solutions プロバイダー](azure-monitor-providers.md)」を参照してください。
