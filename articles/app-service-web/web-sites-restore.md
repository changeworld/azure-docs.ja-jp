<properties 
	pageTitle="Azure App Service でのアプリの復元" 
	description="アプリをバックアップから復元する方法について説明します。" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="cephalin"/>

# Azure App Service でのアプリの復元

この記事では、[App Service](app-service-value-prop-what-is) の Backup 機能を使用して以前にバックアップした App Service アプリを復元する方法について説明します。詳細については、「[App Service の Backup](web-sites-backup.md)」を参照してください。

App Service の復元機能を使用すると、リンクされたデータベース (SQL Database または MySQL) をオンデマンドで使用してアプリを以前の状態に戻したり、元のアプリのいずれかのバックアップに基づいて新しいアプリを作成したりすることができます。最新バージョンと並行して実行される新しいアプリを作成すると、A/B テストを実施する場合に役立ちます。

App Service の復元機能 ([Azure ポータル](http://portal.azure.com)の **[Backup]** ブレードで利用可能) は、Standard および Premium の価格レベルでのみ使用できます。Standard または Premium レベルを使用しているアプリをスケーリングする方法については、「[Azure App Service のアプリをスケーリングする](web-sites-scale.md)」を参照してください。Premium レベルでは、日次バックアップの実行回数を Standard レベルよりも多く設定できます。

<a name="PreviousBackup"></a>
## 以前に作成したバックアップからアプリを復元するには

1. Azure ポータルにあるアプリの **[設定]** ブレードで、**[Backup]** をクリックして **[バックアップ]** ブレードを表示します。コマンド バーで、**[今すぐ復元]** をクリックします。 
	
	![今すぐ復元の選択][ChooseRestoreNow]

3. **[復元]** ブレードで、最初にバックアップ ソースを選択します。

	![](./media/web-sites-restore/021ChooseSource.png)
	
	**[アプリのバックアップ]** オプションには、アプリ自体で直接作成されたすべてのバックアップが表示されます。アプリが認識するのはこれらのバックアップのみです。1 つのバックアップを簡単に選択できます。**[Storage]** オプションを使用して、**[Backup]** ブレードで構成されたストレージ アカウントおよびコンテナーから実際のバックアップ zip ファイルを選択できます。コンテナー内に他のアプリからのバックアップ ファイルが存在する場合、それらを選択して復元することもできます。

4. 次に、**[復元先]** でアプリの復元先を指定します。

	![](./media/web-sites-restore/022ChooseDestination.png)
	
	>[AZURE.WARNING]**[上書き]** を選択すると、既存のアプリに関連するすべてのデータが削除されます。**[OK]** をクリックする前に、実行する操作内容が正しいことを確認します。
	
	**[既存のアプリ]** を選択して、アプリのバックアップを同じリソース グループ内の別のアプリに復元することができます。このオプションを使用する前に、リソース グループ内に別のアプリを作成済みであり、データベース構成を、アプリのバックアップ内に定義されている構成にミラーリングしている必要があります。
	
5. **[OK]** をクリックします。

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
	
1. アプリの復元操作が成功したかどうかについて詳細を確認するには、メインの **[参照]** ブレードで **[監査ログ]** を選択します。 
	
	**[監査ログ]** ブレードには、すべての操作と、レベル、状態、リソース、および時刻が表示されます。
	
2. ブレードをスクロールして、アプリに関連する操作を検索します。
3. 操作に関する追加情報を表示するには、一覧で操作を選択します。
	
詳細ブレードに、操作に関連する利用可能な情報が表示されます。
	
>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 次のステップ

REST API を使用して、App Service アプリのバックアップと復元を実行することもできます (「[REST を使用して App Service アプリのバックアップと復元を実行する](websites-csm-backup.md)」をご覧ください)。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。

<!-- IMAGES -->
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
 

<!---HONumber=AcomDC_0114_2016-->