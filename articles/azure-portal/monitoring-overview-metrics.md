---
title: Microsoft Azure のメトリックの概要 | Microsoft Docs
description: Microsoft Azure のメトリックとその使用方法の概要
author: kamathashwin
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: ashwink

---
# Microsoft Azure のメトリックの概要
この記事では、Microsoft Azure のメトリックの概要、利点、および使用方法について説明します。

## メトリックとは
Azure Monitor では、テレメトリを使用して、Azure のワークロードのパフォーマンスと正常性を視覚的に確認できます。最も重要な Azure テレメトリ データの種類が、Azure リソースのほとんどから出力されるメトリックで、これはパフォーマンス カウンターとも呼ばれます。Azure Monitor では、このメトリックを複数の方法で構成して使用し、監視やトラブルシューティングを行うことができます。

## メトリックで行えること
テレメトリの重要なソースであるメトリックを使用すると、次の作業を行うことができます。

* **パフォーマンスを追跡する**: VM、Web サイト、ロジック アプリなどのリソースのメトリックをポータルのグラフにプロットし、そのグラフをダッシュボードに固定することで、リソースのパフォーマンスを追跡できます。
* **問題の通知を受け取る**: メトリックが特定のしきい値を超えたときに、リソースのパフォーマンスに影響する場合はその旨が通知されます。
* **自動化されたアクションを構成する**: メトリックが特定のしきい値を超えたときに、リソースを自動スケールしたり、Runbook を起動したりします。
* **高度な分析を実行する**: リソースのパフォーマンスや使用傾向も報告することもできます。
* **アーカイブする**: **コンプライアンス/監査**の目的で、リソースのパフォーマンスや正常性の履歴をアーカイブします。

## メトリックの特性
メトリックの特性を次に示します。

* すべてのメトリックが **1 分間隔**です。リソースから 1 分ごとにメトリック値が届くため、リソースの状態と正常性をほぼリアルタイムで把握できます。
* メトリックは、**オプトインなしですぐに利用できます**。追加の診断を設定する必要もありません。
* 各メトリックの **30 日間の履歴**にアクセスできます。リソースのパフォーマンスまたは正常性における最近の傾向や月単位の傾向をすばやく確認できます。

そのための方法は次のとおりです。

* リソースを選択してグラフ上にプロットするときに、Azure ポータルで検出やアクセスを容易に行えるほか、**すべてのメトリックを表示**できます。
* 設定したしきい値をメトリックが超えたときに、そのメトリックに対して、**通知を送信するか自動化されたアクションを実行するアラート ルール**を構成します。自動スケールは自動化された特別なアクションで、リソースをスケールアウトして、Web サイトまたはコンピューティング リソースの受信要求や負荷に対応します。しきい値を超えたメトリックに基づいてスケールアウト/スケールインするように、自動スケール設定のルールを構成することができます。
* メトリックを長期間にわたって**アーカイブ**するか、オフライン レポートに使用します。リソースの診断設定を構成するときに、メトリックを BLOB ストレージにルーティングできます。
* メトリックを Event Hub に**ストリーミング**します。これにより、メトリックを Azure Stream Analytics またはカスタム アプリにルーティングし、ほぼリアルタイムで分析できます。これを行うには、診断設定を使用します。
* すべてのメトリックを OMS Log Analytics に**ルーティング**して、リソースのメトリック データでインスタント分析、検索、カスタム アラートを使用できるようにします。
* 新しい Azure Monitor REST API でメトリックを**使用**します。
* PowerShell コマンドレットまたはクロスプラットフォーム REST API を使用して、メトリックに**クエリ**を実行します。
  
  ![Azure Monitor のメトリックのルーティング](./media/monitoring-overview-metrics/MetricsOverview0.png)

## ポータルを使用してメトリックにアクセスする
ここでは、Azure ポータルを使用してメトリック グラフを作成する手順を簡単に説明します

### リソースの作成後にメトリックを表示する
1. Azure ポータルを開きます
2. 新しい App Service - Web サイトを作成します。
3. 作成した Web サイトの [概要] ブレードに移動します。
4. 新しいメトリックは、"監視" タイルとして表示されます。このタイルを編集して、さらに多くのメトリックを選択できます
   
   ![Azure Monitor のリソースのメトリック](./media/monitoring-overview-metrics/MetricsOverview1.png)

