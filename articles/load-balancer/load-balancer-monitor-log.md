---
title: パブリックな Basic Load Balancer の操作、イベント、カウンターを監視する
titleSuffix: Azure Load Balancer
description: Azure Load Balancer のログ記録を有効にする方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572781"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Standard Azure Load Balancer 用の Azure Monitor のログ

Azure の各種ログを使用して、Standard Load Balancer の管理やトラブルシューティングを行うことができます。 ログは、イベント ハブまたは Log Analytics ワークスペースにストリーム配信できます。 どのログも Azure Blob Storage から抽出し、Excel や Power BI などのさまざまなツールで表示できます。  各種ログの詳細については、以下の一覧を参照してください。

* **アクティビティ ログ:** 「[リソースのアクションを監視するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md)」を使用して、Azure サブスクリプションに送信されているすべてのアクティビティとそれらの状態を表示できます。 アクティビティ ログは既定で有効になっており、Azure portal で表示できます。 これらのログは、Basic および Standard Load Balancer の両方で使用できます。
* **Standard Load Balancer のメトリック:** このログを使用すると、Standard Azure Load Balancer のログとしてエクスポートされたメトリックに対してクエリを実行できます。 これらのログは、Standard Load Balancer でのみ使用できます。

> [!IMPORTANT]
> **正常性プローブと Load Balancer アラートの各イベント ログは現在機能しておらず、[Azure Load Balancer に関する既知の問題](whats-new.md#known-issues)に記載されています。** 

> [!IMPORTANT]
> ログは、Resource Manager デプロイ モデルでデプロイされたリソースについてのみ使用できます。 クラシック デプロイ モデルのリソースには使用できません。 これらのデプロイ モデルの詳細については、[Resource Manager デプロイとクラシック デプロイ](../azure-resource-manager/management/deployment-models.md)に関する記事をご覧ください。

## <a name="enable-logging"></a>ログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 イベント ログと正常性プローブ ログでデータの収集を開始するには、これらのログを有効にします。 ログ記録を有効にするには、次の手順に従います。

[Azure portal](https://portal.azure.com) にサインインします。 ロード バランサーをまだ作成していない場合は、先に進む前に [ロード バランサーを作成](./quickstart-load-balancer-standard-public-portal.md) します。

1. ポータルで、 **[リソース グループ]** をクリックします。
2. ロード バランサーがある **\<resource-group-name>** を選択します。
3. ロード バランサーを選択します。
4. **[アクティビティ ログ]**  >  **[診断設定]** を選択します。
5. **[診断設定]** ウィンドウの **[診断設定]** で、 **[+ Add diagnostic setting]\(+ 診断設定の追加\)** を選択します。
6. **[診断設定]** 作成ウィンドウで、 **[名前]** フィールドに「**myLBDiagnostics**」と入力します。
7. **[診断設定]** には、3 つのオプションがあります。  1 つ、2 つ、または 3 つすべてを選択し、それぞれをご自身の要件に合わせて構成することができます。
   * **ストレージ アカウントへのアーカイブ**
   * **イベント ハブへのストリーム**
   * **Log Analytics への送信**

    ### <a name="archive-to-a-storage-account"></a>ストレージ アカウントへのアーカイブ
    このプロセス用に既に作成されているストレージ アカウントが必要になります。  ストレージ アカウントを作成する場合は、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)」を参照してください

    1. **[ストレージ アカウントへのアーカイブ]** の横のチェックボックスをオンにします。
    2. **[構成]** を選択して、 **[ストレージ アカウントの選択]** ウィンドウを開きます。
    3. プルダウン ボックスで、ストレージ アカウントが作成された **サブスクリプション** を選択します。
    4. プルダウン ボックスの **[ストレージ アカウント]** で、ストレージ アカウントの名前を選択します。
    5. [OK] を選択します。

    ### <a name="stream-to-an-event-hub"></a>イベント ハブへのストリーミング
    このプロセス用に既に作成されているイベント ハブが必要になります。  イベント ハブを作成するには、「[クイックスタート:Azure portal を使用したイベント ハブの作成](../event-hubs/event-hubs-create.md)」を参照してください

    1. **[イベント ハブへのストリーム]** の横にあるチェックボックスをオンにします
    2. **[構成]** を選択して、 **[イベント ハブの選択]** ウィンドウを開きます。
    3. プルダウン ボックスで、イベント ハブが作成された **サブスクリプション** を選択します。
    4. プルダウン ボックスで **[イベント ハブの名前空間の選択]** を選択します。
    5. プルダウン ボックスで **[イベント ハブ ポリシー名の選択]** を選択します。
    6. [OK] を選択します。

    ### <a name="send-to-log-analytics"></a>Log Analytics への送信
    このプロセスでは、Log Analytics ワークスペースが既に作成され、構成されている必要があります。  Log Analytics ワークスペースを作成するには、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/learn/quick-create-workspace.md)」を参照してください

    1. **[Log Analytics への送信]** の横のチェックボックスをオンにします。
    2. プルダウン ボックスで、Log Analytics ワークスペースがある **サブスクリプション** を選択します。
    3. プルダウン ボックスで、 **[Log Analytics ワークスペース]** を選択します。


