---
title: Log Analytics についてよく寄せられる質問 | Microsoft Docs
description: Azure Monitor Log Analytics サービスについてよく寄せられる質問とその回答
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/01/2019
ms.openlocfilehash: 77159e0fa73a1f56688c867c55ae46f28016992c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394783"
---
# <a name="log-analytics-faq"></a>Log Analytics についてよく寄せられる質問

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

この Microsoft FAQ は、Azure Monitor Log Analytics ワークスペースについてよく寄せられる質問の一覧です。 Log Analytics に関して何か追加の質問がある場合は、[ディスカッション フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。


## <a name="new-logs-experience"></a>新しいログ エクスペリエンス

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>Q:新しいログ エクスペリエンスと Log Analytics の違いは何ですか?

A:これらは同じものです。 [Log Analytics は Azure Monitor の機能として統合され](../../azure-monitor/azure-monitor-rebrand.md)、より統一した監視エクスペリエンスが提供されます。 Azure Monitor の新しいログ エクスペリエンスは、多くのお客様が既に使用している Log Analytics クエリとまったく同じものです。

### <a name="q-can-i-still-use-log-search"></a>Q:ログ検索は引き続き使用できますか? 

A:現時点では、ログ検索は OMS ポータルや Azure portal で**ログ (クラシック)** という名前で引き続き使用できます。 OMS ポータルは、2019 年 1 月 15 日に正式に廃止される予定です。 Azure portal のクラシック ログ エクスペリエンスは段階的に廃止され、新しいログ エクスペリエンスに置き換えられます。 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>Q. 高度な分析ポータルは引き続き使用できますか? 
Azure portal の新しいログ エクスペリエンスは高度な分析ポータルに基づいていますが、Azure portal 外から引き続きアクセスできます。 この外部ポータルの廃止に関するロードマップはまもなく発表されます。

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>Q. 新しいログ エクスペリエンスでクエリ エクスプローラーと [保存] ボタンを表示できないのはなぜですか?

特定のリソースのコンテキストでログを検索する場合、**クエリ エクスプローラー**、 **[保存]** ボタンおよび **[警告の設定]** ボタンは使用できません。 アラートを作成し、クエリを保存または読み込むには、ログのスコープをワークスペースに指定する必要があります。 ワークスペース コンテキストでログを開くには、 **[すべてのサービス]**  >  **[監視]**  >  **[ログ]** の順に選択します。 最後に使用されたワークスペースが選択されますが、その他のワークスペースを選択することはできます。 詳細については、「[Log Analytics におけるデータの表示と分析](../log-query/portals.md)」を参照してください。

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>Q. 新しいログ エクスペリエンスではカスタム フィールドをどのように抽出するのですか? 

A:現在、カスタム フィールドの抽出はクラシック ログ エクスペリエンスでサポートされています。 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>Q. 新しいログのリスト ビューはどこにありますか? 

A:リスト ビューは新しいログではご利用いただけません。 結果テーブルの各レコードの左側に矢印があります。 この矢印をクリックすると、そのレコードの詳細が表示されます。 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>Q. クエリの実行後、推奨されるフィルターの一覧が使用できるようになります。 どうすればフィルターを表示できますか? 

A:左ウィンドウで [フィルター] をクリックすると、新しいフィルター実装のプレビューが表示されます。 現在、これは完全な結果セットに基づいており、UI のレコードの上限数 (10,000) によって制限されません。 現時点では、これが最もよく使用されているフィルターのリストであり、フィルターごとに最も一般的な値が 10 個表示されます。 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>Q. エラーが発生する理由:VM からドリルインした後、ログに "このサブスクリプションのリソース プロバイダー 'Microsoft.Insights' を登録してこのクエリを有効にしてください" というエラーが表示されるのはなぜですか? 

A:既定では、多くのリソース プロバイダーが自動的に登録されます。ただし、一部のリソース プロバイダーは手動で登録することが必要な場合があります。 これにより、サブスクリプションがリソース プロバイダーと連携するように構成されます。 登録の範囲は常にサブスクリプションです。 詳細については、「[リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」を参照してください。

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>Q. VM ページからログにアクセスするときに、アクセス権なしというエラー メッセージが表示されるのはなぜですか? 

A:VM ログを表示するには、VM ログを格納するワークスペースに対する読み取りアクセス許可が付与される必要があります。 このような場合、管理者が Azure でのアクセス許可を付与する必要があります。

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>Q. OMS ポータルで自分のワークスペースにはアクセスできますが、Azure portal で "アクセス権がありません" というエラーが表示されるのはなぜですか?  

A:Azure でワークスペースにアクセスするには、Azure のアクセス許可が割り当てられている必要があります。 場合によっては、適切なアクセス許可がないことがあります。 このような場合は、管理者が Azure のアクセス許可を付与する必要があります。詳細については、「[OMS ポータルの Azure への移行](oms-portal-transition.md)」を参照してください。

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>Q. ログでビュー デザイナー エントリを表示できないのはなぜですか?

A:ビュー デザイナーは、共同作成者以上のアクセス許可が割り当てられているユーザーのみがログで使用できます。

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>Q. Azure の外部で引き続き Analytics ポータルを使用できますか?

A. できます。Azure でのログのページと、高度な分析ポータルは同じコードに基づいています。 Log Analytics は Azure Monitor の機能として統合され、より統一した監視エクスペリエンスが提供されます。 次の URL を使用して引き続き Analytics ポータルにアクセスできます: https:\/\/portal.loganalytics.io/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/workspaces/{workspaceName}。



## <a name="general"></a>全般

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>Q. Azure portal で自分のビューとソリューションを表示するにはどうすればよいですか? 

A:Azure portal には、ビューとインストールされているソリューションのリストが表示されます。 **[すべてのサービス]** をクリックします。 リソースのリストで、 **[監視]** を選択してから **[...詳細]** をクリックします。 最後に使用されたワークスペースが選択されますが、その他のワークスペースを選択することはできます。 

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Q. Log Analytics で使用されるエージェントは Azure Security Center のエージェントと同じですか?

A:2017 年 6 月上旬より、Azure Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行っています。 詳細については、[Azure Security Center のプラットフォームの移行についての FAQ](../../security-center/security-center-enable-data-collection.md) に関するページを参照してください。

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Q. AD および SQL Assessment ソリューションでは、どのようなチェックが行われますか?

A:次のクエリは、現在実行されているすべてのチェックの説明を示します。

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

結果を Excel にエクスポートしてレビューすることができます。

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Q. System Center Operations Manager コンソールに OMS とは異なる内容が表示されるのはなぜですか?

A:使用している Operations Manager の更新プログラム ロールアップに応じて、*System Center Advisor*、*Operational Insights*、または *Log Analytics* のノードが表示される可能性があります。

管理パックでは *OMS* のテキスト文字列が更新されており、手動でインポートする必要があります。 現在のテキストと機能を表示するには、最新の System Center Operations Manager 更新プログラム ロールアップ KB 記事の手順に従い、コンソールを更新してください。

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Q:オンプレミス バージョンの Log Analytics はありますか?

A:いいえ。 Log Analytics は、大量のデータを処理および格納するスケーラブルなクラウド サービスです。 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Q. データ収集が停止したときに通知を受けるにはどうすればよいですか?

A:データ収集が停止したときに通知を受けるには、[新しいログ アラートの作成](../../azure-monitor/platform/alerts-metric.md)に関する記事で説明されている手順を使用します。

データ収集が停止したときのアラートを作成する場合は、次のように設定します。

- **[アラートの条件を定義します]** では、リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** では、以下を指定します。
   - **[シグナル名]** では、 **[カスタム ログ検索]** を選択します。
   - **[検索クエリ]** : `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **[アラート ロジック]** は "*結果の数*" **に基づき、** **[条件]** は**しきい値**の *0* "*より大きい*" です
   - **[期間]** は *30* 分、 **[アラートの頻度]** は *10* 分間隔に設定します
- **[アラートの詳細を定義します]** では、以下を指定します。
   - **[名前]** を *[Data collection stopped] \(データ収集が停止した)* に
   - **[重大度]** : *[警告]*

既存の[アクション グループ](../../azure-monitor/platform/action-groups.md)を指定するか、アクション グループを新たに作成して、ログ アラートが条件に一致する場合に、ハートビートが 15 分以上なければ通知が送られるようにします。

## <a name="configuration"></a>構成

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Q. Azure Diagnostics (WAD) からの読み取りに使用されるテーブル/BLOB コンテナーの名前は変更できますか?

A. いいえ。Azure ストレージ内の任意のテーブルまたはコンテナーから読み取ることは現在不可能です。

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Q. Log Analytics サービスはどのような IP アドレスを使用しますか? ファイアウォールが確実に Log Analytics サービスへのトラフィックのみを許可するようにするにはどうすればよいですか?

A. Log Analytics サービスは、Azure に基づいて構築されています。 Log Analytics の IP アドレスは、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内にあります。

サービスのデプロイが行われると、Log Analytics サービスの実際の IP アドレスは変更されます。 ファイアウォールの通過を許可する DNS 名については、[ネットワーク要件](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)に関するセクションをご覧ください。

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Q. Azure への接続に ExpressRoute を使用しています。 Log Analytics トラフィックは、この ExpressRoute 接続を使用しますか?

A. 各種の ExpressRoute トラフィックについては、[ExpressRoute のドキュメント](../../expressroute/expressroute-faqs.md#supported-services)を参照してください。

Log Analytics へのトラフィックには、パブリックピアリング ExpressRoute 回路が使用されます。

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Q. 既存の Log Analytics ワークスペースを別の Log Analytics ワークスペース/Azure サブスクリプションに移動するためのシンプルで簡単な方法はありますか?

A. `Move-AzResource` コマンドレットを使用すると、Log Analytics ワークスペースを移動できるほか、Automation アカウントを Azure サブスクリプション間で移動することもできます。 詳細については、「[Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx)」を参照してください。

この変更は、Azure ポータルで行うこともできます。

ある Log Analytics ワークスペースから別のワークスペースにデータを移行したり、Log Analytics データが格納されているリージョンを変更したりすることはできません。

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Q:System Center Operations Manager に Log Analytics を追加するにはどうすればよいですか?

A:最新の更新プログラム ロールアップに更新し、管理パックをインポートすると、Operations Manager を Log Analytics に接続できます。

>[!NOTE]
>Log Analytics への Operations Manager 接続は、System Center Operations Manager 2012 SP1 以降でのみ使用できます。

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Q:エージェントが Log Analytics と通信できることを確認するにはどうすればよいですか?

A:エージェントが Log Analytics ワークスペースと通信できることを確認するには、次のようにします。[コントロール パネル]、[システムとセキュリティ]、 **[Microsoft Monitoring Agent]** の順に選択します。

**[Azure Log Analytics (OMS)]** タブの緑色のチェック マークを確認します。 緑色のチェック マーク アイコンは、エージェントが Azure サービスと通信できることを示します。

黄色の警告アイコンは、エージェントと Log Analytics の通信に問題が発生していることを示します。 1 つの一般的な原因は、Microsoft Monitoring Agent サービスが停止したことです。 サービス コントロール マネージャーを使用してサービスを再開します。

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Q:エージェントの Log Analytics との通信を停止するにはどうすればよいですか?

A:System Center Operations Manager では、Log Analytics マネージド コンピューターの一覧からコンピューターを削除します。 Operations Manager はエージェントの構成を更新して、Log Analytics に報告しなくなるようにします。 Log Analytics に直接接続されているエージェントの場合は、次の手順でエージェントの通信を停止できます。[コントロール パネル]、[システムとセキュリティ]、 **[Microsoft Monitoring Agent]** の順に選択します。
**[Azure Log Analytics (OMS)]** に表示されているすべてのワークスペースを削除します。

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Q:ワークスペースを Azure サブスクリプション間で移動しようとするとエラーが表示されるのはなぜですか?

A:ワークスペースを別のサブスクリプションまたはリソース グループに移動するには、まず、ワークスペースで Automation アカウントのリンクを解除する必要があります。 ワークスペースに次のソリューションがインストールされている場合、Automation アカウントのリンクを解除するには、これらのソリューションを削除する必要があります:Update Management、Change Tracking、または Start/Stop VMs during off-hours を削除します。 これらのソリューションの削除後、Automation アカウント リソースの左側のウィンドウで **[リンクされたワークスペース]** を選択し、リボンの **[ワークスペースのリンクの解除]** をクリックして、Automation アカウントのリンクを解除します。
 > 移動後、削除したソリューションはワークスペースに再インストールする必要があり、ワークスペースの Automation リンクは再度指定する必要があります。

両方の Azure サブスクリプションにアクセス許可があることを確認してください。

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>Q:SavedSearch を更新しようとするとエラーが発生するのはなぜですか。

A:API の本文、または Azure Resource Manager テンプレートのプロパティに、"etag" を追加する必要があります。
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>エージェント データ
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Q. エージェント経由で Log Analytics に送信できるデータ量はどのくらいですか? 顧客ごとの最大データ量はありますか?
A. アップロードされるデータの量に制限はなく、お客様が選択した価格オプション (容量予約または従量課金制) に基づきます。 Log Analytics ワークスペースは、お客様から送信される量が 1 日あたり数 TB であったとしても、それを処理するように自動的にスケールアップするように設計されています。 詳細については、[価格の詳細](https://azure.microsoft.com/pricing/details/monitor/)をご覧ください。

Log Analytics エージェントは、小さなフットプリントを保証するように設計されました。 データ量は、有効にしているソリューションによって異なります。 [[使用状況]](../../azure-monitor/platform/data-usage.md) ページでデータ量に関する詳細情報を見つけたり、ソリューションごとの内訳を確認したりできます。

詳しくは、Log Analytics エージェントのリソース使用率 (フットプリント) の評価結果が示されている[お客様のブログ](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html)をご覧ください。

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Q. データを Log Analytics に送信するときに、どれくらいのネットワーク帯域幅が Microsoft Management Agent (MMA) によって使用されますか?

A. 帯域幅は、送信されるデータ量に関する機能です。 データは、ネットワーク経由で送信されるときに圧縮されます。

### <a name="q-how-much-data-is-sent-per-agent"></a>Q. エージェントごとにどれくらいのデータが送信されますか?

A. エージェントごとに送信されるデータ量は、次のものによって異なります。

* 有効にしているソリューション
* 収集されているログとパフォーマンス カウンターの数
* ログ内のデータ量

全体的な使用状況は [[使用状況]](../../azure-monitor/platform/data-usage.md) ページに表示されます。

WireData エージェントを実行できるコンピューターの場合、どれくらいのデータが送信されているかを確認するには次のクエリを使用します。

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>次のステップ

「[Azure Monitor の概要](../../azure-monitor/overview.md)」で Log Analytics の詳細を確認し、すぐに起動して実行してみることができます。
