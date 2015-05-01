<properties 
	pageTitle="Azure App Service での Web アプリの復元" 
	description="Web アプリをバックアップから復元する方法について説明します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Azure App Service での Web アプリの復元

この記事では、[App Service Web アプリ](http://go.microsoft.com/fwlink/?LinkId=529714)のバックアップ機能を使用して以前にバックアップした Web アプリを復元する方法について説明します。詳細については、「[App Service Web Apps Backups (App Service Web アプリのバックアップ)](web-sites-backup.md). 」を参照してください。

Web アプリの復元機能では、Web アプリをオンデマンドで以前の状態に戻したり、元の Web アプリのバックアップに基づいて新しい Web アプリを作成することができます。最新バージョンと並列で実行する新しい Web アプリを作成すると、A/B テストを実施する場合に役立ちます。

Web アプリの復元機能 ([Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)の**バックアップ** ブレードで利用可能) は、標準およびプレミアム モードでのみ使用できます。アプリを標準やプレミアム モードに拡張する方法については、「[Scale a web app in Azure App Service (Azure App Service での Web アプリの拡張)](web-sites-scale.md).」を参照してください。 
プレミアム モードでは、日次バックアップの実行回数を標準モードよりも多く設定できます。

<a name="PreviousBackup"></a>
## 以前に作成したバックアップから Web アプリを復元するには

1. Azure ポータルにある Web アプリの **[設定]** ブレードで、**[バックアップ]** オプションをクリックして **[バックアップ]** ブレードを表示します。このブレードで画面をスクロールし、バックアップ リストの**バックアップ時間**と**状態**を見ながらバックアップ項目を選択します。
	
	![バックアップ ソースの選択][ChooseBackupSource]
	
2. **[バックアップ]** ブレードの上部にある **[今すぐ復元]** を選択します。 

	![復元を今すぐ選択][ChooseRestoreNow]

3. 既存の Web アプリを復元するには、**[復元]** ブレードで表示されているすべての詳細を確認し、**[OK]** をクリックします。 
	
**[復元]** ブレードから **[Web アプリ]** を選択し、**[Web アプリを新規作成]** を選択することで、 Web アプリを新規の Web アプリに復元することもできます。
	
<a name="StorageAccount"></a>
## ストレージ アカウントからバックアップをダウンロードまたは削除するには
	
1. Azure ポータルのメインの **[参照]** ブレードで、**[ストレージ アカウント]** を選択します。
	
	既存のストレージ アカウントの一覧が表示されます。 
	
2. ダウンロードまたは削除するバックアップが含まれているストレージ アカウントを選択します。
	
	**[ストレージ]** ブレードが表示されます。

3. **[ストレージ]** ブレードで **[コンテナー]** を選択し、**[コンテナー]** ブレードを表示します。
	
	コンテナーの一覧が表示されます。この一覧には、コンテナーの URL と最終変更日も表示されます。
	
	![コンテナーの表示][ViewContainers]

4. 一覧でコンテナーを選択してブレードを表示すると、ファイル名の一覧と各ファイルのサイズが表示されます。
	
5. ファイルを選択すると、ファイルを**ダウンロード**するか**削除**するかを選択できます。主に使用するファイル タイプは、.zip ファイルと .xml ファイルの 2 種類です。 

<a name="OperationLogs"></a>
## 監査ログを表示するには
	
1. Web アプリケーションの復元操作が成功したかどうかについて詳細を確認するには、メインの **[参照]** ブレードで **[監査ログ]** を選択します。 
	
	**[監査ログ]** ブレードには、すべての操作と、レベル、状態、リソース、および時刻が表示されます。
	
2. ブレードをスクロールして、Web アプリに関連する操作を検索します。
3. 操作に関する追加情報を表示するには、一覧で操作を選択します。
	
詳細ブレードに、操作に関連する利用可能な情報が表示されます。
	
>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Try App Service (App Service を試す)](http://go.microsoft.com/fwlink/?LinkId=523751)」にアクセスすれば、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
	
## 変更に関する情報
* Websites から App Service への変更に関するガイドは、次を参照してください:[Azure App Service and Its Impact on Existing Azure Services (Azure App Service と既存の Azure サービス)](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更に関するガイドは、次を参照してください:[Reference for navigating the preview portal (プレビュー ポータルの移動に関するリファレンス)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png

<!--HONumber=49-->