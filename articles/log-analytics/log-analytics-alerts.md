---
title: "OMS Log Analytics のアラートの概要 | Microsoft Docs"
description: "Log Analytics のアラートは、OMS リポジトリ内の重要な情報を識別し、問題について事前に通知したり、問題を修正するためのアクションを呼び出したりできます。  この記事では、アラート ルールを作成する方法と、実行できるさまざまなアクションの詳細について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: ec7167fb36e54219957629699f33dfce950f86d5
ms.openlocfilehash: 48d921650dbbf3f9cc2a8a0e265e0c7deaebdafd
ms.lasthandoff: 03/01/2017


---
# <a name="respond-to-issues-in-log-analytics-using-alerts"></a>アラートを使用した Log Analytics の問題への対応

Log Analytics のアラートは、Log Analytics リポジトリ内の重要な情報を特定します。  アラートを使用して、問題を事前に通知したり、問題に対応するための自動アクションを実行したりできます。  この記事では、アラートを作成して使用する方法の概要を説明します。  


>[!NOTE]
> 現在パブリック プレビューのメトリック測定アラート ルールの詳細については、「[New metric measurement alert rule type in Public Preview! (新しいメトリック測定アラートルール タイプがパブリック プレビューに登場)](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/)」を参照してください。

## <a name="alert-rules"></a>アラート ルール

アラートは、ログ検索を一定の間隔で自動的に実行するアラート ルールによって作成されます。  ログ検索の結果が特定の条件に一致すると、アラート レコードが作成されます。  また、アラートを事前に通知したり、別のプロセスを呼び出したりするための&1; つ以上のアクションを、ルールによって自動的に実行することもできます。  

![Log Analytics alerts](media/log-analytics-alerts/overview.png)


アラート ルールは次の内容で定義されます。

- **ログ検索**:   これは、アラート ルールが実行されるたびに実行されるクエリです。  このクエリによって返されるレコードを使用して、アラートを作成するかどうかを決定します。
- **時間枠**:   クエリの時間範囲を指定します。  クエリでは、現在の時刻に先立つ指定の時間範囲の間に作成されたレコードだけを返します。  5 分から 24 時間までの値を指定できます。 たとえば、時間枠が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが返されます。
- **[頻度]**:   クエリの実行頻度を指定します。 5 分から 24 時間までの値を指定できます。 この値は、時間枠の値以下にする必要があります。  この値が時間枠の値よりも大きい場合、レコードを見落とすおそれがあります。<br>たとえば、時間枠が 30 分、頻度が 60 分であるとします。  クエリが午後 1 時に実行された場合、午後 12 時 30 分から午後 1 時までの間のレコードが返されます。  次回クエリが実行されるのは午後 2 時であり、このときには午後 1 時 30 分から午後 2 時までの間のレコードが返されます。  つまり、午後 1 時から午後 1 時 30 分までの間に作成されたレコードは評価されないことになります。
- **しきい値**:   ログ検索から返されるレコードの数がしきい値を超えた場合、アラートが作成されます。

## <a name="creating-alert-rules"></a>アラート ルールの作成
アラート ルールは、複数の方法で作成および変更できます。  詳しいガイダンスについては、次の記事をご覧ください。  

- [OMS ポータル](log-analytics-alerts-creating.md)を使用してアラート ルールを作成する
- [Resource Manager テンプレート](log-analytics-template-workspace-configuration.md)を使用してアラート ルールを作成する
- [REST API](log-analytics-api-alerts.md) を使用してアラート ルールを作成する

## <a name="alert-actions"></a>アラート アクション

アラート ルールでは、アラート レコードを作成するだけでなく、アラートの作成時に&1; つ以上のアクションを実行することもできます。  アクションを使用して、アラートに対応する電子メールを送信したり、是正措置を講じるためのプロセスを開始したりできます。  

