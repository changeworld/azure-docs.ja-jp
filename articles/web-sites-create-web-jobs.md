<properties 
	pageTitle="Web ジョブでのバック グラウンド タスクの実行" 
	description="Web アプリでバックグラウンド タスクを実行する方法について説明します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Web ジョブでのバック グラウンド タスクの実行

## 概要

[アプリ サービス](http://go.microsoft.com/fwlink/?LinkId=529714) Web アプリの Web ジョブで、プログラムまたはスクリプトを 3 つの方法で実行できます (継続的に、要求時に、またはスケジュールに従って)。Web ジョブの使用に追加コストはかかりません。

この記事では、[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用して Web ジョブをデプロイする方法について説明します。Visual Studio または継続的な配信を使用したデプロイ方法の詳細については、「[Azure Web ジョブの Web アプリへのデプロイ方法](websites-dotnet-deploy-webjobs.md)」を参照してください。

Azure の Web ジョブ SDK は多くの Web ジョブのプログラミング タスクを簡略化しています。詳細については、「[Azure Web ジョブ SDK とは](websites-dotnet-webjobs-sdk.md)」を参照してください。

## <a name="acceptablefiles"></a>スクリプトやプログラムで許容可能なファイルの種類

次のファイルの種類を指定できます。

* .cmd、.bat、.exe (windows cmd を使用)
* .ps1 (powershell を使用)
* .sh (bash を使用)
* .php (php を使用)
* .py (python を使用)
* .js (node を使用)

## <a name="CreateOnDemand"></a>ポータルでのオンデマンドの web ジョブの作成

1. [Azure ポータル](http://portal.azure.com)の **[Web アプリ]** ブレードで、**[すべての設定] > [Web ジョブ]** の順にクリックし、**[Web ジョブ]** ブレードを表示します。
	
	![WebJob blade](./media/web-sites-create-web-jobs/wjblade.png)
	
5. **[追加]** をクリックします。**[Web ジョブの追加]** ダイアログ ボックスが表示されます。
	
	![Add WebJob blade](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. **[名前]** ボックスに、Web ジョブの名前を入力します。名前は、先頭を文字または数字にする必要があります。"-" と "_" 以外の特殊文字を使用することはできません。
	
4. **[実行方法]** ボックスの一覧の **[オンデマンドで実行]** をクリックします。
	
3. **[ファイル アップロード]** ボックスで、フォルダー アイコンをクリックし、スクリプトが含まれている zip ファイルを参照します。この zip ファイルには、実行可能ファイル (.exe .cmd .bat .sh .php .py .js) のほか、プログラムまたはスクリプトの実行に必要な補助ファイルもすべて含まれている必要があります。
	
5. **[作成]** をクリックし、Web アプリにスクリプトをアップロードします。 
	
	Web ジョブに指定した名前が、**[Web ジョブ]** ブレードの一覧に表示されます。
	
6. Web ジョブを実行するには、一覧でその名前を右クリックし、**[実行]** をクリックします。
	
	![Run WebJob](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>連続的に実行する Web ジョブを作成する

1. 連続的に実行する Web ジョブを作成するには、一度だけ実行する Web ジョブを作成する場合と同様の手順を使用します。ただし、**[実行方法]** ボックスの一覧では **[連続的]** を選択します。

2. 連続的な Web ジョブを開始または停止するには、一覧で Web ジョブを右クリックし、**[開始]** または **[停止]** をクリックします。
	
> [AZURE.NOTE] Web アプリが複数のインスタンスで実行される場合、連続的に実行する Web ジョブはすべてのインスタンス上で実行されます。オンデマンドで実行する Web ジョブおよびスケジュールに従って実行するタスクは、負荷分散目的で Microsoft Azure によって選択された単一のインスタンス上で実行されます。
	
> [AZURE.NOTE] 連続的に実行する Web ジョブについては、Web アプリで **[常時接続]** を有効にすることをお勧めします。[常時接続] は [基本] と [標準] のモードで使用できる機能であり、この機能を有効にすると、アイドル状態がしばらく続いても Web アプリがアンロードされなくなります。Web アプリが常に読み込まれていれば、連続的に実行する Web ジョブをより高い信頼性で実行できます。 

## <a name="CreateScheduled"></a>スケジュールされた WebJob の作成

Azure 管理ポータルには、スケジュールされた Web ジョブを作成する機能がまだありません。ただし、この機能が追加されるまで、[以前のポータル](http://manage.windowsazure.com)を使用して、これを行うことができます。

1. [以前のポータル](http://manage.windowsazure.com) で、[Web ジョブ] ページに移動し、**[追加]** をクリックします。

1. **[実行方法]** ボックスの一覧の **[スケジュールに従って実行]** をクリックします。
	
	![New Scheduled Job][NewScheduledJob]
	
2. **[スケジューラのリージョン]** ボックスの一覧で、ジョブに応じたリージョンを選択し、ダイアログ ボックスの右下にある右矢印をクリックして、次の画面に進みます。

3. **[ジョブの作成]** ダイアログ ボックスの **[繰り返し]** ボックスで、繰り返しの種類として**[一度だけのジョブ**] または [**定期的なジョブ**] を選択します。
	
	![Schedule Recurrence][SchdRecurrence]
	
4. また、**[開始]** 時間で、**[今すぐ] **または **[特定の時間]** を選択します。
	
	![Schedule Start Time][SchdStart]
	
5. 特定の日時に開始するには、**[開始日時]** の各ボックスで、開始時刻の値を選択します。
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. 定期的なジョブを選択した場合は、**[繰り返しの間隔]** で実行頻度を指定し、**[終了日]** で終了日時を指定します。
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. **[週]** を選択した場合は **[特定のスケジュールに従って]** チェック ボックスをオンにし、ジョブを実行する曜日 (複数も可能) を指定することができます。
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. **[月]** を選択して **[特定のスケジュールに従って]** チェック ボックスをオンにした場合は、**[日]** をクリックして、1 か月のうちジョブを実行する日 (複数も可能) を指定することができます。 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. **[平日]** を選択した場合は、1 か月のうちジョブを実行する曜日 (複数も可能) を指定することができます。
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. 最後に、**[発生回数]** を併せて使用すると、1 か月のうち指定した曜日にジョブを実行する週 (1 週目、2 週目、3 週目など) を選択することができます。
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. ジョブ (複数可) を作成したら、それらの名前が状態、スケジュールの種類、その他の情報と共に [Web ジョブ] タブに表示されます。最近実行された 30 件の Web ジョブに関する履歴情報が管理されます。
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>スケジュールされたジョブと Azure スケジューラ

スケジュールされたジョブは、[以前のポータル](http://manage.windowsazure.com)の Azure スケジューラ ページでさらに構成できます。

1.	[Web ジョブ] ページで、ジョブの **[スケジュール]** リンクをクリックして Azure スケジューラ ポータル ページに移動します。 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. スケジューラ ページで、ジョブをクリックします。
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. **[ジョブの操作]** ページが開き、ここでジョブをさらに構成できます。 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>ジョブ履歴を表示する

1. Web ジョブ SDK で作成したジョブを含め、ジョブの実行履歴を表示するには、[Web ジョブ] ブレードの **[ログ]** 列で対応するリンクをクリックします。(必要に応じて、クリップボード アイコンを使用して、ログ ファイル ページの URL をクリップボードにコピーできます)。
	
	![ログのリンク](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. リンクをクリックすると、Web ジョブの詳細ページが開きます。このページには、実行されたコマンド、そのコマンドを最後に実行した時間、その実行が成功したか失敗したかが表示されます。**[最近実行されたジョブ]** で、さらに詳細を表示する時間をクリックします。
	
	![WebJobDetails][WebJobDetails]
	
3. **[Web ジョブ実行の詳細]** ページが表示されます。ログの内容のテキストを表示するには、**[出力切り替え]** をクリックします。ログ出力がテキスト形式で表示されます。 
	
	![Web job run details][WebJobRunDetails]
	
4. 出力されたテキストを別のブラウザー ウィンドウで確認するには、**[ダウンロード]** リンクをクリックします。テキスト自体をダウンロードするには、リンクを右クリックしてから、ブラウザーのオプションを使用してファイルの内容を保存します。
	
	![Download log output][DownloadLogOutput]
	
5. ページの上部にある **[Web ジョブ]** リンクを使用すると、履歴ダッシュボードに Web ジョブの一覧を簡単に表示できます。
	
	![Link to WebJobs list][WebJobsLinkToDashboardList]
	
	![List of WebJobs in history dashboard][WebJobsListInJobsDashboard]
	
	これらのリンクのいずれかをクリックすると、選択したジョブの Web ジョブ詳細ページに移動します。


## <a name="WHPNotes"></a>メモ
	
- 2014 年 3 月の時点では、scm (デプロイ) サイトへの要求がなく Azure で Web アプリのポータルが開かれていない場合、[無料] モードの Web アプリは 20 分後にタイムアウトすることがあります。実際のサイトへの要求があっても、この状態はリセットされません。
- 連続するジョブのコードは、無限ループで実行されるように記述する必要があります。
- 連続するジョブが連続的に実行されるのは、アプリが稼働状態になっているときのみです。
- [基本] モードと [標準] モードには [常時接続] 機能が用意されており、この機能を有効にすると、Web アプリがアイドル状態にならなくなります。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[アプリ サービスの試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。アプリ サービスで、有効期間が短いスターター Web アプリをすぐに作成できます。クレジット カードが必要なコミットメントはありません。

## <a name="NextSteps"></a>次のステップ
 
詳細については、「[Azure Web ジョブの推奨リソース][」を参照してください。]

## 変更内容
* Web サイトからアプリ サービスへの変更に関するガイドについては、以下を参照してください。[Azure App Service and Its Impact on Existing Azure Services (Azure アプリ サービスと既存の Azure サービスへの影響)](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更に関するガイドについては、以下を参照してください。[Reference for navigating the preview portal (プレビュー ポータルをナビゲートするためのリファレンス)](http://go.microsoft.com/fwlink/?LinkId=529715)

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png

<!--HONumber=49-->