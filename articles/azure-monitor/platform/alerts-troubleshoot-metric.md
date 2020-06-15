---
title: Azure メトリック警告のトラブルシューティング
description: Azure Monitor のメトリック警告に関する一般的な問題と考えられる解決策。
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 04/28/2020
ms.subservice: alerts
ms.openlocfilehash: 605d1f550335417a26340b6ee54736321ad69f80
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84302664"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Azure Monitor のメトリック警告に関する問題のトラブルシューティング 

この記事では、Azure Monitor の[メトリック警告](alerts-metric-overview.md)に関する一般的な問題とそのトラブルシューティングの方法について説明します。

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 管理者は、その通知を見て、システムのユーザーが問題に気付く前に問題を識別して対処できます。 アラートの詳細については、「[Microsoft Azure のアラートの概要](alerts-overview.md)」を参照してください。

## <a name="metric-alert-should-have-fired-but-didnt"></a>メトリック警告が発生するはずだが発生しない 

メトリック警告が発生するはずなのに発生せず、Azure portal で見つからない場合は、次の手順を試してください。

1. **構成** - メトリック警告ルールの構成を調べて、適切に構成されていることを確認します。
    - **[集計の種類]** 、 **[集約粒度 (期間)]** 、および **[しきい値]** または **[秘密度]** が、意図したとおりに構成されていることを確認します
    - 動的しきい値が使用されている警告ルールの場合は、詳細設定が構成されているかどうかを確認します。 **[違反の数]** によって警告がフィルター処理され、 **[次よりも前のデータを無視します]** によってしきい値の計算方法が影響を受けることがあります

       > [!NOTE] 
       > 動的しきい値がアクティブになるためには、少なくとも 3 日の期間と、30 個のメトリック サンプルが必要です。

