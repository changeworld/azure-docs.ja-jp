<properties linkid="web-sites-create-web-jobs" urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Use WebJobs to run background tasks in Microsoft Azure Websites" metaKeywords="Microsoft Azure Web Sites, Web Jobs, background tasks" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Microsoft Azure の Web サイトで Web ジョブを使用してバックグラウンド タスクを実行する方法

Microsoft Azure の Web サイトを使用すると、Web サイト内でプログラムやスクリプトを "オンデマンドで実行"、"連続的に実行"、および "スケジュールに従って実行" のうちいずれかの方法で実行できます。後で説明する常時接続機能を有効にしない限り、Microsoft Azure の Web ジョブの使用に追加コストはかかりません。

## 目次

-   [スクリプトとして使用できるファイルの種類][スクリプトとして使用できるファイルの種類]
-   [オンデマンドで実行するタスクを作成する][オンデマンドで実行するタスクを作成する]
-   [連続的に実行するタスクを作成する][連続的に実行するタスクを作成する]
-   [スケジュールに従って実行するタスクを作成する][スケジュールに従って実行するタスクを作成する]
    -   [スケジュールされたジョブと Azure スケジューラ][スケジュールされたジョブと Azure スケジューラ]
-   [ジョブ履歴を表示する][ジョブ履歴を表示する]
-   [メモ][メモ]
-   [次のステップ][次のステップ]
    -   [Microsoft Azure の Web ジョブ SDK を活用する][Microsoft Azure の Web ジョブ SDK を活用する]
    -   [別の方法で展開する][別の方法で展開する]
    -   [その他のリソース][その他のリソース]

<a name="acceptablefiles"></a>

## スクリプトとして使用できるファイルの種類

実行可能なスクリプトとして使用できるファイルの種類は次のとおりです。

.cmd、.bat、.exe (windows cmd を使用)

.ps1 (powershell を使用)

.sh (bash を使用)

.php (php を使用)

.py (python を使用)

.js (node を使用)

<a name="CreateOnDemand"></a>

## オンデマンドで実行するタスクを作成する

1.  **[Web ジョブ]** ページのコマンド バーで、**[追加]** をクリックします。**[新しいジョブ]** ダイアログ ボックスが表示されます。

    ![オンデマンドで実行するタスク][オンデマンドで実行するタスク]

2.  **[名前]** ボックスに、タスクの名前を入力します。名前は、先頭を文字または数字にする必要があります。"-" と "\_" 以外の特殊文字を使用することはできません。

3.  **[コンテンツ (zip ファイル - 最大 100MB)]** ボックスで、スクリプトが含まれている zip ファイルを指定します。この zip ファイルには、実行可能ファイル (.exe .cmd .bat .sh .php .py .js) のほか、プログラムまたはスクリプトの実行に必要な補助ファイルもすべて含まれている必要があります。

4.  **[実行方法]** ボックスの一覧の **[オンデマンドで実行]** をクリックします。

5.  スクリプトを Web サイトにアップロードするには、ダイアログ ボックスの右下にあるチェック マークをクリックします。タスクに指定した名前が、一覧に表示されます。

    ![タスクの一覧][タスクの一覧]

6.  スクリプトを実行するには、一覧で名前を選択し、ポータル ページの下部にあるコマンド バーの **[一度だけ実行する]** をクリックします。

    ![一度だけ実行する][一度だけ実行する]

<a name="CreateContinuous"></a>

## 連続的に実行するタスクを作成する

1.  連続的に実行するタスクを作成するには、一度だけ実行するタスクを作成する場合と同様の手順を使用します。ただし、**[実行方法]** ボックスの一覧では **[連続的に実行]** を選択します。

    ![連続的に実行する新しいタスク][連続的に実行する新しいタスク]

2.  連続的に実行するタスクを開始または停止するには、一覧でタスクを選択し、コマンド バーの **[開始]** または **[停止]** をクリックします。

> [WACOM.NOTE] Web サイトが複数のインスタンスで実行される場合、連続的に実行するタスクはすべてのインスタンス上で実行されます。オンデマンドで実行するタスクおよびスケジュールに従って実行するタスクは、負荷分散目的で Microsoft Azure によって選択された単一のインスタンス上で実行されます。

> [WACOM.NOTE]
> 連続的に実行するタスクについては、Web サイトの [構成] ページで **[常時接続]** を有効にすることをお勧めします。[常時接続] は [基本] と [標準] のモードで使用できる機能であり、この機能を有効にすると、アイドル状態がしばらく続いても Web サイトがアンロードされなくなります。Web サイトが常に読み込まれていれば、連続的に実行するタスクをより高い信頼性で実行できます。

