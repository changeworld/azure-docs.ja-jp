---
title: Azure Update Management ログにクエリを実行する
description: この記事では、Log Analytics ワークスペースで Update Management のログに対してクエリを実行する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 01/10/2020
ms.topic: conceptual
ms.openlocfilehash: 5a1979b0e714f35694999c04e1f890b710d54ac9
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867068"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Azure Monitor Logs で Update Management の更新レコードに対してクエリを実行する

Update Management ソリューションで提供される詳細に加えて、Log Analytics ワークスペースに格納されているログを検索することができます。 ソリューション ページの左側のペインで、 **[ログ]** を選択します。 **[ログ検索]** ページが開きます。

クエリをカスタマイズする方法や、さまざまなクライアントから使用する方法などについては、[Log Analytics の検索 API のドキュメント](https://dev.loganalytics.io/)を参照してください。

## <a name="update-records"></a>Update レコード

Windows および Linux VM の Update Management によって収集されるレコードと、ログ検索結果に表示されるデータの種類。 以下のセクションで、これらのレコードについて説明します。

### <a name="required-updates"></a>必要な更新プログラム

コンピューターで必要な更新プログラムを表す `RequiredUpdate` の種類のレコードが作成されます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 | 
|----------|-------------|
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| KBID | Windows Update のサポート技術情報の記事 ID。 |
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 | 
| Product | 更新プログラムが適用される製品。 | 
| PublishDate | Windows Update から更新プログラムをダウンロードしてインストールする準備ができた日付。 |
| サーバー | | 
| SourceHealthServiceId | Log Analytics Windows エージェント ID を表す一意識別子。 |
| SourceSystem | *OperationsManager* | 
| TenantId | Azure Active Directory の組織のインスタンスを表す一意識別子。 | 
| TimeGenerated | レコードが作成された日付と時刻。 | 
| 種類 | *アップデート* | 
| UpdateClassification | 適用できる更新プログラムの種類を示します。 Windows の場合:<br> *緊急更新プログラム*<br> *セキュリティ更新プログラム*<br> *更新プログラムのロールアップ*<br> *Feature Pack*<br> *Service Pack*<br> *定義ファイルの更新*<br> *ツール*<br> *[Updates]* (更新)。 Linux の場合:<br> *重要な更新プログラムとセキュリティ更新プログラム*<br> *その他* |
| UpdateSeverity | 脆弱性の重大度の評価。 値は次のとおりです。<br> *重大*<br> *重要*<br> *中*<br> *低* |
| UpdateTitle | 更新プログラムのタイトル。|

### <a name="update"></a>更新

`Update` という種類のレコードが作成されます。これは、使用可能な更新プログラムと、コンピューターでのそのインストール状態を表します。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 | 
|----------|-------------|
| ApprovalSource | Windows オペレーティング システムにのみ適用されます。 値は "*Microsoft Update*" です。 |
| Approved | "*True*" または "*False*" |
| 分類 | *更新プログラム* |
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| ComputerEnvironment | "*Azure*" または "*Non-Azure*"。 |
| MSRCBulletinID | セキュリティ情報 ID 番号 | 
| MSRCSeverity | 脆弱性の重大度の評価。 値は次のとおりです。<br> *重大*<br> *重要*<br> *中*<br> *低* |  
| KBID | Windows Update のサポート技術情報の記事 ID。 |
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| UpdateID | ソフトウェア更新プログラムの一意識別子。 |
| RevisionNumber | 更新プログラムの特定のリビジョンのリビジョン番号。 |
| 省略可能 | "*True*" または "*False*" | 
| RebootBehavior | 更新プログラムをインストールまたはアンインストールした後の再起動動作。 |
| _ResourceId | レコードが関連付けられているリソースの一意識別子。 |
| 種類 | *アップデート* |
| VMUUID | 仮想マシンの一意識別子。 |
| MG | 管理グループまたは Log Analytics ワークスペースの一意識別子。 | 
| TenantId | Azure Active Directory の組織のインスタンスを表す一意識別子。 | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | レコードが作成された日付と時刻。 | 
| SourceComputerId | ソース コンピューターを表す一意識別子。 | 
| タイトル | 更新プログラムのタイトル。 |
| PublishedDate (UTC) | Windows Update から更新プログラムをダウンロードしてインストールする準備ができた日付。  |
| UpdateState | 更新プログラムの現在の状態。 | 
| Product | 更新プログラムが適用される製品。 |
| SubscriptionId | Azure サブスクリプションの一意識別子。 | 
| ResourceGroup | リソースがメンバーであるリソース グループの名前。 | 
| ResourceProvider | リソース プロバイダーを指定します。 | 
| リソース | リソースの名前。 | 
| ResourceType | リソースの種類の名前。 | 

### <a name="update-agent"></a>更新エージェント

コンピューター上の更新エージェントの詳細を提供する `UpdateAgent` の種類のレコードが作成されます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| OSVersion | オペレーティング システムのバージョン。 |
| サーバー | |
| SourceHealthServiceId | Log Analytics Windows エージェント ID を表す一意識別子。 |
| SourceSystem | *OperationsManager* | 
| TenantId | Azure Active Directory の組織のインスタンスを表す一意識別子。 |
| TimeGenerated | レコードが作成された日付と時刻。 |
| 種類 | *アップデート* | 
| WindowsUpdateAgentVersion | Windows Update エージェントのバージョン。 |
| WSUSServer | Windows Update エージェントにトラブルシューティングに役立つ問題がある場合は、エラーが表示されます。 |

### <a name="update-deployment-status"></a>更新プログラムのデプロイの状態 

コンピューターごとにスケジュールされたデプロイの更新プログラムのデプロイの状態を提供する、`UpdateRunProgress` の種類のレコードが作成されます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 | 
|----------|-------------|
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| ComputerEnvironment | "*Azure*" または "*Non-Azure*"。 | 
| CorrelationId | 更新プログラムに対して実行される Runbook ジョブの一意識別子。 |
| EndTime | 同期プロセスが終了した時刻。 | 
| ErrorResult | 更新プログラムのインストールに失敗した場合に生成される Windows Update のエラー コード。 | 
| InstallationStatus | クライアント コンピューター上の更新プログラムの考えられるインストール状態 ("*実行中*"、"*成功*"、"*部分的に失敗*")。 |
| KBID | Windows Update のサポート技術情報の記事 ID。 | 
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| OSType | オペレーティング システムの種類 ("*Windows*" または "*Linux*") を指定します。 | 
| Product | 更新プログラムが適用される製品。 |
| リソース | リソースの名前。 | 
| ResourceId | レコードが関連付けられているリソースの一意識別子。 |
| ResourceProvider | リソース プロバイダーを指定します。 | 
| ResourceType | リソースの種類の名前。 | 
| SourceComputerId | ソース コンピューターを表す一意識別子。 | 
| SourceSystem | *OperationsManager* |
| StartTime | 更新プログラムのインストールがスケジュールされている時刻。 |
| SubscriptionId | Azure サブスクリプションの一意識別子。 | 
| SucceededOnRetry | 最初の試行で更新プログラムの実行がいつ失敗したかと、現在の操作が再試行であることを示します。 |
| TimeGenerated | レコードが作成された日付と時刻。 |
| タイトル | 更新プログラムのタイトル。 |
| 種類 | *UpdateRunProgress* |
| UpdateId | ソフトウェア更新プログラムの一意識別子。 |
| VMUUID | 仮想マシンの一意識別子。 |
| _ResourceId | レコードが関連付けられているリソースの一意識別子。 |

### <a name="update-summary"></a>概要の更新 

コンピューターごとの更新の概要を提供する `UpdateSummary` の種類のレコードが作成されます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 | 
|----------|-------------|
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| ComputerEnvironment | "*Azure*" または "*Non-Azure*"。 | 
| CriticalUpdatesMissing | 適用可能だが、インストールされていない重要な更新プログラムの数。 | 
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| NETRuntimeVersion | Windows コンピューターにインストールされている .NET Framework のバージョン。 |
| OldestMissingSecurityUpdateBucket | | 
| OldestMissingSecurityUpdateInDays | |
| OsVersion | オペレーティング システムのバージョン。 |
| OtherUpdatesMissing | インストールされていない更新プログラムの検出数。 |
| リソース |  リソースの名前。 | 
| ResourceGroup | リソースがメンバーであるリソース グループの名前。 |
| ResourceId | レコードが関連付けられているリソースの一意識別子。 |
| ResourceProvider | リソース プロバイダーを指定します。 |
| ResourceType | リソースの種類の名前。 |
| RestartPending | *True* または *False* です。 |
| SecurityUpdatesMissing | 適用可能だが、インストールされていないセキュリティ更新プログラムの数。| 
| SourceComputerId | 仮想マシンの一意識別子。 |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Azure サブスクリプションの一意識別子。 |
| TimeGenerated | レコードが作成された日付と時刻。 |
| TotalUpdatesMissing | 適用可能だが、インストールされていない更新プログラムの合計数。 | 
| 種類 | *UpdateSummary* |
| VMUUID | 仮想マシンの一意識別子。 |
| WindowsUpdateAgentVersion | Windows Update エージェントのバージョン。 |
| WindowsUpdateSetting | Windows Update エージェントの状態を表示します。 次のいずれかの値になります。<br> "*Scheduled installation*" (スケジュールに従ってインストールする)<br> "*Notify before installation*" (インストールする前に通知する)<br> 異常な WUA エージェントからはエラーが返されます。 | 
| WSUSServer | Windows Update エージェントにトラブルシューティングに役立つ問題がある場合は、エラーが表示されます。 |
| _ResourceId | レコードが関連付けられているリソースの一意識別子。 |

## <a name="sample-queries"></a>サンプル クエリ

以下のセクションは、Update Management に関して収集された更新レコードのログ クエリの例です。

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Azure 以外のマシンが配布準備済みであることを確認する

直接接続されたマシンが Azure Monitor ログと通信していることを確認するには、次のいずれかのログ検索を実行します。

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Windows コンピューターでは、次の情報を調べて、Azure Monitor ログとエージェントの接続を確認できます。

1. [コントロール パネル] から **[Microsoft Monitoring Agent]** を開きます。 **[Azure Log Analytics]** タブで、エージェントに"**Microsoft Monitoring Agent は Log Analytics に正常に接続しました**" というメッセージが表示されます。
2. Windows イベント ログを開きます。 **アプリケーションとサービス ログ\Operations Manager** に移動して、ソースの **[サービス コネクタ]** でイベント ID 3000 および 5002 を検索します。 これらのイベントは、コンピューターが Log Analytics ワークスペースに登録され、構成を受信していることを示しています。

エージェントが Azure Monitor ログと通信できず、ファイアウォールまたはプロキシ サーバーを介してインターネットと通信するよう構成されている場合は、ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認します。 ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認する方法については、[Windows エージェントのネットワーク構成](../azure-monitor/platform/agent-windows.md)または [Linux エージェントのネットワーク構成](../log-analytics/log-analytics-agent-linux.md)に関する記事を参照してください。

> [!NOTE]
> Linux システムがプロキシまたは Log Analytics ゲートウェイと通信するよう構成されており、このソリューションの配布準備を行っている場合は、次のコマンドを実行し、ファイルに対する読み取り権限を omiuser グループに付与するよう、*proxy.conf* のアクセス許可を更新します。
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

新しく追加された Linux エージェントは、評価が完了した後、状態が "**更新済み**" と表示されます。 このプロセスには最大で 6 時間かかります。

Operations Manager 管理グループが Azure Monitor ログと通信していることを確認する方法については、[Operations Manager と Azure Monitor ログの統合の検証](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)に関するページを参照してください。

### <a name="single-azure-vm-assessment-queries-windows"></a>単一の Azure VM 評価クエリ (Windows)

VMUUID 値を、クエリの対象である仮想マシンの VM GUID に置き換えます。 Azure Monitor ログで次のクエリを実行することで、使用する必要がある VMUUID を確認できます。`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>単一の Azure VM 評価クエリ (Linux)

Linux のディストリビューションによっては、Azure Resource Manager に由来する VMUUID 値と、Azure Monitor ログに格納されている値との間で[エンディアン](https://en.wikipedia.org/wiki/Endianness)が一致しない場合があります。 次のクエリは、いずれかのエンディアンでの一致をチェックします。 結果を適切に返すために、VMUUID 値を GUID のビッグエンディアン形式とリトルエンディアン形式に置き換えます。 Azure Monitor ログで次のクエリを実行することで、使用する必要がある VMUUID を確認できます。`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>マルチ VM の評価クエリ

#### <a name="computers-summary"></a>コンピューターの概要

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

#### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>コンピューター一覧

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログ](../log-analytics/log-analytics-log-searches.md)のログ検索を使用して、詳細な更新データを確認します。
* 更新プログラムのデプロイの状態に関する[アラートを作成](automation-tutorial-update-management.md#configure-alerts)します。