2. **発生したが通知されない** - [発生した警告の一覧](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)を調べて、発生した警告が見つかるかどうかを確認します。 一覧に警告があっても、そのアクションまたは通知の一部に問題がある場合は、[こちら](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-troubleshoot#action-or-notification-on-my-alert-did-not-work-as-expected)で詳細を参照してください。

3. **既にアクティブになっている** - 警告が発生すると予想されるメトリックに対して既に警告が発生しているかどうかを、メトリックの時系列で確認します。 メトリック警告はステートフルです。つまり、特定のメトリックの時系列で警告が発生すると、問題が検出されなくなるまで、その時系列でさらに警告が発生することはありません。 このような設計により、ノイズが減少します。 連続する 3 回の評価において警告条件が満たされない場合、警告は自動的に解決されます。

4. **使用されているディメンション** - [1 つのメトリックに対してディメンション値](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)を複数選択した場合、警告ルールでは、(ディメンション値の組み合わせによって定義される) メトリックの時系列ごとに、しきい値の違反が監視されます。 (ディメンションが選択されていない) 集計メトリック時系列も監視するには、ディメンションを選択せずに、メトリックに対する追加の警告ルールを構成します。

5. **集計と時間の粒度** - [メトリック チャート](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)を使用してメトリックを視覚化している場合は、次のことを確認します。
    * メトリック グラフで選択されている**集計**が、警告ルールの **[集計の種類]** と同じであること
    * 選択されている**時間粒度**が、警告ルールの **[集約粒度 (期間)]** と同であること (および、"自動" に設定されていないこと)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>メトリック警告が発生してはならないときに発生した

メトリック警告が発生してはならないことが確実な場合に発生した場合は、次の手順が問題の解決に役立つことがあります。

1. [発生した警告の一覧](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)を調べて発生した警告を見つけ、クリックしてその詳細を表示します。 **[Why did this alert fire?]\(この警告が発生した理由\)** で提供される情報を調べて、警告がトリガーされた時点でのメトリック グラフ、**メトリック値**、および**しきい値**を確認します。

    > [!NOTE] 
    > 動的しきい値条件の種類を使用しているとき、使用されているしきい値が正しくないと思われる場合は、問題点、改善点を報告するためのアイコンを使って、フィードバックをお寄せください。 このフィードバックは、機械学習のアルゴリズムの研究に影響を与え、将来の検出の改善に役立ちます。

2. 1 つの警告に複数のディメンション値を選択した場合は、(ディメンション値の組み合わせによって定義される) メトリック時系列の**いずれか**がしきい値を超えると警告がトリガーされます。 メトリック アラートでのディメンションの使用の詳細については、[こちら](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)を参照してください。

3. 警告ルールの構成を調べて、適切に構成されていることを確認します。
    - **[集計の種類]** 、 **[集約粒度 (期間)]** 、および **[しきい値]** または **[秘密度]** が、意図したとおりに構成されていることを確認します
    - 動的しきい値が使用されている警告ルールの場合は、詳細設定が構成されているかどうかを確認します。 **[違反の数]** によって警告がフィルター処理され、 **[次よりも前のデータを無視します]** によってしきい値の計算方法が影響を受けることがあります

   > [!NOTE]
   > 動的しきい値がアクティブになるためには、少なくとも 3 日の期間と、30 個のメトリック サンプルが必要です。

4. [メトリック グラフ](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)を使用してメトリックを視覚化している場合は、次の点を確認します。
    - メトリック グラフで選択されている**集計**が、警告ルールの **[集計の種類]** と同じであること
    - 選択されている**時間粒度**が、警告ルールの **[集約粒度 (期間)]** と同であること (および、"自動" に設定されていないこと)

5. 同じ条件を監視する (まだ解決されていない) 警告がまだ生成されている間に、警告が生成される場合は、警告ルールの構成で *autoMitigate* プロパティが **false** に設定されているかどうかを確認します (このプロパティは、REST、PowerShell、CLI でのみ構成できるので、その警告ルールのデプロイに使用したスクリプトを確認します)。 そうである場合は、生成された警告は警告ルールによって自動的に解決されず、再度生成される前に、生成されている警告が解決される必要はありません。


## <a name="cant-find-metric-to-alert-on---virtual-machines"></a>警告対象のメトリックが見つからない - 仮想マシン 

仮想マシンのゲスト メトリックスについての警告を作成するには (メモリ、ディスク領域など) は、Azure Monitor シンクにデータを送信するように診断設定を設定したことを確認します。
    * [Windows VM の場合](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)
    * [Linux VM の場合](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)
    
> [!NOTE] 
> Log Analytics ワークスペースに送信されるようにゲスト メトリックを構成した場合、これらのメトリックは Log Analytics ワークスペース リソースの下に表示され、それらを監視する警告ルールを作成した後で**のみ**データの表示が開始されます。 これを行うには、[ログのメトリック アラートを構成する](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#configuring-metric-alert-for-logs)手順に従います。

## <a name="cant-find-the-metric-to-alert-on"></a>警告対象のメトリックが見つからない

特定のメトリックについての警告を調べていて、リソースに対するメトリックが何も表示されない場合は、[リソースの種類がメトリック警告に対してサポートされているかどうかを確認します](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-near-real-time)。
リソースに対してメトリックがいくつか表示されるが、特定のメトリックが見つからない場合は、[そのメトリックが使用できるかどうかを確認し](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)、使用できる場合は、メトリックの説明を参照して、特定のバージョンまたはエディションのリソースでのみ使用できるかどうかを確認ます。

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>警告対象のメトリック ディメンションが見つからない

[メトリックの特定のディメンション値](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)について警告を作成しようとしているが、これらの値が見つからない場合は、次の点に注意してください。

1. ディメンション値が**ディメンション値**一覧に表示されるまでに数分かかる場合があります。
1. 表示されるディメンション値は、過去 3 日間に収集されたメトリック データに基づいています
1. ディメンションの値がまだ生成されていない場合は、[+] 記号をクリックしてカスタム値を追加します
1. ディメンションのすべての可能な値 (将来の値を含む) について警告を生成する場合は、[Select *] チェック ボックスをオンにします

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>削除されたリソースに対してメトリック警告ルールがまだ定義されている 

Azure リソースを削除しても、関連付けられているメトリック アラート ルールは自動的には削除されません。 削除されたリソースに関連付けられている警告ルールを削除するには、次のようにします。

1. 削除されたリソースが定義されたリソース グループを開きます。
1. リソースが表示されている一覧で、 **[非表示の型の表示]** チェックボックスをオンにします。
1. 型「**microsoft.insights/metricalerts**」で一覧をフィルター処理します。
1. 関連する警告ルールを選択し、 **[削除]** を選択します

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>条件が満たされるたびにメトリック警告が発生するようにする

メトリック警告は既定ではステートフルです。そのため、特定の時系列に対して既に警告が発生している場合、追加の警告は発生しません。 特定のメトリック警告ルールをステートレスにして、警告の条件が満たされるすべての評価で警告を受け取る場合は、プログラムで ([Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)、[PowerShell](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1)、[REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate)、[CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest) を使用するなどして) 警告ルールを作成し、*autoMitigate* プロパティを "False" に設定すします。

> [!NOTE] 
> メトリック警告ルールをステートレスにすると、発生した警告は解決されません。そのため、条件が満たされなくなっても、発生した警告は 30 日の保有期間まで発生状態のままになります。


## <a name="metric-alert-rules-quota-too-small"></a>メトリック警告ルールのクォータが小さすぎる

許可されるサブスクリプションあたりのメトリック警告ルール数は、[クォータ制限](https://docs.microsoft.com/azure/azure-monitor/service-limits)の対象になります。

クォータ制限に達した場合は、次の手順が問題の解決に役立つ場合があります。
1. 今後使用しないメトリック警告ルールを削除または無効にします。

2. 複数のリソースを監視するメトリック警告ルールを使用するように切り替えます。 この機能を使用すると、クォータに対してカウントされる警告ルールを 1 つだけ使用して、1 つの警告ルールで複数のリソースを監視できます。 この機能とサポートされているリソースの種類の詳細については、[複数の場合](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)を参照してください。

3. クォータ制限を増やす必要がある場合は、サポート リクエストを開き、次の情報を提供します。

    - クォータ制限を増やす必要があるサブスクリプション ID
    - クォータを引き上げるリソースの種類:**メトリック警告**または**メトリック警告 (クラシック)**
    - 要求されるクォータ制限

## <a name="check-total-number-of-metric-alert-rules"></a>メトリック警告ルールの合計数を確認する

メトリック警告ルールの現在の使用状況を確認するには、次の手順のようにします。

### <a name="from-the-azure-portal"></a>Azure portal から

1. **[アラート]** 画面を開き、 **[アラート ルールの管理]** をクリックします
2. **[サブスクリプション]** ドロップダウン コントロールを使用して、関連するサブスクリプションをフィルター処理します
3. 特定のリソース グループ、リソースの種類、またはリソースをフィルター処理しないようにしてください
4. **[シグナルの種類]** ドロップダウン コントロールで、 **[メトリック]** を選択します
5. **[状態]** ドロップダウン コントロールが **[有効]** に設定されていることを確認します
6. メトリック警告ルールの合計数がルールの一覧の上に表示されます

### <a name="from-api"></a>API から

- PowerShell - [Get-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API - [サブスクリプションごとの一覧](https://docs.microsoft.com/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Resource Manager テンプレート、REST API、PowerShell、または Azure CLI を使用して警告ルールを管理する

Resource Manager テンプレート、REST API、PowerShell、または Azure コマンド ライン インターフェイス (CLI) を使用してメトリック警告を作成、更新、取得、削除するときに問題が発生する場合は、次の手順が問題の解決に役立つ可能性があります。

### <a name="resource-manager-templates"></a>Resource Manager テンプレート

- [一般的な Azure デプロイ エラー](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors)の一覧を確認して、適宜トラブルシューティングします。
- [メトリック警告の Azure Resource Manager テンプレートの例](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)を参照して、すべてのパラメーターが確実かつ適切に渡されることを確認します

### <a name="rest-api"></a>REST API

[REST API ガイド](https://docs.microsoft.com/rest/api/monitor/metricalerts/)を確認して、すべてのパラメーターが確実かつ適切に渡されることを確かめます

### <a name="powershell"></a>PowerShell

メトリック警告に適切な PowerShell コマンドレットを使用していることを確認します。

- メトリック アラート用の PowerShell コマンドレットは [Az. Monitor モジュール](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1)で使用できます
- 新しい (クラシックではない) メトリック警告については、必ず V2 で終了するコマンドレットを使用してください ([Add-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/Add-AzMetricAlertRuleV2?view=azps-3.6.1) など)

### <a name="azure-cli"></a>Azure CLI

メトリック警告に適切な CLI コマンドを使用していることを確認します。

- メトリック アラートの CLI コマンドは `az monitor metrics alert` で始まります。 構文については、 [Azure CLI リファレンス](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)を参照してください。
- [メトリック アラート CLI の使用方法を示すサンプル](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli)をご覧いただけます
- カスタム メトリックに対してアラートを作成するには、メトリック名の前に、関連するメトリック名前空間を付ける必要があります。NAMESPACE.METRIC

### <a name="general"></a>全般

- `Metric not found` エラーが発生している場合は、次のようにします。

   - プラットフォーム メトリックの場合: **メトリックの表示名**ではなく、[Azure Monitor でサポートされているメトリックのページ](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)の**メトリック**名を使用していることを確認します

   - カスタム メトリックの場合: メトリックが既に出力されていること (まだ存在していないカスタム メトリックに対して警告ルールを作成することはできないため)、およびカスタム メトリックの名前空間を指定していることを確認します ([こちら](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)の ARM テンプレートの例を参照)

- [ログに関するメトリック アラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs)を作成する場合は、適切な依存関係が含まれていることを確認します。 [サンプル テンプレート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#resource-template-for-metric-alerts-for-logs)を参照してください。

- 複数の条件を含む警告ルールを作成する場合は、次の制約に注意してください。

   - 各条件内では、ディメンションごとに 1 つの値のみを選択できます。
   - "\*" をディメンション値として使用することはできません。
   - 異なる条件で構成されている複数のメトリックで同じディメンションがサポートされている場合、構成されているディメンションの値は、それらすべてのメトリックに対して同じ方法で明示的に設定されている必要があります ([こちら](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)の Resource Manager テンプレートの例を参照)


## <a name="no-permissions-to-create-metric-alert-rules"></a>メトリック警告ルールを作成するためのアクセス許可がない

メトリック警告ルールを作成するには、次のアクセス許可が必要です。

- 警告ルールのターゲット リソースに対する読み取りアクセス許可
- 警告ルールが作成されるリソース グループに対する書き込みアクセス許可 (Azure portal から警告ルールを作成する場合、警告ルールはターゲット リソースが存在するのと同じリソース グループに作成されます)
- 警告ルールに関連付けられているアクション グループに対する読み取りアクセス許可 (該当する場合)

## <a name="next-steps"></a>次のステップ

- 警告と通知に関する一般的なトラブルシューティング情報については、「[Azure Monitor のアラートの問題のトラブルシューティング](alerts-troubleshoot.md)」を参照してください。