<a name="CreateScheduled"></a>

## スケジュールに従って実行するタスクを作成する

1.  スケジュールに従って実行するタスクを作成するには、これまでと同様の手順を使用します。ただし、**[実行方法]** ボックスの一覧では **[スケジュールに従って実行]** を選択します。

    ![スケジュールに従って実行する新しいジョブ][スケジュールに従って実行する新しいジョブ]

2.  **[スケジューラのリージョン]** ボックスの一覧で、ジョブに応じたリージョンを選択し、ダイアログ ボックスの右下にある右矢印をクリックして、次の画面に進みます。

3.  **[ジョブの作成]** ダイアログ ボックスの **[繰り返し]** ボックスで、繰り返しの種類として **[一度だけのジョブ]** または **[定期的なジョブ]** を選択します。

    ![繰り返しのスケジュール設定][繰り返しのスケジュール設定]

4.  また、**[開始]** ボックスの一覧で、開始時刻として **[現在]** または **[指定時]** を選択します。

    ![開始時刻の指定][開始時刻の指定]

5.  特定の日時に開始するには、**[開始日時]** の各ボックスで、開始時刻の値を選択します。

    ![指定時の開始を指定][指定時の開始を指定]

6.  定期的なジョブを選択した場合は、**[繰り返しの間隔]** で実行頻度を指定し、**[終了日]** で終了日時を指定します。

    ![繰り返しのスケジュール設定][1]

7.  **[週]** を選択した場合は **[特定のスケジュールに従って]** チェック ボックスをオンにし、ジョブを実行する曜日 (複数も可能) を指定することができます。

    ![曜日の指定][曜日の指定]

8.  **[月]** を選択して **[特定のスケジュールに従って]** チェック ボックスをオンにした場合は、**[日]** をクリックして、1 か月のうちジョブを実行する日 (複数も可能) を指定することができます。

    ![1 か月のうち特定の日を指定][1 か月のうち特定の日を指定]

9.  **[平日]** を選択した場合は、1 か月のうちジョブを実行する曜日 (複数も可能) を指定することができます。

    ![1 か月のうち特定の曜日を指定][1 か月のうち特定の曜日を指定]

10. 最後に、**[発生回数]** を併せて使用すると、1 か月のうち指定した曜日にジョブを実行する週 (1 週目、2 週目、3 週目など) を選択することができます。

    ![1 か月のうち特定の週の特定の曜日を指定][1 か月のうち特定の週の特定の曜日を指定]

11. ジョブ (複数可) を作成したら、それらの名前が状態、スケジュールの種類、その他の情報と共に [Web ジョブ] タブに表示されます。最近実行された 30 件のタスクに関する履歴情報が管理されます。

    ![ジョブの一覧][ジョブの一覧]

<a name="Scheduler"></a>

### スケジュールされたジョブと Azure スケジューラ

スケジュールされたジョブは Azure スケジューラ ポータルでさらに構成できます。

1.  [Web ジョブ] ページで、ジョブの **[スケジュール]** リンクをクリックして Azure スケジューラ ポータル ページに移動します。

    ![Azure スケジューラへのリンク][Azure スケジューラへのリンク]

2.  スケジューラ ページで、ジョブをクリックします。

    ![スケジューラ ポータル ページ上のジョブ][スケジューラ ポータル ページ上のジョブ]

3.  **[ジョブの操作]** ページが開き、ここでジョブをさらに構成できます。

    ![スケジューラの ジョブの操作 ページ][]

<!-- ================ ViewJobHistory ================= -->

<a name="ViewJobHistory"></a>

## ジョブ履歴を表示する

1.  Web ジョブ SDK で作成したジョブを含め、ジョブの実行履歴を表示するには、**[ログ]** 列で対応するリンクをクリックします。(必要に応じて、クリップボード アイコンを使用して、ログ ファイル ページの URL をクリップボードにコピーできます)。

    ![ログのリンク][ログのリンク]

2.  リンクをクリックすると、タスクの Web ジョブ詳細ページが開きます。このページには、実行されたコマンド、そのコマンドを最後に実行した時間、その実行が成功したか失敗したかが表示されます。**[最近実行されたジョブ]** で、さらに詳細を表示する時間をクリックします。

    ![Web ジョブ詳細][Web ジョブ詳細]

3.  **Web ジョブ実行の詳細** ページが表示されます。ログの内容のテキストを表示するには、**[出力切り替え]** をクリックします。ログ出力がテキスト形式で表示されます。

    ![Web ジョブ実行の詳細][Web ジョブ実行の詳細]