### 1 か所ですべてのメトリックにアクセスする
1. Azure ポータルを開きます
2. 新しい [Monitor] タブに移動し、その下にある [メトリック] オプションを選択します
3. ドロップダウン リストから、サブスクリプション、リソース グループ、およびリソースの名前を選択できます。
4. 使用可能なメトリックの一覧が表示されます。
5. 対象のメトリックを選択し、プロットします。
6. それをダッシュボードに固定するには、右上隅にあるピン アイコンをクリックします。
   
   ![Azure Monitor の 1 か所ですべてのメトリックにアクセス](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> ホスト レベルのメトリックには、VM (Azure Resource Manager ベース) および VM スケール セットからアクセスできます。その際、追加の診断設定は不要です。この新しいホスト レベルのメトリックは、Windows と Linux のインスタンスで使用できます。VM や VMSS で Azure 診断をオンにしている場合にアクセスできる、ゲスト OS レベルのメトリックと混同しないでください。Azure 診断の構成の詳細については、「[Microsoft Azure 診断とは](../azure-diagnostics.md)」を参照してください。
> 
> 

## REST API を使用してメトリックにアクセスする
Azure メトリックには、Azure Monitor API を使用してアクセスできます。メトリックの検出とアクセスに役立つ API は 2 つあります。以下の API を使用してください。

* [Azure Monitor メトリック定義 REST API](https://msdn.microsoft.com/library/mt743621.aspx) では、サービスで使用できるメトリックの一覧にアクセスできます。
* [Azure Monitor メトリック REST API](https://msdn.microsoft.com/library/mt743622.aspx) では、実際のメトリック データにアクセスできます

> [!NOTE]
> この記事では、Azure リソースの[メトリックを対象とした新しい API](https://msdn.microsoft.com/library/dn931930.aspx) で使用するメトリックについて説明します。新しいメトリック定義 API の API バージョンは 2016-03-01 で、メトリック API のバージョンは 2016-09-01 です。従来のメトリック定義とメトリックには、API バージョン 2014-04-01 でアクセスできます。
> 
> 

Azure Monitor REST API を使用した詳細なチュートリアルについては、[Azure Monitor REST API のチュートリアル](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md)に関するページをご覧ください。

## メトリックのエクスポート オプション
[Monitor] タブで [診断ログ] ブレードに移動し、メトリックのエクスポート オプションを表示できます。この記事で前述したユース ケースでは、メトリック (および診断ログ) を選択して、Blob Storage、Event Hubs、または OMS Log Analytics にルーティングできます。

 ![Azure Monitor のメトリックのエクスポート オプション](./media/monitoring-overview-metrics/MetricsOverview3.png)

これを構成するには、Resource Manager テンプレート、[PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md)、[CLI](../monitoring-and-diagnostics/insights-cli-samples.md)、または [REST API](https://msdn.microsoft.com/library/dn931943.aspx) を使用します。

## メトリックでの操作
メトリック データの通知を受け取ったり、自動化されたアクションを実行したりできます。これを行うには、アラート ルールまたは自動スケール設定を構成します。

### アラート ルール
メトリックにアラート ルールを構成できます。このアラート ルールは、メトリックが特定のしきい値を超えたかどうかを確認し、電子メールで通知するか、任意のカスタム スクリプトを実行する際に使用できる webhook を起動します。webhook を使用して、サードパーティ製品の統合を構成することもできます。

 ![Azure Monitor のメトリックとアラート ルール](./media/monitoring-overview-metrics/MetricsOverview4.png)

### Autoscale
Azure リソースの中には、複数のインスタンスをスケールアウトまたはスケールインして、ワークロードを処理できるものがあります。自動スケールは、App Services (Web アプリ)、仮想マシン スケール セット (VMSS)、および従来の Cloud Services に適用されます。ワークロードに影響する特定のメトリックが、指定したしきい値を超えたときにスケールアウトまたはスケールインするように、自動スケール ルールを構成することができます。詳細については、[自動スケールの概要](../monitoring-and-diagnostics/monitoring-overview-autoscale.md)に関するページをご覧ください。

 ![Azure Monitor のメトリックと自動スケール](./media/monitoring-overview-metrics/MetricsOverview5.png)

## サポートされているサービスとメトリック
Azure Monitor は、新しいメトリック インフラストラクチャです。この Azure Monitor が、Azure ポータルと新しい Azure Monitor API バージョンでサポートする Azure サービスは次のとおりです。

* VM (Azure Resource Manager ベース)
* VM スケール セット
* Batch  
* Event Hub 名前空間
* Service Bus 名前空間 (Premium SKU のみ)
* SQL (バージョン 12)
* Elastic SQL Pool
* Web サイト
* Web サーバー ファーム
* Logic Apps
* IoT Hub
* Redis Cache
* Application Gateway
* Search

サポートされているサービスとそのメトリックの詳細な一覧については、[Azure Monitor のメトリック - リソースの種類ごとのサポートされているメトリック](../monitoring-and-diagnostics/monitoring-supported-metrics.md)に関するページをご覧ください。

## 次のステップ
この記事のリンクを参照してください。さらに、次の情報も確認します。

* [自動スケールの一般的なメトリック](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)の概要
* [アラート ルールの作成](../monitoring-and-diagnostics/insights-alerts-portal.md)方法

<!---HONumber=AcomDC_0928_2016-->