また、アクションを活用して、Log Analytics の機能を他のサービスによって強化することもできます。  たとえば、Log Analytics では、SMS や電話を使用して通知する機能は現在提供されていません。  アラート ルールで webhook アクションを使用すると、これらの機能を提供する [PagerDuty](https://www.pagerduty.com/) などのサービスを呼び出すことができます。  [Slack](https://slack.com/) を使用してメッセージを送信する webhook の作成例については、「[Create an alert webhook action in OMS Log Analytics to send message to Slack (OMS Log Analytics で Slack にメッセージを送信するアラート webhook アクションを作成する)](log-analytics-alerts-webhooks.md)」をご覧ください。

次の表に実行できるアクションを示します。  これらの各アクションの詳細については、[Log Analytics でアラート ルールにアクションを追加する方法](log-analytics-alerts-actions.md)に関する記事をご覧ください。 

| アクション | Description |
|:--|:--|
| 電子メール  |     アラートの詳細を記載した電子メールを&1; 人以上の受信者に送信します。 |
| Webhook | 1 つの HTTP POST 要求を使用して外部プロセスを呼び出します。 |
| Runbook | Azure Automation で Runbook を開始します。 |


## <a name="alerting-scenarios"></a>アラート作成シナリオ

### <a name="events"></a>イベント
1 つのイベントに対してアラートを作成するには、結果の数を **0 より大きい**に設定し、頻度と時間枠の両方を **5 分**に設定したアラート ルールを作成します。  それにより、クエリが 5 分ごとに実行され、前回のクエリ実行後に作成された 1 つのイベントの発生を確認します。  頻度の値を大きくすると、イベントが収集されてアラートが作成される間隔が長くなります。  次のようなクエリを使用して、対象となるイベントを指定します。

    Type=Event Source=MyApplication EventID=7019 

一部のアプリケーションでは、必ずしもアラートを発生させない偶発的なエラーが記録される場合もあります。  たとえば、エラー イベントを作成したプロセスをアプリケーションが再試行し、次回は成功する場合などがあります。  このような場合は、特定の時間枠内に複数のイベントが作成されない限り、アラートを作成しないようにできます。  そのためには、同じクエリを使用し、しきい値を大きな値に設定します。  たとえば、30 分間に発生した 5 つのイベントに対してアラートを作成するには、頻度を **5 分**、時間枠を **30 分**、結果の数を **4 より大きい**にそれぞれ設定します。    

また、イベントが発生しないときにアラートを作成する場合もあります。  たとえば、正しく動作していることを示すために定期的なイベントを記録するプロセスがあります。  そのようなプロセスが特定の時間枠内にそれらのイベントを記録しなかった場合には、アラートを作成する必要があります。  この場合、結果の数を **1 より小さい**に設定します。

### <a name="performance-alerts"></a>パフォーマンスのアラート
[パフォーマンス データ](log-analytics-data-sources-performance-counters.md)は、イベントと同様にレコードとして Log Analytics リポジトリに格納されます。  パフォーマンス カウンターが特定のしきい値を超えたときにアラートを生成する場合は、クエリにそのしきい値を含める必要があります。

たとえば、プロセッサが 90% を超える割合で実行されたときにアラートを作成する場合は、アラート ルールの結果の数を **0 より大きい**に設定して、次のようなクエリを使用します。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

プロセッサが特定の時間枠に平均 90% を超える割合で実行されたときにアラートを作成する場合は、アラート ルールのしきい値を **0 より大きい**に設定して、次のように [measure コマンド](log-analytics-search-reference.md#commands)を使用したクエリを使用します。 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90


## <a name="alert-records"></a>アラート レコード
Log Analytics のアラート ルールで作成されるアラート レコードは、**[Type]** が **[Alert]**、**[SourceSystem]** が **[OMS]** に設定されています。  これらのレコードには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| 型 |*アラート:* |
| SourceSystem |*OMS* |
| AlertName |アラートの名前。 |
| AlertSeverity |アラートの重大度。 |
| LinkToSearchResults |アラートを作成したクエリに基づいてレコードを返す Log Analytics ログ検索へのリンク。 |
| クエリ |実行されたクエリのテキスト。 |
| QueryExecutionEndTime |クエリの時間範囲の終了時刻。 |
| QueryExecutionStartTime |クエリの時間範囲の開始時刻。 |
| ThresholdOperator | アラート ルールで使用された演算子。 |
| ThresholdValue | アラート ルールで使用された値。 |
| TimeGenerated |アラートが作成された日付と時刻。 |

[Alert Management ソリューション](log-analytics-solution-alert-management.md)および [Power BI エクスポート](log-analytics-powerbi.md)では、他の種類のアラート レコードも作成されます。  これらはすべて、**[Type]** が **[Alert]** ですが、それぞれ **[SourceSystem]** によって区別されます。


## <a name="next-steps"></a>次のステップ
* [OMS ポータル](log-analytics-alerts-creating.md)を使用してアラート ルールを作成します。
* [Alert Management ソリューション](log-analytics-solution-alert-management.md) をインストールして、Log Analytics で作成されたアラートおよび System Center Operations Manager (SCOM) から収集されたアラートを分析します。
* アラートを生成する [ログ検索](log-analytics-log-searches.md) の詳細を確認します。
* アラート ルールに関する [Webhook を構成する](log-analytics-alerts-webhooks.md) チュートリアルを完了します。  
* アラートで識別された問題を修復するために [Azure Automation の Runbook](https://azure.microsoft.com/documentation/services/automation) を作成する方法について学習します。


