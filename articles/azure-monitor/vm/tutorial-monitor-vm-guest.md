---
title: チュートリアル - Azure 仮想マシンからゲストのログとメトリックを収集する
description: Azure 仮想マシンからゲストのログとメトリックを収集するデータ収集ルールを作成します。
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/08/2021
ms.openlocfilehash: 5bd3b557bf7bc9ada4e75979e593c7fb0c338dd4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349810"
---
# <a name="tutorial-collect-guest-logs-and-metrics-from-azure-virtual-machine"></a>チュートリアル: Azure 仮想マシンからゲストのログとメトリックを収集する
[VM insights での監視を有効](tutorial-monitor-vm-enable.md)にすると、Log Analytics エージェントを使用してパフォーマンス データが収集されます。 ゲスト オペレーティング システムからログを収集し、パフォーマンス データを Azure Monitor メトリックに送信するには、[Azure Monitor エージェント](../agents/azure-monitor-agent-overview.md)をインストールし、収集するデータとその送信先を定義する[データ収集ルール](../agents/data-collection-rule-overview.md) (DCR) を作成します。 

> [!NOTE]
> Azure Monitor エージェントのリリース以前は、Azure 仮想マシンからゲストのメトリックを収集する際に、Windows および Linux を対象とした [Azure 診断の拡張機能](../agents/diagnostics-extension-overview.md) (それぞれ WAD および LAD) が使われていました。 これらは今も提供されており、仮想マシンの **[診断設定]** メニュー項目から構成できますが、これらを Azure Monitor エージェントに置き換える作業が進められています。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * ゲストのパフォーマンス データを Azure Monitor メトリックに、またログ イベントを Azure Monitor ログに送信するデータ収集ルールを作成する。 
> * メトリックス エクスプローラーでゲストのメトリックを確認する。
> * Log Analytics でゲストのログを確認する。

## <a name="prerequisites"></a>必須コンポーネント
このチュートリアルを完了するには、以下が必要です。 

- 監視する Azure 仮想マシン。


## <a name="create-data-collection-rule"></a>データ収集ルールを作成する
Azure Monitor の[データ収集ルール](../agents/data-collection-rule-overview.md)は、収集するデータとその送信先を定義するものです。 Azure portal を使用してデータ収集ルールを定義するときに、ルールの適用対象となる仮想マシンを指定します。 Azure Monitor エージェントがまだインストールされていない仮想マシンには、エージェントが自動的にインストールされます。

> [!NOTE]
> 現在、Azure Monitor エージェントは、Azure portal の **[モニター]** メニューからインストールする必要があります。 まだ仮想マシンのメニューからは、この機能を利用できません。 

Azure portal の **[モニター]** メニューから **[データ収集ルール]** を選択し、 **[作成]** を選択して新しいデータ収集ルールを作成します。

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-create.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-create.png" alt-text="データ収集ルールを作成する":::

**[基本]** タブで **ルール名** を指定します。これが Azure portal に表示されるルールの名前になります。 DCR とそれに関連したデータが格納される **サブスクリプション**、**リソース グループ**、**リージョン** を選択します。 これらは監視対象のリソースと同じである必要はありません。 以降 DCR を定義する過程で利用できるオプションは、 **[プラットフォームの種類]** によって決まります。 関連付けの対象となるプラットフォームの種類が Windows または Linux のリソースのみである場合は *[Windows]* または *[Linux]* を、その両方である場合は *[カスタム]* を選択してください。

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-basics.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-basics.png" alt-text="データ収集ルールの基本":::

## <a name="select-resources"></a>リソースを選択する
**[リソース]** タブで、データ収集ルールの適用先となる仮想マシンを指定します。 まだ Azure Monitor エージェントがインストールされていない仮想マシンには、Azure Monitor エージェントがインストールされます。 **[リソースの追加]** をクリックして、目的の仮想マシンあるいはそれが属しているリソース グループまたはサブスクリプションを選択します。 選択されたスコープ内のすべての仮想マシンにデータ収集ルールが適用されます。

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-resources.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-resources.png" alt-text="データ収集ルールのリソース":::

## <a name="select-data-sources"></a>データ ソースを選択する
1 つのデータ収集ルールには、複数のデータ ソースを適用できます。 このチュートリアルでは、同じルールを使用して、ゲストのメトリックとゲストのログを両方収集します。 メトリックス エクスプローラーと Log Analytics の両方で分析できるよう、メトリックは、Azure Monitor メトリックと Azure Monitor ログの両方に送信します。

**[収集と配信]** タブで **[データ ソースの追加]** をクリックします。 **[データ ソースの種類]** で、 **[パフォーマンス カウンター]** を選択します。 設定は **[基本]** のままにして、収集するカウンターを選択してください。 **[カスタム]** を選んだ場合は、個々のメトリック値を選択できるようになります。 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" alt-text="データ収集ルールのメトリックのデータ ソース":::

**[Destination]\(送信先\)** タブを選択します。既に **[Azure Monitor メトリック]** が表示されているはずです。 さらに追加する場合は、 **[ターゲットの追加]** をクリックしてください。 **[送信先の種類]** には **[Azure Monitor ログ]** を選択します。 **[アカウントまたは名前空間]** には、自分の Log Analytics ワークスペースを選択してください。 **[データ ソースの追加]** をクリックして、データ ソースを保存します。

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" alt-text="データ収集ルールの送信先":::