4.  出力されたテキストを別のブラウザー ウィンドウで確認するには、**[ダウンロード]** リンクをクリックします。テキスト自体をダウンロードするには、リンクを右クリックしてから、ブラウザーのオプションを使用してファイルの内容を保存します。

    ![ログ出力のダウンロード][ログ出力のダウンロード]

5.  ページの上部にある **[Web ジョブ]** リンクを使用すると、履歴ダッシュボードに Web ジョブの一覧を簡単に表示できます。

    ![Web ジョブの一覧へのリンク][Web ジョブの一覧へのリンク]

    ![履歴ダッシュボードに表示されたジョブの一覧][履歴ダッシュボードに表示されたジョブの一覧]

    これらのリンクのいずれかをクリックすると、選択したジョブの Web ジョブ詳細ページに移動します。

<a name="WHPNotes"></a>

## メモ

-   2014 年 3 月の時点では、scm (展開) サイトへの要求がなく Azure で Web サイトのポータルが開かれていない場合、[無料] モードの Web サイトは 20 分後にタイムアウトすることがあります。実際のサイトへの要求があっても、この状態はリセットされません。

-   連続するジョブのコードは、無限ループで実行されるように記述する必要があります。

-   連続するジョブが連続的に実行されるのは、サイトが稼働状態になっているときのみです。

-   [基本] モードと [標準] モードには [常時接続] 機能が用意されており、この機能を有効にすると、Web サイトがアイドル状態にならなくなります。

<a name="NextSteps"></a>

## 次のステップ

<!-- ======= Do More with the Microsoft Azure WebJobs SDK ======== -->

<a name="WebJobsSDK"></a>

### Microsoft Azure の Web ジョブ SDK を活用する

Microsoft Azure の Web ジョブ SDK を使用すると、Azure ストレージ、キュー、BLOB、テーブル、または Azure サービス バス キューと連携する Web ジョブのプログラミング タスクが容易になります。Azure ポータルに新たに統合されたダッシュボードでは、この SDK を使用して作成したプログラムを対象として、豊富な監視機能および診断機能が提供されます。監視機能および診断機能 SDK に組み込まれており、プログラムに特別なコードを追加する必要はありません。

詳細については、チュートリアル「[Getting Started with Microsoft Azure WebJobs SDK (Microsoft Azure の Web ジョブ SDK の概要)][Getting Started with Microsoft Azure WebJobs SDK (Microsoft Azure の Web ジョブ SDK の概要)]」を参照してください。

<!-- =========== Alternative Methods of Deployment ============= -->

<a name="AlternateDeployments"></a>

### 別の方法で展開する

Visual Studio 2013 には、コンソール アプリケーション プロジェクトのデプロイを Web ジョブとして自動化する機能が含まれています。詳細については、「[How to Deploy Azure WebJobs to Azure Websites (Azure の Web ジョブを Azure の Web サイトにデプロイする方法)][How to Deploy Azure WebJobs to Azure Websites (Azure の Web ジョブを Azure の Web サイトにデプロイする方法)]」を参照してください。

Web ジョブのデプロイには、FTP、Git、Web 展開も使用できます。詳細については、「[Git deploying a .NET console app to Azure using WebJobs (Web ジョブの使用による Azure への .NET コンソール アプリケーションの Git デプロイ)][Git deploying a .NET console app to Azure using WebJobs (Web ジョブの使用による Azure への .NET コンソール アプリケーションの Git デプロイ)]」および「[How to deploy Windows Azure WebJobs (Windows Azure の Web ジョブ をデプロイする方法)][How to deploy Windows Azure WebJobs (Windows Azure の Web ジョブ をデプロイする方法)]」を参照してください。

<a name="AdditionalResources"></a>

### その他のリソース

-   Web ジョブ機能の注釈付きのリンク一覧については、「[Azure WebJobs - Recommended Resources (Azure Web ジョブ - 推奨リソース)][Azure WebJobs - Recommended Resources (Azure Web ジョブ - 推奨リソース)]」を参照してください。

