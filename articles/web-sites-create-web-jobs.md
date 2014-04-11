<properties linkid="web-sites-create-web-jobs" urlDisplayName="Windows Azure で Web ジョブを作成する方法" pageTitle="Windows Azure の Web サイトで Web ジョブを作成する方法" metaKeywords="Windows Azure の Web サイト, Web ジョブ" description="Windows Azure の Web サイトで Web ジョブを作成する方法について説明します。" metaCanonical="" services="web-sites" documentationCenter="" title="Windows Azure の Web サイトで Web ジョブを作成する方法" authors=""  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

#Windows Azure の Web サイトで Web ジョブ機能を使用する方法#

Windows Azure の Web サイトを使用すると、Web サイト内でプログラムやスクリプトを "オンデマンドで実行"、"連続的に実行"、および "スケジュールに従って実行" のうちいずれかの方法で実行できます。後で説明する常時接続機能を有効にしない限り、Windows Azure の Web ジョブの使用に追加コストはかかりません。


## 目次##
- [スクリプトとして使用できるファイルの種類](#acceptablefiles)
- [オンデマンドで実行するタスクを作成する](#CreateOnDemand)
- [連続的に実行するタスクを作成する](#CreateContinuous)
- [スケジュールに従って実行するタスクを作成する](#CreateScheduled)
- [履歴を確認する](#ViewJobHistory)
- [次の手順](#NextSteps)
	- [Windows Azure の Web ジョブ SDK を活用する](#WebJobsSDK)
	- [別の方法で展開する](#AlternateDeployments)
	- [その他のリソース](#AdditionalResources)

<a name="acceptablefiles"></a>
##スクリプトとして使用できるファイルの種類##
実行可能なスクリプトとして使用できるファイルの種類は次のとおりです。

.cmd、.bat、.exe (windows cmd を使用)

.ps1 (powershell を使用)

.sh (bash を使用)

.php (php を使用)

.py (python を使用)

.js (node を使用)

<a name="CreateOnDemand"></a>
##オンデマンドで実行するタスクを作成する##

1. **[Web ジョブ]** ページのコマンド バーで、**[追加]** をクリックします。**[新しい Web ジョブ]** ダイアログ ボックスが表示されます。
	
	![オンデマンドで実行するタスク][OnDemandWebJob]
	
2. **[名前]** ボックスに、タスクの名前を入力します。名前は、先頭を文字または数字にする必要があります。"-" と "_" 以外の特殊文字を使用することはできません。
	
3. **[コンテンツ (zip ファイル - 最大 200 MB)]** ボックスで、スクリプトが含まれている zip ファイルを指定します。この zip ファイルには、実行可能ファイル (.exe .cmd .bat .sh .php .py .js) のほか、プログラムまたはスクリプトの実行に必要な補助ファイルもすべて含まれている必要があります。
	
4. **[実行方法]** ボックスの一覧の **[オンデマンドで実行]** をクリックします。
	
5. スクリプトを Web サイトにアップロードするには、ダイアログ ボックスの右下にあるチェック マークをクリックします。タスクに指定した名前が、一覧に表示されます。
	
	![タスクの一覧][WebJobsList]
	
6. スクリプトを実行するには、一覧で名前を選択し、ポータル ページの下部にあるコマンド バーの **[一度だけ実行する]** をクリックします。
	
	![一度だけ実行する][RunOnce]

<a name="CreateContinuous"></a>
##連続的に実行するタスクを作成する##

1. 連続的に実行するタスクを作成するには、一度だけ実行するタスクを作成する場合と同様の手順を使用します。ただし、**[実行方法]** ボックスの一覧では **[連続的に実行]** を選択します。
	
	![連続的に実行する新しいタスク][NewContinuousJob]
	
2. 連続的に実行するタスクを開始または停止するには、一覧でタスクを選択し、コマンド バーの **[開始]** または **[停止]** をクリックします。

> [WACOM.NOTE] Web サイトが複数のインスタンスで実行される場合、連続的に実行するタスクはすべてのインスタンス上で実行されます。オンデマンドで実行するタスクおよびスケジュールに従って実行するタスクは、負荷分散目的で Windows Azure によって選択された単一のインスタンス上で実行されます。

> [WACOM.NOTE]
> 連続的に実行するタスクについては、Web サイトの [構成] ページで **[常時接続]** を有効にすることをお勧めします。常時接続は標準モードでのみ使用できる機能で、これにより、アイドル状態がしばらく続いても Web サイトのアンロードを防止することができます。Web サイトが常に読み込まれていれば、連続的に実行するタスクをより高い信頼性で実行できます。

<a name="CreateScheduled"></a>
##スケジュールに従って実行するタスクを作成する##
1. スケジュールに従って実行するタスクを作成するには、これまでと同様の手順を使用します。ただし、**[実行方法]** ボックスの一覧では **[スケジュールに従って実行]** を選択します。
	
	![スケジュールに従って実行する新しいジョブ][NewScheduledJob]
	
2. **[スケジューラのリージョン]** ボックスの一覧で、ジョブに応じたリージョンを選択し、ダイアログ ボックスの右下にある右矢印をクリックして、次の画面に進みます。

3. **[ジョブの作成]** ダイアログ ボックスの **[繰り返し]** ボックスで、繰り返しの種類として **[一度だけのジョブ]** または **[定期的なジョブ]** を選択します。
	
	![繰り返しのスケジュール設定][SchdRecurrence]
	
4. また、**[開始]** ボックスの一覧で、開始時刻として **[現在]** または **[指定時]** を選択します。
	
	![開始時刻の指定][SchdStart]
	
5. 特定の日時に開始するには、**[開始日時]** の各ボックスで、開始時刻の値を選択します。
	
	![指定時の開始を指定][SchdStartOn]
	
6. 定期的なジョブを選択した場合は、**[繰り返しの間隔]** で実行頻度を指定し、**[終了日]** で終了日時を指定します。
	
	![繰り返しのスケジュール設定][SchdRecurEvery]
	
7. **[週]** を選択した場合は **[特定のスケジュールに従って]** チェック ボックスをオンにし、ジョブを実行する曜日 (複数も可能) を指定することができます。
	
	![曜日の指定][SchdWeeksOnParticular]
	
8. **[月]** を選択して **[特定のスケジュールに従って]** チェック ボックスをオンにした場合は、**[日]** をクリックして、1 か月のうちジョブを実行する日 (複数も可能) を指定することができます。
	
	![1 か月のうち特定の日を指定][SchdMonthsOnPartDays]
	
9. **[平日]** を選択した場合は、1 か月のうちジョブを実行する曜日 (複数も可能) を指定することができます。
	
	![1 か月のうち特定の曜日を指定][SchdMonthsOnPartWeekDays]
	
10. 最後に、**[発生回数]** を併せて使用すると、1 か月のうち指定した曜日にジョブを実行する週 (1 週目、2 週目、3 週目など) を選択することができます。
	
	![1 か月のうち特定の週の特定の曜日を指定][SchdMonthsOnPartWeekDaysOccurences]

<a name="ViewJobHistory"></a>
##タスク履歴の確認##
タスクの実行履歴を確認するには、ダッシュボードの **[ログ]** 列にある、該当するリンクをクリックします (クリップボード アイコンを使用して、ログ ファイル ページの URL をクリップボードにコピーすることもできます)。

![ログのリンク][WebJobLogs]
	
リンクをクリックすると、タスクの履歴ページが開きます。

![ジョブの実行履歴][TriggeredHistory]

ログの内容を確認するには、**[出力ファイル]** 列の **[ダウンロード]** リンクをクリックします。

出力ログはテキスト形式で、次のような内容が含まれています。

	[01/08/2014 03:12:27 > 543efe: SYS INFO] Status changed to Initializing
	[01/08/2014 03:12:27 > 543efe: SYS INFO] Run script 'run.cmd' with script host - 'WindowsScriptHost'
	[01/08/2014 03:12:28 > 543efe: SYS INFO] Status changed to Running
	[01/08/2014 03:12:28] 
	[01/08/2014 03:12:28] C:\DWASFiles\Sites\Contoso\Temp\jobs\triggered\OnDemandWebJob1\knvcab4m.522>ECHO "On Demand Task Test" 
	[01/08/2014 03:12:28] "On Demand Task Test"
	[01/08/2014 03:12:28 > 543efe: SYS INFO] Status changed to Success

最近実行された 30 件のタスクに関する履歴情報が管理されます。

<a name="NextSteps"></a>
##次の手順##

<a name="WebJobsSDK"></a>
###Windows Azure の Web ジョブ SDK を活用する###
Windows Azure の Web ジョブ SDK を使用すると、Windows Azure の Web サイトにバックグラウンド プロセスを追加するタスクが容易になります。この SDK は Windows Azure のストレージを統合し、キュー、BLOB、またはテーブルに項目が追加されると、プログラム内の関数を呼び出します。ダッシュボードでは、この SDK を使用して作成したプログラムを対象として、豊富な監視機能および診断機能が提供されます。監視機能および診断機能 SDK に組み込まれており、プログラムに特別なコードを追加する必要はありません。
 
詳細については、チュートリアル「[Getting Started with Windows Azure の Web ジョブ SDK (Windows Azure の Web ジョブ SDK の概要)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)」を参照してください。このチュートリアルでは、Web ジョブ SDK の機能概要を示し、Hello World という単純なバックグラウンド プロセスを作成して実行する方法を説明します。

Windows Azure の Web ジョブ SDK で作成されたコマンド ライン アプリのサンプルの使用例については、「[Introducing Windows Azure の Web ジョブ (Windows Azure の Web ジョブ の紹介)][HanselIntro]」を参照してください。

<a name="AlternateDeployments"></a>
###別の方法で展開する###
スクリプトのアップロードに Web ジョブ ポータル ページを使用しない場合は、FTP、git、または Web 展開を使用できます。詳細については、「[How to deploy Windows Azure の Web ジョブ (Windows Azure の Web ジョブ を展開する方法)][AmitDeploy]」および「[Git deploying a .NET console app to Azure using Web ジョブ (Web ジョブの使用による Azure への .NET コンソール アプリの Git 展開)][AmitConsole]」を参照してください。

<a name="AdditionalResources"></a>
###その他のリソース###
その他のリソースについては、注釈付きのリンク一覧を参照してください: [Using the WebJobs feature of Windows Azure Web Sites (Windows Azure の Web サイトの Web ジョブ機能の使用)][RickWebJobsCurah]



[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx

[HanselIntro]:http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx

[AmitDeploy]:http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs

[AmitConsole]:http://blog.amitapple.com/post/73574681678/git-deploy-console-app

[RickWebJobsCurah]:http://go.microsoft.com/fwlink/?LinkId=390226


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
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[TriggeredHistory]: ./media/web-sites-create-web-jobs/15aTriggeredHistory.png


