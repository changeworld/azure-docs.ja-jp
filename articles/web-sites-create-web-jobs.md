<properties urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Microsoft Azure の Web サイトで Web ジョブを使用してバックグラウンド タスクを実行する方法" metaKeywords="Microsoft Azure の Web サイト, Web ジョブ, バックグラウンド タスク" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="cephalin" />

#Azure Websites でバックグラウンド タスクを実行して Web ジョブを使用する方法

Azure Websites サイトを使用すると、Web サイト内でプログラムやスクリプトを "オンデマンドで実行"、"連続的に実行"、および "スケジュールに従って実行" のうちいずれかの方法で実行できます。Microsoft Azure Web ジョブの使用に追加コストはかかりません。

この記事では、Azure の管理ポータルを使用して Web ジョブをデプロイする方法について説明します。Visual Studio または継続的な配信を使用したデプロイ方法の詳細については、「[Azure Web ジョブの Azure Azure Websites へのデプロイ方法](http://azure.microsoft.com/ja-jp/documentation/articles/websites-dotnet-deploy-webjobs)」を参照してください。

Azure の Web ジョブ SDK は多くの Web ジョブのプログラミング タスクを簡略化しています。詳細については、「[Azure Web ジョブ SDK とは](../websites-dotnet-webjobs-sdk)」を参照してください。

## 目次 ##
- [スクリプトとして使用できるファイルの種類](#acceptablefiles)
- [オンデマンドで実行するタスクを作成する](#CreateOnDemand)
- [連続的に実行するタスクを作成する](#CreateContinuous)
- [スケジュールに従って実行するタスクを作成する](#CreateScheduled)
	- [スケジュールされたジョブと Azure スケジューラ](#Scheduler)
- [ジョブ履歴を表示する](#ViewJobHistory)
- [メモ](#WHPNotes)
- [次のステップ](#NextSteps)

## <a name="acceptablefiles"></a>スクリプトやプログラムで許容可能なファイルの種類

次のファイルの種類を指定できます。

* .cmd、.bat、.exe (windows cmd を使用)
* .ps1 (powershell を使用)
* .sh (bash を使用)
* .php (php を使用)
* .py (python を使用)
* .js (node を使用)

## <a name="CreateOnDemand"></a>オンデマンドで実行するタスクを作成する

1. **[Web ジョブ]** ページのコマンド バーで、**[追加]** をクリックします。**[新しいジョブ]** ダイアログ ボックスが表示されます。
	
	![On Demand Task][OnDemandWebJob]
	
2. **[名前]** ボックスに、タスクの名前を入力します。名前は、先頭を文字または数字にする必要があります。"-" と "_" 以外の特殊文字を使用することはできません。
	
3. **[コンテンツ (Zip ファイル - 最大 100 MB)]** ボックスで、スクリプトが含まれている zip ファイルを閲覧します。この zip ファイルには、実行可能ファイル (.exe .cmd .bat .sh .php .py .js) のほか、プログラムまたはスクリプトの実行に必要な補助ファイルもすべて含まれている必要があります。
	
4. **[実行方法]** ボックスで、**[オンデマンドで実行]** をクリックします。
	
5. スクリプトを Web サイトにアップロードするには、ダイアログ ボックスの右下にあるチェック マークをクリックします。タスクに指定した名前が、一覧に表示されます。
	
	![Task List][WebJobsList]
	
6. スクリプトを実行するには、一覧で名前を選択し、ポータル ページの下部にあるコマンド バーの **[一度だけ実行する]** をクリックします。
	
	![Run Once][RunOnce]

## <a name="CreateContinuous"></a>連続的に実行するタスクを作成する

1. 連続的に実行するタスクを作成するには、一度だけ実行するタスクを作成する場合と同様の手順を使用します。ただし、**[実行方法]** ボックスの一覧では **[連続的に実行]** を選択します。
	
	![New Continuous Task][NewContinuousJob]
	
2. 連続的に実行するタスクを開始または停止するには、一覧でタスクを選択し、コマンド バーの **[開始]** または **[停止]** をクリックします。

> [WACOM.NOTE] Web サイトが複数のインスタンスで実行される場合、連続的に実行するタスクはすべてのインスタンス上で実行されます。オンデマンドで実行するタスクおよびスケジュールに従って実行するタスクは、負荷分散目的で Microsoft Azure によって選択された単一のインスタンス上で実行されます。

> [WACOM.NOTE]
> 連続的に実行するタスクについては、Web サイトの [構成] ページで **[常時接続]** を有効にすることをお勧めします。[常時接続] は [基本] と [標準] のモードで使用できる機能であり、この機能を有効にすると、アイドル状態がしばらく続いても Web サイトがアンロードされなくなります。Web サイトが常に読み込まれていれば、連続的に実行するタスクをより高い信頼性で実行できます。 

## <a name="CreateScheduled"></a>スケジュールに従って実行するタスクを作成する

1. スケジュールに従って実行するタスクを作成するには、これまでと同様の手順を使用します。ただし、**[実行方法]** ボックスの一覧では **[スケジュールに従って実行]** を選択します。
	
	![New Scheduled Job][NewScheduledJob]
	
2. **[スケジューラのリージョン]** ボックスの一覧で、ジョブを選択し、ダイアログ ボックスの右下にある右矢印をクリックして、次の画面に進みます。

3. **[ジョブの作成]** ダイアログ ボックスで、希望する**繰り返し**の種類、**[一度だけのジョブ]** または **[定期的なジョブ]** を選択します。
	
	![Schedule Recurrence][SchdRecurrence]
	
4. また、**[開始]** 時刻として、**[現在]** または **[指定時]** を選択します。
	
	![Schedule Start Time][SchdStart]
	
5. 特定の日時に開始するには、**[開始日時]** の各ボックスで、開始時刻の値を選択します。
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. 定期的なジョブを選択した場合は、**[繰り返しの間隔]** で実行頻度を指定し、**[終了日]** で終了日時を指定します。
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. **[週]** を選択した場合は **[特定のスケジュールに従って]** チェック ボックスをオンにし、ジョブを実行する曜日 (複数可) を指定することができます。
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. **[月]** を選択して **[特定のスケジュールに従って]** チェック ボックスをオンにした場合は、**[日]** をクリックして、1 か月のうちジョブを実行する日 (複数可) を指定することができます。 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. **[平日]** を選択した場合は、1 か月のうちジョブを実行する曜日 (複数可) を指定することができます。
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. 最後に、**[発生回数]** を併せて使用すると、1 か月のうち指定した曜日にジョブを実行する週 (1 週目、2 週目、3 週目など) を選択することができます。
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. ジョブ (複数可) を作成したら、それらの名前が状態、スケジュールの種類、その他の情報と共に [Web ジョブ] タブに表示されます。最近実行された 30 件のタスクに関する履歴情報が管理されます。
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>スケジュールされたジョブと Azure スケジューラ

スケジュールされたジョブは Azure スケジューラ ポータルでさらに構成できます。

1.	[Web ジョブ] ページで、ジョブの **[スケジュール]** リンクをクリックして Azure Scheduler のポータル ページに移動します。 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. スケジューラ ページで、ジョブをクリックします。
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. **[ジョブの操作]** ページが開き、ここでジョブをさらに構成できます。 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>ジョブ履歴を表示する

1. Web ジョブ SDK で作成したジョブを含め、ジョブの実行履歴を表示するには、**[ログ]** 列で対応するリンクをクリックします。(必要に応じて、クリップボード アイコンを使用して、ログ ファイル ページの URL をクリップボードにコピーできます)。
	
	![Logs Link][WebJobLogs]
		
2. リンクをクリックすると、タスクの Web ジョブ詳細ページが開きます。このページには、実行されたコマンド、そのコマンドを最後に実行した時間、その実行が成功したか失敗したかが表示されます。**[最近実行されたジョブ]** で、さらに詳細を表示する時間をクリックします。
	
	![WebJobDetails][WebJobDetails]
	
3. **[Web ジョブ実行の詳細]** ページが表示されます。ログの内容のテキストを表示するには、**[出力切り替え]** をクリックします。ログ出力がテキスト形式で表示されます。 
	
	![Web job run details][WebJobRunDetails]
	
4. 出力されたテキストを別のブラウザー ウィンドウで確認するには、**[ダウンロード]** リンクをクリックします。テキスト自体をダウンロードするには、リンクを右クリックしてから、ブラウザーのオプションを使用してファイルの内容を保存します。
	
	![Download log output][DownloadLogOutput]
	
5. ページの上部にある **[Web ジョブ]** リンクを使用すると、履歴ダッシュボードに Web ジョブの一覧を簡単に表示できます。
	
	![Link to web jobs list][WebJobsLinkToDashboardList]
	
	![List of jobs in history dashboard][WebJobsListInJobsDashboard]
	
	これらのリンクのいずれかをクリックすると、選択したジョブの Web ジョブ詳細ページに移動します。


## <a name="WHPNotes"></a>メモ
	
- 2014 年 3 月の時点では、scm (デプロイメント) サイトへの要求がなく Azure で Web サイトのポータルが開かれていない場合、[無料] モードの Web サイトは 20 分後にタイムアウトすることがあります。実際のサイトへの要求があっても、この状態はリセットされません。
- 連続するジョブのコードは、無限ループで実行されるように記述する必要があります。
- 連続するジョブが連続的に実行されるのは、サイトが稼働状態になっているときのみです。
- [基本] モードと [標準] モードには [常時接続] 機能が用意されており、この機能を有効にすると、Web サイトがアイドル状態にならなくなります。

## <a name="NextSteps"></a>次のステップ
 
詳細については、「[Azure Web ジョブの推奨リソース][WebJobsRecommendedResources]」を参照してください。

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