-   Web ジョブ関連のビデオ:

    [Azure WebJobs 101 - Basic WebJobs with Jamie Espinosa (Azure Web ジョブ 101 - 基本的な Web ジョブ機能に関する Jamie Espinosa のビデオ)][Azure WebJobs 101 - Basic WebJobs with Jamie Espinosa (Azure Web ジョブ 101 - 基本的な Web ジョブ機能に関する Jamie Espinosa のビデオ)]

    [Azure WebJobs 102 - Scheduled WebJobs and the WebJobs Dashboard with Jamie Espinosa (Azure Web ジョブ 102 - スケジュールされた Web ジョブと Web ジョブ ダッシュボードに関する Jamie Espinosa のビデオ)][Azure WebJobs 102 - Scheduled WebJobs and the WebJobs Dashboard with Jamie Espinosa (Azure Web ジョブ 102 - スケジュールされた Web ジョブと Web ジョブ ダッシュボードに関する Jamie Espinosa のビデオ)]

    [Azure Scheduler 101 - Kevin Lam explains how to schedule stuff (Azure スケジューラ 101 - スケジュール全般に関する Kevin Lam のビデオ)][Azure Scheduler 101 - Kevin Lam explains how to schedule stuff (Azure スケジューラ 101 - スケジュール全般に関する Kevin Lam のビデオ)]

### はじめに

Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト][Microsoft Azure の無料評価版サイト]」を参照してください。

<!-- LINKS --> 
<!-- IMAGES -->

  [スクリプトとして使用できるファイルの種類]: #acceptablefiles
  [オンデマンドで実行するタスクを作成する]: #CreateOnDemand
  [連続的に実行するタスクを作成する]: #CreateContinuous
  [スケジュールに従って実行するタスクを作成する]: #CreateScheduled
  [スケジュールされたジョブと Azure スケジューラ]: #Scheduler
  [ジョブ履歴を表示する]: #ViewJobHistory
  [メモ]: #WHPNotes
  [次のステップ]: #NextSteps
  [Microsoft Azure の Web ジョブ SDK を活用する]: #WebJobsSDK
  [別の方法で展開する]: #AlternateDeployments
  [その他のリソース]: #AdditionalResources
  [オンデマンドで実行するタスク]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
  [タスクの一覧]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
  [一度だけ実行する]: ./media/web-sites-create-web-jobs/13RunOnce.png
  [連続的に実行する新しいタスク]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
  [スケジュールに従って実行する新しいジョブ]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
  [繰り返しのスケジュール設定]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
  [開始時刻の指定]: ./media/web-sites-create-web-jobs/06SchdStart.png
  [指定時の開始を指定]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
  [1]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
  [曜日の指定]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
  [1 か月のうち特定の日を指定]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
  [1 か月のうち特定の曜日を指定]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
  [1 か月のうち特定の週の特定の曜日を指定]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
  [ジョブの一覧]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
  [Azure スケジューラへのリンク]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
  [スケジューラ ポータル ページ上のジョブ]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
  [スケジューラの ジョブの操作 ページ]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
  [ログのリンク]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
  [Web ジョブ詳細]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
  [Web ジョブ実行の詳細]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
  [ログ出力のダウンロード]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
  [Web ジョブの一覧へのリンク]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
  [履歴ダッシュボードに表示されたジョブの一覧]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
  [Getting Started with Microsoft Azure WebJobs SDK (Microsoft Azure の Web ジョブ SDK の概要)]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [How to Deploy Azure WebJobs to Azure Websites (Azure の Web ジョブを Azure の Web サイトにデプロイする方法)]: http://azure.microsoft.com/ja-jp/documentation/articles/websites-dotnet-deploy-webjobs
  [Git deploying a .NET console app to Azure using WebJobs (Web ジョブの使用による Azure への .NET コンソール アプリケーションの Git デプロイ)]: http://blog.amitapple.com/post/73574681678/git-deploy-console-app
  [How to deploy Windows Azure WebJobs (Windows Azure の Web ジョブ をデプロイする方法)]: http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs
  [Azure WebJobs - Recommended Resources (Azure Web ジョブ - 推奨リソース)]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Azure WebJobs 101 - Basic WebJobs with Jamie Espinosa (Azure Web ジョブ 101 - 基本的な Web ジョブ機能に関する Jamie Espinosa のビデオ)]: http://www.windowsazure.com/ja-jp/documentation/videos/azure-webjobs-basics/
  [Azure WebJobs 102 - Scheduled WebJobs and the WebJobs Dashboard with Jamie Espinosa (Azure Web ジョブ 102 - スケジュールされた Web ジョブと Web ジョブ ダッシュボードに関する Jamie Espinosa のビデオ)]: http://www.windowsazure.com/ja-jp/documentation/videos/azure-webjobs-schedule-and-dashboard/
  [Azure Scheduler 101 - Kevin Lam explains how to schedule stuff (Azure スケジューラ 101 - スケジュール全般に関する Kevin Lam のビデオ)]: http://www.windowsazure.com/ja-jp/documentation/videos/azure-scheduler-how-to/
  [Microsoft Azure の無料評価版サイト]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