データ収集ルールにログを追加するために、もう一度 **[データ ソースの追加]** をクリックします。 **[データ ソースの種類]** に、 **[Windows イベント ログ]** または **[Linux syslog]\(Linux の Syslog\)** を選択します。 収集するログ データの種類を選択します。 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" alt-text="データ収集ルールの Windows ログ データ ソース":::

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" alt-text="データ収集ルールの Linux ログ データ ソース":::

**[Destination]\(送信先\)** タブを選択します。 **[送信先の種類]** には、既に **[Azure Monitor ログ]** が選択されているはずです。 **[アカウントまたは名前空間]** には、自分の Log Analytics ワークスペースを選択してください。 まだワークスペースをお持ちでない場合は、サブスクリプションの既定のワークスペースを選択すれば、そのワークスペースが自動的に作成されます。 **[データ ソースの追加]** をクリックして、データ ソースを保存します。

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" alt-text="データ収集ルールのログ送信先":::

**[確認と作成]** をクリックすると、選択した仮想マシンにデータ収集ルールが作成されて Azure Monitor エージェントがインストールされます。

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-save.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-save.png" alt-text="データ収集ルールの保存":::

## <a name="viewing-logs"></a>ログの表示
Log Analytics ワークスペースからデータを取得するには、Kusto クエリ言語 (KQL) で記述したログ クエリを使用します。 仮想マシンには、あらかじめ作成されたクエリ一式が用意されていますが、ここでは、収集対象のイベントを見るだけの単純なクエリを使用することにします。 

仮想マシンのメニューから **[ログ]** を選択します。 Log Analytics が開き、そのマシンにスコープが設定された空のクエリ ウィンドウが表示されます。 そのマシンから収集されたレコードだけがクエリの対象になります。 

> [!NOTE]
> Log Analytics を開くと、 **[クエリ]** ウィンドウが表示される場合があります。 ここには、あらかじめ作成されたクエリが用意されており、それらを使用することもできます。 ここでは単純なクエリを手動で作成するので、このウィンドウは閉じてください。

:::image type="content" source="media/tutorial-monitor-vm/log-analytics.png" lightbox="media/tutorial-monitor-vm/log-analytics.png" alt-text="Log Analytics":::


ご使用のマシンで実行されているのが Windows であるか Linux であるかに応じて、空のクエリ ウィンドウに「`Event`」または「`Syslog`」を入力し、 **[実行]** をクリックします。 **時間範囲** 内に収集されたイベントが表示されます。

> [!NOTE]
> クエリからデータが返されない場合は、仮想マシンにイベントが作成されて収集されるまで数分待ってください。 さらに、新しくイベントのカテゴリを追加するには、データ収集ルールにデータ ソースの変更が必要になる場合もあります。

:::image type="content" source="media/tutorial-monitor-vm/log-analytics-query.png" lightbox="media/tutorial-monitor-vm/log-analytics-query.png" alt-text="Log Analytics とクエリの結果":::

Log Analytics を使用したログ データ分析のチュートリアルについては、[Log Analytics のチュートリアル](../logs/log-analytics-tutorial.md)を参照してください。 ログ データからのアラート ルール作成のチュートリアルについては、「[チュートリアル: Azure リソースのログ クエリ アラートを作成する](../alerts/tutorial-log-alert.md)」を参照してください。

## <a name="view-guest-metrics"></a>ゲストのメトリックを表示する
ホスト仮想マシンのメトリックは、[他のあらゆる Azure リソース](../essentials/tutorial-metrics.md)と同様、データ収集ルールがなくても、メトリックス エクスプローラーを使用して表示できます。 ただしデータ収集ルールがあると、メトリックス エクスプローラーを使用して、ホストのメトリックに加え、ゲストのメトリックも表示することができます。

仮想マシンのメニューから **[Metrics]\(メトリックス\)** を選択します。 スコープが仮想マシンに設定されたメトリックス エクスプローラーが開きます。 **[メトリック名前空間]** をクリックし、 **[Virtual Machine Guest]\(仮想マシンのゲスト\)** を選択します。 

> [!NOTE]
> **[Virtual Machine Guest]\(仮想マシンのゲスト\)** が表示されない場合は、エージェントがデプロイされてデータの収集が開始されるまで、あと数分待ってください。


:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer.png" lightbox="media/tutorial-monitor-vm/metrics-explorer.png" alt-text="メトリックス エクスプローラー":::

利用可能なゲスト メトリックが表示されます。 **[Metric]\(メトリック\)** を選択するとグラフが追加されます。

:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" lightbox="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" alt-text="メトリックス エクスプローラーとゲスト メトリック":::

メトリックス エクスプローラーを使用したメトリック データの確認と分析について、また、メトリック アラートの作成についての詳細なチュートリアルについては、それぞれ「[チュートリアル: Azure リソースのメトリックを分析する](../essentials/tutorial-metrics.md)」と「[チュートリアル: Azure リソースのメトリック アラート](../alerts/tutorial-metric-alert.md)」を参照してください。 



## <a name="next-steps"></a>次の手順
仮想マシンのゲスト メトリックを収集できるようになったので、使用可能なメモリや論理ディスク領域などのゲスト メトリックに基づくメトリック アラートを作成してみましょう。

> [!div class="nextstepaction"]
> [Azure Monitor でメトリック アラートを作成する](../alerts/tutorial-metric-alert.md)