8.  **[診断設定]** ウィンドウの **[メトリック]** セクションの下で、以下の横にあるチェック ボックスをオンにします。**AllMetrics**

9. すべてが正しく表示されていることを確認し、 **[診断設定]** の作成ウィンドウの上部にある **[保存]** をクリックします。

## <a name="activity-log"></a>アクティビティ ログ

アクティビティ ログは既定で生成されます。 これは、サブスクリプション レベルでエクスポートするように構成することができます。それには、[この記事のこちらの手順に従ってください](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)。 これらのログについて詳しくは、「[リソースのアクションを監視するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md)」を参照してください。

### <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools:** Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 それぞれの方法の詳細な手順については、「 [リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md) 」を参照してください。
* **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure 監査ログ統合](https://powerbi.microsoft.com/integrations/azure-audit-logs/)を使用すると、構成済みのダッシュボードでデータを分析できます。また、要件に合わせてビューをカスタマイズすることもできます。

## <a name="metrics-as-logs"></a>ログとしてのメトリック
Azure Monitor によって提供されている、メトリックからログへのエクスポート機能を使用すると、ご利用の Load Balancer メトリックをエクスポートできます。 これらのメトリックでは、1 分のサンプリング間隔ごとにログ エントリが生成されます。

メトリックからログへのエクスポートは、リソース レベルごとに有効になります。 これらのログを有効にするには、[診断設定] ブレードに移動し、リソース グループでフィルター処理し、メトリックのエクスポートを有効にする Load Balancer を選択します。 Load Balancer 診断設定ページが起動したら、[AllMetrics] を選択して、資格のあるメトリックをログとしてエクスポートします。

メトリックのエクスポート制限事項については、この記事の「[制限事項](#limitations)」セクションを参照してください。

### <a name="view-and-analyze-metrics-as-logs"></a>メトリックをログとして表示および分析する
Standard Load Balancer の診断設定で AllMetrics を有効にした後に、Event Hub または Log Analytics ワークスペースを使用する場合、これらのログは AzureMonitor テーブルに設定されます。 ストレージにエクスポートする場合は、ストレージ アカウントに接続し、イベントおよび正常性プローブ ログの JSON ログ エントリを取得します。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。 

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## <a name="stream-to-an-event-hub"></a>イベント ハブへのストリーミング
診断情報がイベント ハブにストリーム配信されたら、Azure Monitor 統合を使用して、サードパーティ製の SIEM ツールで集中ログ分析に使用できます。 詳しくは、「[イベント ハブへの Azure 監視データのストリーム配信](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)」を参照してください

## <a name="send-to-log-analytics"></a>Log Analytics への送信
Azure 内のリソースは、Log Analytics ワークスペースに直接送信される診断情報を持つことができます。このワークスペースでは、トラブルシューティングと分析のために情報に対して複雑なクエリを実行できます。  詳しくは、「[Azure Monitor の Log Analytics ワークスペースで Azure リソース ログを収集する](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)」を参照してください

## <a name="limitations"></a>制限事項
Load Balancer 用の、メトリックからログへのエクスポート機能を使用する場合、現時点では次の制限があります。
* メトリックは、現在、ログとしてエクスポートするときには内部名を使用して表示されます。そのマッピングは以下の表にあります。
* メトリックの次元は保持されません。 たとえば、DipAvailability (正常性プローブの状態) などのメトリックの場合、バックエンド IP アドレスごとに分割または表示することはできません。
* 使用された SNAT ポートと割り当てられた SNAT ポートの各メトリックは、現在、ログとしてエクスポートできません。

## <a name="next-steps"></a>次の手順
* [Load Balancer で使用可能なメトリックを取得する](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Azure Monitor の指示に従ってクエリを作成してテストする](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* 次のリンクを使用して、この記事または Load Balancer 機能に関するフィードバックをお送りください。
