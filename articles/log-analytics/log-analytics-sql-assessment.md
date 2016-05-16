<properties
	pageTitle="Log Analytics で SQL 評価ソリューションによる環境を最適化する | Microsoft Azure"
	description="SQL 評価ソリューションを使用して、サーバー環境のリスクと正常性を定期的に評価します。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Log Analytics で SQL 評価ソリューションによる環境を最適化する


SQL 評価ソリューションを使用して、サーバー環境のリスクと正常性を定期的に評価します。この記事は、潜在的な問題の修正措置を実行できるように、ソリューションをインストールするために役立ちます。

このソリューションでは、デプロイされているサーバー インフラストラクチャに固有の優先順位付けされた推奨事項の一覧を提供します。推奨事項は 6 つの対象領域に分類されているので、すばやくリスクを把握し、修正措置を実行できます。

推奨事項は、マイクロソフトのエンジニアによる数多くの顧客訪問によって得られた知識と経験に基づいています。各推奨事項では、問題が重要である理由と推奨される変更を実装する方法に関するガイダンスが提供されます。

組織にとって最も重要な対象領域を選択し、リスクのない正常な環境の実行に向けた進行状況を追跡できます。

ソリューションを追加し、評価が完了すると、環境のインフラストラクチャの **[SQL Assessment (SQL 評価)]** ダッシュボードに対象領域の概要情報が表示されます。次のセクションでは、 **[SQL Assessment (SQL 評価)]** ダッシュボードの情報を使用する方法について説明します。ここでは、SQL サーバー インフラストラクチャを確認し、推奨された解決方法を実行できます。

