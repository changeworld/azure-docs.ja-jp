<properties 
   pageTitle="Log Analytics のアラート管理ソリューション | Microsoft Azure"
   description="管理対象となる環境内のすべてのアラートは、Log Analytics のアラート管理ソリューションを使用して分析できます。OMS 内で生成されたアラートを一元管理できるほか、接続されている System Center Operations Manager (SCOM) 管理グループからのアラートを Log Analytics にインポートすることができます。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/12/2016"
   ms.author="bwren" />

# Log Analytics のアラート管理ソリューション

管理対象となる環境内のすべてのアラートは、アラート管理ソリューションを使用して分析できます。OMS 内で生成されたアラートを一元管理できるほか、接続されている System Center Operations Manager (SCOM) 管理グループからのアラートを Log Analytics にインポートすることができます。複数の管理グループが存在する環境では、すべての管理グループのアラートをアラート管理ソリューションによって一元的に把握することができます。

![Alert Management icon](media/log-analytics-solution-alert-management/icon.png)

## 前提条件

- このソリューションで SCOM からアラートをインポートするためには、「[Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」に記載されている手順に従って、OMS ワークスペースと SCOM 管理グループとを接続する必要があります。  


## 構成

[ソリューションの追加](log-analytics-add-solutions.md)に関するページの手順に従って、アラート管理ソリューションを OMS ワークスペースに追加します。さらに手動で構成する必要はありません。

## 管理パック

SCOM 管理グループが OMS ワークスペースに接続されている場合、このソリューションを追加したときに次の管理パックが SCOM にインストールされます。これらの管理パックに伴う構成や保守は不要です。

- Microsoft System Center Advisor Alert Management (Microsoft.IntelligencePacks.AlertManagement)

ソリューション管理パックの更新方法の詳細については、「[Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」を参照してください。

## データ収集

### エージェント

次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続されているソース | サポート | 説明 |
|:--|:--|:--|
| [Windows エージェント](log-analytics-windows-agents.md) | いいえ | 直接の Windows エージェントでは、SCOM アラートは生成されません。 |
| [Linux エージェント](log-analytics-limux-agents.md) | いいえ | 直接の Linux エージェントでは、SCOM アラートは生成されません。 |
| [SCOM 管理グループ](log-analytics-om-agents.md) | あり | SCOM エージェントで生成されたアラートは管理グループに配信された後、Log Analytics に転送されます。<br><br>SCOM エージェントから Log Analytics への直接接続は不要です。アラート データは管理グループから OMS リポジトリに転送されます。 |
| [Azure ストレージ アカウント](log-analytics-azure-storage.md) | いいえ | SCOM のアラートは Azure ストレージ アカウントに保存されません。 |

### 収集の頻度

OMS 内で生成されたアラートはすぐにアラート管理ソリューションから利用できるようになります。アラート データは 3 分おきに SCOM 管理グループから Log Analytics に送信されます。

## ソリューションの使用

OMS ワークスペースにアラート管理ソリューションを追加すると、OMS ダッシュボードに **[Alert Management]** (アラート管理) タイルが追加されます。このタイルには、過去 24 時間に生成された現在アクティブなアラートの数とグラフが表示されます。この時間範囲を変更することはできません。

![Alert Management tile](media/log-analytics-solution-alert-management/tile.png)

**[Alert Management]** (アラート管理) タイルをクリックすると、**[Alert Management]** (アラート管理) ダッシュボードが表示されます。ダッシュボードには、次の表に示した列が存在します。それぞれの列には、特定のスコープと時間範囲について、その列の基準に該当するアラート数の上位 10 件が表示されます。ログ検索を実行してアラート全件を取得するには、列の一番下にある **[See all]** (すべて表示) をクリックするか、列ヘッダーをクリックします。

| 分割| 説明 |
|:--|:--|
| 重大なアラート | 重大度が "重大" であるすべてのアラートがその名前別に表示されます。アラート名をクリックするとログの検索が実行され、そのアラートに該当するすべてのレコードが返されます。 |
| 警告アラート | 重大度が "警告" であるすべてのアラートがその名前別に表示されます。アラート名をクリックするとログの検索が実行され、そのアラートに該当するすべてのレコードが返されます。 |
| アクティブ SCOM アラート | *[Closed]* (終了) 状態を除くすべての SCOM アラートが、その生成元ごとにグループ化されて表示されます。 |
| すべてのアクティブなアラート | 重大度に関係なくすべてのアラートがその名前別に表示されます。対象となるのは *[Closed]* (終了) 状態以外の SCOM アラートだけです。|

右へスクロールすると、使用頻度の高いいくつかのクエリがダッシュボードに一覧表示されます。そのクエリをクリックすると、アラート データを探すための[ログの検索](log-analytics-log-searches.md)が実行されます。

![Alert Management dashboard](media/log-analytics-solution-alert-management/dashboard.png)

## スコープと時間範囲

既定では、アラート管理ソリューションの分析は、過去 7 日間に生成された接続されているすべての管理グループから収集されたアラートが対象となります。

![Alert Management scope](media/log-analytics-solution-alert-management/scope.png)

- 分析の対象となる管理グループを変更するには、ダッシュボードの上部にある **[Scope]** (スコープ) をクリックします。接続されているすべての管理グループを対象とする場合は **[Global]** (グローバル) を、単一の管理グループを対象とする場合は **[By Management Group]** (管理グループごと) を選択します。

- アラートの時間範囲を変更するには、ダッシュボードの上部にある **[Data based on]** (データの時間範囲) を選択します。過去 7 日、過去 1 日、過去 6 時間のいずれかの時間範囲内に生成されたアラートを選択できます。**[Custom]** (カスタム) を選択して、独自の日付範囲を指定することもできます。

## Log Analytics のレコード

アラート管理ソリューションでは、**Alert** タイプのすべてのレコードが分析されます。アラートは SCOM からもインポートされ、タイプを **Alert**、SourceSystem を **OpsManager** として、それぞれ対応するレコードが作成されます。これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
| 型 | *アラート:* |
| SourceSystem | *OpsManager* |
| AlertContext | アラートが生成されるきっかけとなったデータ項目の詳細 (XML 形式)。 |
| AlertDescription | アラートの詳しい説明。 |
| AlertId | アラートの GUID。 |
| AlertName | アラートの名前。 |
| AlertPriority | アラートの優先度。 |
| AlertSeverity | アラートの重大度。 |
| AlertState | アラートの最新の解決状態。 |
| LastModifiedBy | アラートを最後に変更したユーザーの名前。 |
| ManagementGroupName | アラートが生成された管理グループの名前。 |
| RepeatCount | 同じ監視対象オブジェクトについて、同じアラートが前回の解決以降に生成された回数。 |
| ResolvedBy | アラートを解決したユーザーの名前。アラートが未解決の場合は空になります。 |
| SourceDisplayName | アラートの生成元となった監視オブジェクトの表示名。 |
| SourceFullName | アラートの生成元となった監視オブジェクトの完全名。 |
| TicketId | アラートのチケット ID (SCOM 環境がアラートのチケット割り当てプロセスに統合されている場合)。チケット ID が割り当てられていない場合は空になります。 |
| TimeGenerated | アラートが作成された日付と時刻。 |
| TimeLastModified | アラートが最後に変更された日付と時刻。 |
| TimeRaised | アラートが生成された日付と時刻。 |
| TimeResolved | アラートが解決された日付と時刻。アラートが未解決の場合は空になります。 |

## サンプル ログ検索

以下の表は、このソリューションによって収集されたアラート レコードを探すログ検索の例です。

| クエリ | 説明 |
|:--|:--|
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR | 過去 24 時間以内に発生した重大なアラート |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR | 過去 24 時間以内に発生した警告アラート |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR | measure count() as Count by SourceDisplayName | 過去 24 時間以内に発生したアクティブなアラートが存在するソース |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed | 過去 24 時間以内に発生し、まだ解決されていない重大なアラート |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed | 過去 24 時間以内に発生したものの、既に解決されている重大なアラート |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY | measure count() as Count by AlertSeverity | 過去 1 日以内に発生したアラートを重大度に基づいてグループ化 |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY | sort RepeatCount desc | 過去 1 日以内に発生したアラートを RepeatCount の値で並べ替え |

## 次のステップ

- Log Analytics におけるアラートの生成について詳しくは、[Log Analytics のアラート](log-analytics-alerts.md)に関するページを参照してください。 

<!---HONumber=AcomDC_0518_2016-->