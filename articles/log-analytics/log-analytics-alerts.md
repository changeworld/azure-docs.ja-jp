---
title: Log Analytics のアラート | Microsoft Docs
description: Log Analytics のアラートは、OMS リポジトリ内の重要な情報を識別し、問題について事前に通知したり、問題を修正するためのアクションを呼び出したりできます。  この記事では、アラート ルールを作成する方法と、実行できるさまざまなアクションの詳細について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2016
ms.author: bwren

---
# <a name="alerts-in-log-analytics"></a>Log Analytics のアラート
Log Analytics のアラートは、OMS リポジトリ内の重要な情報を識別します。  アラート ルールにより、スケジュールに従って自動的にログ検索が実行され、結果が特定の条件に一致した場合にアラート レコードが作成されます。  さらに、アラートを事前に通知したり、別のプロセスを呼び出したりするために、1 つ以上のアクションを自動的に実行できます。   

![Log Analytics alerts](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>アラート ルールの作成
アラート ルールを作成するには、まずアラートを呼び出すレコードに対するログ検索を作成します。  それにより、アラート ルールを作成して構成するための **[Alert]** (アラート) ボタンが使用できるようになります。

1. OMS の [Overview](概要.md) ページで、 **[Log Search]**(ログ検索) をクリックします。
2. 新しいログ検索クエリを作成するか、保存されているログ検索を選択します。 
3. ページの上部にある **[アラート]** をクリックして、**[アラート ルールの追加]** 画面を開きます。
4. アラートを構成するオプションの詳細については、次に示す表を参照してください。
5. アラート ルールの時間枠を指定すると、その時間枠内で検索条件に一致した既存のレコードの数が表示されます。  これにより、期待する数の結果が得られる頻度を判断できます。
6. **[Save]** (保存) をクリックして、アラート ルールを完成させます。  すぐに実行が開始されます。

![[Add Alert Rule]](media/log-analytics-alerts/add-alert-rule.png)

| プロパティ | 説明 |
|:--- |:--- |
| **Alert information** | |
| 名前 |アラート ルールを識別する一意の名前。 |
| 重大度 |このルールによって作成されるアラートの重大度。 |
| Search query (検索クエリ) |**[Use current search query]** (現在の検索クエリを使用) を選択して現在のクエリを使用するか、または既存の保存した検索条件を一覧から選択します。  クエリの構文がテキスト ボックスに表示され、必要に応じて変更できます。 |
| Time window (時間枠) |クエリの時間範囲を指定します。  クエリでは、現在の時刻に先立つ指定の時間範囲の間に作成されたレコードだけを返します。  5 分から 24 時間までの値を指定できます。  アラートの頻度以上の値を指定する必要があります。  <br><br>  たとえば、時間枠が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが返されます。 |
| **スケジュール** | |
| しきい値 |アラートを作成する条件。  クエリによって返されるレコード数がこの条件に一致する場合に、アラートが作成されます。 |
| Alert frequency (アラートの頻度) |クエリの実行頻度を指定します。  5 分から 24 時間までの値を指定できます。  この値は、時間枠の値以下にする必要があります。 |
| Suppress alerts (アラートの抑制) |アラート ルールの抑制を有効にすると、新しいアラートを作成した後、定義された期間の間、ルールのアクションが無効になります。  ルールは引き続き実行され、条件が満たされればアラート レコードが作成されます。  このオプションは、問題を修正している間、同じアクションが繰り返し実行されるのを防ぐために用意されています。 |
| **アクション** | |
| 電子メール通知 |アラートがトリガーされたときに電子メールを送信する場合は、 **[Yes]** (はい) を指定します。 |
| [件名] |電子メールの件名。  電子メールの本文を変更することはできません。 |
| Recipients |電子メールのすべての受信者のアドレス。  複数のアドレスを指定する場合は、アドレスをセミコロン (;) で区切ります。 |
| Webhook |アラートがトリガーされたときに Webhook を呼び出す場合は、 **[Yes]** (はい) を指定します。 |
| Webhook URL |Webhook の URL。 |
| Include custom JSON payload (カスタム JSON ペイロードを含める) |既定のペイロードをカスタム ペイロードに置き換える場合は、このオプションを選択します。 |
| Enter your custom JSON payload (カスタム JSON ペイロードの入力) |Webhook のカスタム ペイロード。  詳細については、前のセクションを参照してください。 |
| Runbook |アラートがトリガーされたときに Azure Automation の Runbook を開始する場合は、 **[Yes]** (はい) を指定します。 |
| Select a runbook (Runbook の選択) |Automation ソリューションで構成されているオートメーション アカウントの Runbook から、開始する Runbook を選択します。 |
| Run on (実行先) |Runbook を Azure クラウドで実行する場合は、 **[Azure]** を選択します。  Runbook をローカル環境の **Hybrid Runbook Worker** で実行する場合は、 [[ハイブリッド worker]](../automation/automation-hybrid-runbook-worker.md) を選択します。 |

## <a name="manage-alert-rules"></a>アラート ルールの管理
Log Analytics の **[Settings (設定)]** の **[Alerts (アラート)]** メニューで、すべてのアラート ルールの一覧を表示することができます。  

![Manage alerts](./media/log-analytics-alerts/configure.png)

1. OMS コンソールで、 **[Settings]** (設定) タイルを選択します。
2. **[Alerts]**(アラート) を選択します。

このビューから複数のアクションを実行できます。

* 横にある **[Off]** (オフ) を選択して、ルールを無効にします。
* その横にある鉛筆アイコンをクリックして、アラート ルールを編集します。
* その横にある **[X]** アイコンをクリックして、アラート ルールを削除します。 

## <a name="setting-time-windows"></a>時間枠の設定
### <a name="event-alerts"></a>イベントのアラート
イベントには、Windows イベント ログ、Syslog、カスタム ログなどのデータ ソースが含まれます。  特定のエラー イベントが作成されたとき、または特定の時間枠内に複数のエラー イベントが作成されたときなどに、アラートを作成することができます。

1 つのイベントに対してアラートを作成するには、結果の数を 0 より大きな値に設定し、頻度と時間枠の両方を 5 分に設定します。  それにより、クエリが 5 分ごとに実行され、前回のクエリ実行後に作成された 1 つのイベントの発生を確認します。  頻度の値を大きくすると、イベントが収集されてアラートが作成される間隔が長くなります。

一部のアプリケーションでは、必ずしもアラートを発生させない偶発的なエラーが記録される場合もあります。  たとえば、エラー イベントを作成したプロセスをアプリケーションが再試行し、次回は成功する場合などがあります。  このような場合は、特定の時間枠内に複数のイベントが作成されない限り、アラートを作成しないようにできます。  

また、イベントが発生しないときにアラートを作成する場合もあります。  たとえば、正しく動作していることを示すために定期的なイベントを記録するプロセスがあります。  そのようなプロセスが特定の時間枠内にそれらのイベントを記録しなかった場合には、アラートを作成する必要があります。  この場合は、しきい値を " *Less than 1*" に設定します。

### <a name="performance-alerts"></a>パフォーマンスのアラート
[パフォーマンス データ](log-analytics-data-sources-performance-counters.md) は、イベントと同様に OMS リポジトリ内のレコードとして格納されます。  各レコードの値は、過去 30 分の間に測定された平均値です。  パフォーマンス カウンターが特定のしきい値を超えたときにアラートを生成する場合は、クエリにそのしきい値を含める必要があります。

たとえば、プロセッサが 30 分間にわたって 90% を超える割合で実行されたときにアラートを発生させるには、"*Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90*" というクエリを使用し、アラート ルールのしきい値を "*Greater than 0*" に設定します。  

 [パフォーマンス レコード](log-analytics-data-sources-performance-counters.md) は、各カウンターを収集する頻度に関係なく 30 分ごとに集計されるため、時間枠を 30 分より短く設定すると、レコードが返されない場合があります。  時間枠を 30 分に設定すると、接続された各ソースについて、その時間内での平均値を表す 1 つのレコードが取得されるようになります。

## <a name="alert-actions"></a>アラート アクション
アラート レコードを作成するだけでなく、1 つ以上のアクションを自動的に実行するようにアラート ルールを構成できます。  アクションを使用して、アラートが発生したときに通知したり、検出された問題を修正するためのプロセスを開始したりできます。  以降のセクションでは、現在使用できるアクションについて説明します。

### <a name="email-actions"></a>電子メール アクション
電子メール アクションは、アラートの詳細を記載した電子メールを 1 人以上の受信者に送信します。  電子メールの件名は指定できますが、メールの内容は Log Analytics によって構築された標準の形式となります。  電子メールには、アラートの名前などの概要情報に加えて、ログ検索で返される最大 10 個のレコードの詳細情報が含まれます。  また、そのクエリに基づくレコードのセット全体を返す Log Analytics のログ検索へのリンクも含まれています。   メールの送信者は、"*Microsoft Operations Management Suite チーム &lt;noreply@oms.microsoft.com&gt;*" となります。 

### <a name="webhook-actions"></a>Webhook アクション
Webhook アクションは、1 つの HTTP POST 要求を使用して外部のプロセスを呼び出すことができます。  呼び出されるサービスは、Webhook をサポートし、受信したペイロードの使用方法を決定できる必要があります。  また、要求に API で認識される形式を使用すれば、Webhook を明示的にはサポートしない REST API も呼び出すことができます。  アラートに対する応答で Webhook を使用する例として、[Slack](http://slack.com) などのサービスを使用してアラートの詳細情報を含むメッセージを送信したり、[PagerDuty](http://pagerduty.com/) などのサービスでインシデントを作成したりできます。  

Webhook でサンプル サービスを呼び出すアラート ルールを作成する詳細なチュートリアルについては、「 [Log Analytics のアラートでの Webhook](log-analytics-alerts-webhooks.md)」を参照してください。

Webhook には、URL と共に、外部のサービスに送信されるデータである JSON 形式のペイロードが含まれます。  既定では、ペイロードには次の表に示す値が格納されます。  このペイロードは、独自のカスタム ペイロードに置き換えることができます。  その場合は、各パラメーターに対して表に示される変数を使用して、カスタム ペイロードにそれらの値を含めることができます。

| パラメーター | 変数 | 説明 |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |アラート ルールの名前。 |
| AlertThresholdOperator |#thresholdoperator |アラート ルールのしきい値演算子。  "*Greater than*" または "*Less than*" を使用できます。 |
| AlertThresholdValue |#thresholdvalue |アラート ルールのしきい値。 |
| LinkToSearchResults |#LinkToSearchResults |アラートを作成したクエリに基づいてレコードを返す Log Analytics ログ検索へのリンク。 |
| ResultCount |#searchresultcount |検索結果に含まれるレコードの数。 |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |UTC 形式で記述したクエリの終了時刻。 |
| SearchIntervalInSeconds |#searchinterval |アラート ルールの時間枠。 |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |UTC 形式で記述したクエリの開始時刻。 |
| SearchQuery |#SearchQuery |アラート ルールで使用されるログ検索クエリ。 |
| SearchResults |以下を参照 |クエリによって返される JSON 形式のレコード。  最初の 5,000 レコードに制限されます。 |
| WorkspaceID |#WorkspaceID |OMS ワークスペースの ID。 |

たとえば、 *text*という名前の 1 つのパラメーターを含む次のカスタム ペイロードを指定できます。  この Webhook で呼び出すサービスでは、このパラメーターが想定されます。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

この例のペイロードは、Webhook に送信されると、次のような内容に解決されます。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

カスタム ペイロードに検索結果を含めるには、json ペイロードの最上位レベルのプロパティとして、次の行を追加します。  

    "IncludeSearchResults":true

たとえば、アラート名と検索結果だけを含むカスタム ペイロードを作成するには、次のように入力します。 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Webhook で外部サービスを開始するアラート ルールを作成する例の全体については、「 [Log Analytics のアラートでの Webhook](log-analytics-alerts-webhooks.md)」にあるサンプルを参照してください。

### <a name="runbook-actions"></a>Runbook アクション
Runbook アクションは、Azure Automation で Runbook を開始します。  この種類のアクションを使用するためには、OMS ワークスペースに [Automation ソリューション](log-analytics-add-solutions.md) がインストールされ、構成されている必要があります。  まだインストールされていない場合は、新しいアラート ルールを作成するときに、インストールへのリンクが表示されます。  Automation ソリューションで構成されているオートメーション アカウントの Runbook から選択できます。

Runbook アクションは、 [Webhook](../automation/automation-webhooks.md)を使用して Runbook を開始します。  アラート ルールを作成すると、Runbook に対して、" **OMS Alert Remediation** " の後に GUID が付いた名前を持つ新しい Webhook が自動的に作成されます。  

Runbook のパラメーターを直接設定することはできませんが、[$WebhookData パラメーター](../automation/automation-webhooks.md)にアラートの詳細 (それを作成したログ検索の結果を含む) が格納されます。  この Runbook で、アラートのプロパティにアクセスするためのパラメーターとして **$WebhookData** を定義する必要があります。  アラート データは、**$WebhookData** の **RequestBody** プロパティにある **SearchResults** という単一のプロパティから JSON 形式で取得できます。  このデータには、次の表に示したプロパティが存在します。

| ノード | 説明 |
|:--- |:--- |
| id |検索のパスと GUID。 |
| __metadata |アラートに関する情報 (レコードの件数、検索結果の状態を含む)。 |
| 値 |検索結果のレコードごとのエントリ。  エントリの詳細は、レコードのプロパティおよび値と対応します。 |

たとえば、以下の Runbook では、ログの検索から返されたレコードを抽出し、レコードの種類ごとに異なるプロパティを割り当てています。  Runbook ではまず、JSON 形式の **RequestBody** を PowerShell からオブジェクトとして扱うことができるように変換していることに注目してください。

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>アラート レコード
Log Analytics のアラート ルールで作成されるアラート レコードは、**[Type]** が **[Alert]**、**[SourceSystem]** が **[OMS]** に設定されています。  これらのレコードには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| 型 |*アラート:* |
| SourceSystem |*OMS* |
| AlertSeverity |アラートの重大度。 |
| AlertName |アラートの名前。 |
| クエリ |実行されたクエリのテキスト。 |
| QueryExecutionEndTime |クエリの時間範囲の終了時刻。 |
| QueryExecutionStartTime |クエリの時間範囲の開始時刻。 |
| TimeGenerated |アラートが作成された日付と時刻。 |

[Alert Management ソリューション](log-analytics-solution-alert-management.md)および [Power BI エクスポート](log-analytics-powerbi.md)では、他の種類のアラート レコードも作成されます。  これらはすべて、**[Type]** が **[Alert]** ですが、それぞれ **[SourceSystem]** によって区別されます。

## <a name="next-steps"></a>次のステップ
* [Alert Management ソリューション](log-analytics-solution-alert-management.md) をインストールして、Log Analytics で作成されたアラートおよび System Center Operations Manager (SCOM) から収集されたアラートを分析します。
* アラートを生成する [ログ検索](log-analytics-log-searches.md) の詳細を確認します。
* アラート ルールに関する [Webhook を構成する](log-analytics-alerts-webhooks.md) チュートリアルを完了します。  
* アラートで識別された問題を修復するために [Azure Automation の Runbook](https://azure.microsoft.com/documentation/services/automation) を作成する方法について学習します。

<!--HONumber=Oct16_HO2-->