![[SQL 評価] タイルの画像](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![[SQL 評価] ダッシュボードの画像](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## ソリューションのインストールと構成
SQL 評価は、Standard、Developer、Enterprise の各エディションの、現在サポートされているすべてのバージョンの SQL Server に対応しています。

次の情報を使用して、ソリューションをインストールおよび構成します。

- SQL 評価ソリューションには、OMS エージェントがある各コンピューターにインストールされている .NET Framework 4 が必要です。
- Operations Manager エージェントを SQL の評価に使用する場合は、Operations Manager の実行アカウントを使用する必要があります。詳細については、「[OMS のアカウントとして実行される Operations Manager](#operations-manager-run-as-accounts-for-oms)」を参照してください。

    >[AZURE.NOTE] MMA エージェントでは、Operations Manager の実行アカウントはサポートされていません。

- 「[Add Log Analytics solutions from the Solutions Gallery (ソリューション ギャラリーから Log Analytics ソリューションを追加する)](log-analytics-add-solutions.md)」で説明されている手順に従って SQL 評価ソリューションを OMS ワークスペースに追加します。 さらに手動で構成する必要はありません。

>[AZURE.NOTE] ソリューションを追加した後、AdvisorAssessment.exe ファイルがエージェントを含むサーバーに追加されます。構成データが読み取られ、処理のためにクラウドの OMS サービスに送信されます。受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。

## SQL 評価データ収集の詳細

次の表に、エージェントのデータ収集方法、Operations Manager (SCOM) が必要であるかどうか、およびどのくらいの頻度でデータがエージェントによって収集されるかを示します。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM は必要ですか? | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
|---|---|---|---|---|---|---|
|Windows|![あり](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![はい](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![なし](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![いいえ](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![あり](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 日|

## OMS で使用される Operations Manager の実行アカウント

OMS の Log Analytics では、データの収集と OMS サービスへのデータの送信に、Operations Manager エージェントと管理グループを使用します。OMS は、ワークロード用の管理パックを基に付加価値サービスを実現しています。それぞれのワークロードがさまざまなセキュリティ コンテキストで管理パックを実行するためには、ワークロード固有の特権が必要となります (ドメイン アカウントなど)。Operations Manager の実行アカウントを構成して資格情報を与えることが必要です。

次の情報を使用すると、SQL の評価用に Operations Manager の実行アカウントを設定できます。

### SQL の評価に使用する実行アカウントの設定

 SQL Server 管理パックを既に使用している場合、SQL Server の実行アカウントを使用する必要があります。

#### オペレーション コンソールで SQL の実行アカウントを構成するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[OMS SQL の評価の実行プロファイル]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. SQL Server に必要な資格情報を含んだ Windows 実行アカウントを選択するか、**[新規]** をクリックして新たに作成します。
	>[AZURE.NOTE] 実行アカウントの種類は Windows であることが必要です。さらに、SQL Server インスタンスをホストするすべての Windows Server 上のローカルの Administrators グループに、その実行アカウントが属している必要があります。

5. **[保存]** をクリックします。

6. 次の T-SQL サンプルに変更を加えて、各 SQL Server インスタンスで実行します。実行アカウントで SQL の評価を行うために必要な最低限の権限が付与されます。ただし、実行アカウントが既に SQL Server インスタンスの sysadmin サーバー ロールに属している場合、この作業は不要です。

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Understanding how recommendations are prioritized

Every recommendation made is given a weighting value that identifies the relative importance of the recommendation. Only the ten most important recommendations are shown.

### How weights are calculated

Weightings are aggregate values based on three key factors:

- The *probability* that an issue identified will cause problems. A higher probability equates to a larger overall score for the recommendation.

- The *impact* of the issue on your organization if it does cause a problem. A higher impact equates to a larger overall score for the recommendation.

- The *effort* required to implement the recommendation. A higher effort equates to a smaller overall score for the recommendation.

The weighting for each recommendation is expressed as a percentage of the total score available for each focus area. For example, if a recommendation in the Security and Compliance focus area has a score of 5%, implementing that recommendation will increase your overall Security and Compliance score by 5%.

### Focus areas

**Security and Compliance** - This focus area shows recommendations for potential security threats and breaches, corporate policies, and technical, legal and regulatory compliance requirements.

**Availability and Business Continuity** - This focus area shows recommendations for service availability, resiliency of your infrastructure, and business protection.

**Performance and Scalability** - This focus area shows recommendations to help your organization's IT infrastructure grow, ensure that your IT environment meets current performance requirements, and is able to respond to changing infrastructure needs.

**Upgrade, Migration and Deployment** - This focus area shows recommendations to help you upgrade, migrate, and deploy SQL Server to your existing infrastructure.

**Operations and Monitoring** - This focus area shows recommendations to help streamline your IT operations, implement preventative maintenance, and maximize performance.

**Change and Configuration Management** - This focus area shows recommendations to help protect day-to-day operations, ensure that changes don't negatively affect your infrastructure, establish change control procedures, and to track and audit system configurations.

### Should you aim to score 100% in every focus area?

Not necessarily. The recommendations are based on the knowledge and experiences gained by Microsoft engineers across thousands of customer visits. However, no two server infrastructures are the same, and specific recommendations may be more or less relevant to you. For example, some security recommendations might be less relevant if your virtual machines are not exposed to the Internet. Some availability recommendations may be less relevant for services that provide low priority ad hoc data collection and reporting. Issues that are important to a mature business may be less important to a start-up. You may want to identify which focus areas are your priorities and then look at how your scores change over time.

Every recommendation includes guidance about why it is important. You should use this guidance to evaluate whether implementing the recommendation is appropriate for you, given the nature of your IT services and the business needs of your organization.

## Use assessment focus area recommendations

Before you can use an assessment solution in OMS, you must have the solution installed. To read more about installing solutions, see [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). After it is installed, you can view the summary of recommendations by using the SQL Assessment tile on the Overview page in OMS.

View the summarized compliance assessments for your infrastructure and then drill-into recommendations.

### To view recommendations for a focus area and take corrective action

1. On the **Overview** page, click the **SQL Assessment** tile.
2. On the **SQL Assessment** page, review the summary information in one of the focus area blades and then click one to view recommendations for that focus area.
3. On any of the focus area pages, you can view the prioritized recommendations made for your environment. Click a recommendation under **Affected Objects** to view details about why the recommendation is made.  
    ![image of SQL Assessment recommendations](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. You can take corrective actions suggested in **Suggested Actions**. When the item has been addressed, later assessments will record that recommended actions were taken and your compliance score will increase. Corrected items appear as **Passed Objects**.

## Ignore recommendations

If you have recommendations that you want to ignore, you can create a text file that OMS will use to prevent recommendations from appearing in your assessment results.

### To identify recommendations that you will ignore

1.	Sign in to your workspace and open Log Search. Use the following query to list recommendations that have failed for computers in your environment.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Here's a screen shot showing the Log Search query:
    ![failed recommendations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	無視する推奨事項を選択します。次の手順で RecommendationId の値を使用します。


### IgnoreRecommendations.txt テキスト ファイルを作成および使用するには

1.	IgnoreRecommendations.txt という名前のファイルを作成します。
2.	OMS に個別の行で無視させ、ファイルを保存して閉じさせるには、推奨事項ごとにそれぞれ RecommendationId を貼り付けるか入力します。
3.	OMS に推奨事項を無視させる各コンピューターの次のフォルダーにファイルを配置します。
    - Microsoft Monitoring Agent を含むコンピューター (直接または Operations Manager 経由で接続されている) - *SystemDrive*:\\Program Files\\Microsoft Monitoring Agent\\Agent
    - Operations Manager 管理サーバー - *SystemDrive*:\\Program Files\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### 推奨事項が無視されていることを確認するには

1.	次回スケジュールされている評価が実行した後は、既定では 7 日おきで、推奨事項が Ignored とマークされ、評価ダッシュボードには表示されません。
2.	次のログ検索クエリを使用して、無視されるすべての推奨事項の一覧を表示します。

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	無視された推奨事項を表示することを後で決定する場合は、IgnoreRecommendations.txt ファイルを削除します。また、そのファイルから RecommendationID を削除することもできます。

## SQL 評価ソリューションに関する FAQ

*評価はどのくらいの頻度で実行されますか?*
- 評価は 7 日おきに実行されます。

*評価の実行頻度を構成する方法がありますか?*
- 現時点ではありません。

*SQL 評価ソリューションを追加後、別のサーバーが検出された場合、それは評価されますか?*
- はい。検出されると、それ以降 7 日おきに評価されます。

*サーバーを使用停止にした場合、それはいつ評価対象から除外されますか?*
- サーバーは、3 週間にわたりデータを送信しない場合、除外されます。

*データ収集を行うプロセスの名前は何ですか?*
- AdvisorAssessment.exe です。

*データの収集にはどれくらいの時間がかかりますか?*
- サーバー上での実際のデータ収集には約 1 時間かかります。SQL のインスタンスまたはデータベースの数が多いサーバーでは、もっと長くなる可能性があります。

*どのような種類のデータが収集されますか?*
- 次の種類のデータが収集されます。
    - WMI
    - レジストリ
    - パフォーマンス カウンター
    - SQL の動的管理ビュー (DMV)

*データが収集されるタイミングを構成する方法はありますか?*
- 現時点ではありません。

*実行アカウントを構成しなければならないのはなぜですか?*
- SQL Server の場合、少数の SQL クエリが実行されます。これらのクエリが動作するためには、SQL に対する VIEW SERVER STATE 権限を持つ実行アカウントを使用する必要があります。さらに、WMI を照会するには、ローカル管理者の資格情報が必要です。

*上位 10 個の推奨事項しか表示されないのはなぜですか?*
- タスクの一覧を余すことなく完全に提供するのでなく、まず優先的な推奨事項への対処に重点を置くことをお勧めしています。優先的な推奨事項に対処すると、追加の推奨事項が表示されます。詳細な一覧を確認する場合は、OMS のログ検索を使用してすべての推奨事項を表示できます。

*推奨事項を無視する方法はありますか?*
- はい。前のセクション「[推奨事項を無視する](#ignore-recommendations)」を参照してください。



## 次のステップ

- [ログ検索](log-analytics-log-searches.md)を実行して、詳細な SQL の評価データと推奨事項を表示します。

<!---HONumber=AcomDC_0504_2016-->