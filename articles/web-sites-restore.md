<properties 
	pageTitle="Microsoft Azure Website の復元" 
	description="バックアップから Azure の Web サイトを復元する方法を説明します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Microsoft Azure Website の復元

この記事では、Azure Websites のバックアップ機能を使用して以前にバックアップした Web サイトを復元する方法について説明します。詳細については、[Microsoft Azure の Web サイトのバックアップに関するページ](http://azure.microsoft.com/documentation/articles/web-sites-backup/)をご覧ください。 

Azure Websites の復元機能を使用して、オンデマンドで Web サイトを以前の状態に復元したり、元のサイトのいずれかのバックアップに基づいて新しい Web サイトを作成できます。最新バージョンと並列で実行する新しい Web サイトを作成すると、A/B テストを実施する場合に役立ちます。

Azure Websites のポータルで [バックアップ] タブに配置されている復元機能は、標準モードでのみ使用可能です。

##この記事の内容
- [以前に作成したバックアップから、Azure の Web サイトを復元するには](#PreviousBackup)
- [ストレージ アカウントから、Azure の Web サイトを直接復元するには](#StorageAccount)
- [Web サイト復元設定の選択と復元操作の開始](#RestoreSettings)
- [操作ログの表示](#OperationLogs)


<a name="PreviousBackup"></a>
##以前に作成したバックアップから、Azure Website を復元するには

1. **[バックアップ]** タブで、ポータル ページの下部にあるコマンド バーの **[今すぐ復元]** をクリックします。**[今すぐ復元]** ダイアログ ボックスが表示されます。
	
	![Choose backup source][ChooseBackupSource]
	
2. **[バックアップ ソースの選択]** で、**[この Web サイトの前のバックアップ]** をクリックします。
3. 復元するバックアップの日付を選択し、右矢印をクリックして続行します。
4. この記事の後半に掲載されている「[Web サイト復元設定の選択]」の(#RestoreSettings) 手順に従います。

<a name="StorageAccount"></a>
##ストレージ アカウントから、Azure Website を直接復元するには

1. **[バックアップ]** タブで、ポータル ページの下部にあるコマンド バーの **[今すぐ復元]** をクリックします。**[今すぐ復元]** ダイアログ ボックスが表示されます。
	
	![Choose backup source][ChooseBackupSource]
	
2. **[バックアップ ソースの選択]** で、**[ストレージ アカウント ファイル]** をクリックします。ここでは、ストレージ アカウント ファイルに対応する URL を直接指定するか、BLOB ストレージに移動するためのフォルダー アイコンをクリックしてバックアップ ファイルを指定します。この例では、フォルダー アイコンをクリックします。
	
	![Storage Account File][StorageAccountFile]
	
3. フォルダー アイコンをクリックし、**[クラウド ストレージの参照]** ダイアログ ボックスを開きます。
	
	![Browse Cloud Storage][BrowseCloudStorage]
	

4. 使用するストレージ アカウントの名前を展開し、バックアップを格納している **websitebackups** を選択します。
5. 復元するバックアップを格納している zip ファイルを選択し、**[開く]** をクリックします。
6. ストレージ アカウント ファイルが選択され、[ストレージ アカウント] ボックス内に表示されます。右矢印をクリックして次へ進みます。
	
	![Storage Account File Selected][StorageAccountFileSelected]
	
7. この後に続く「[Web サイト復元設定の選択と復元操作の開始]」セクションに進みます(#RestoreSettings)。

<a name="RestoreSettings"></a>
##Web サイト復元設定の選択と復元操作の開始
1. **[Web サイト復元設定の選択]** の **[復元先]** で、**[現在の Web サイト]** または **[新しい Web サイト インスタンス]** を選択します。
	
	![Choose your web site restore settings][ChooseRestoreSettings]
	
	**[現在の Web サイト]** を選択した場合は、選択したバックアップによって、既存の Web サイトが上書きされます (中断を伴う復元)。選択したバックアップの時刻より後にこの Web サイトに対して加えたすべて変更は完全に削除され、復元操作を元に戻すことはできません。復元操作中に、現在の Web サイトは一時的に利用できなくなります。この影響に関する警告が表示されます。
	
	**[新しい Web サイト インスタンス]** を選択した場合は、既に指定した名前が存在するのと同じリージョンに、新しい Web サイトが作成されます(既定では、新しいサイトの名前は **restored-***oldWebSiteName* になります)。 
	
	復元先サイトには、元のサイトと同じコンテンツ、および元のサイトに対応するポータルで実行されたのと同じ構成が格納されます。また、復元先サイトに含めるデータベースを次の手順で選択しますが、それらのデータベースすべても格納されます。
2. Web サイトと共にデータベースを復元する場合は、**[含まれるデータベース]** で、**[復元先]** の下にあるドロップダウンを使用して、データベースの復元先になるデータベース サーバーの名前を選択します。復元先として使用する新しいデータベース サーバーを作成する方針を選択すること、または既定である **[復元しない]** を使用してデータベースを復元しない方針を選択できます。 
	
	サーバー名を選択した後、**[データベース名]** ボックスで、復元のターゲット データベースの名前を指定します。
	
	復元に少なくとも 1 つのデータベースが含まれている場合は、**[接続文字列の自動調整]** を選択して、バックアップに保存されている接続文字列を更新し、必要に応じて新しいデータベースまたはデータベース サーバーを指すようにできます復元が完了した後、データベースに関連しているすべての機能が期待どおりに動作していることを確認する必要があります。
	
	![Choose database server host][ChooseDBServer]
	
	> [AZURE.NOTE] SQL データベースを、同じデータベース名を使用して、同じ SQL Server に復元することはできません。別のデータベース名を選択するか、データベースの復元先として別の SQL Server ホストを選択する必要があります。 
	
	> [AZURE.NOTE] MySQL データベースを、同じデータベース名を使用して、同じサーバーに復元することは可能です。ただし、その場合は MySQL データベースに格納されている既存のコンテンツが消去されることにご注意ください。	
	
3. 既存のデータベースを復元する場合は、ユーザー名とパスワードを入力する必要があります。新しいデータベースに復元する場合は、新しいデータベース名を入力する必要があります。
	
	![Restore to a new SQL database][RestoreToNewSQLDB]
	
	右矢印をクリックして次へ進みます。	
4. 新しいデータベースを作成する場合は、次のダイアログ ボックスで、データベースに対応する資格情報とその他の初期構成情報を入力する必要があります。新しい SQL データベースを次の例に示します(新しい MySQL データベースに対応するオプションはある程度異なります)。
	
	![New SQL database settings][NewSQLDBConfig]
	
5. チェックマークをクリックして、復元操作を開始します。操作が完了した時点で、ポータル内にある Web サイトの一覧に新しい Web サイト インスタンス (既存ではなく新規サイトの復元オプションを選択した場合) が表示されます。
	
	![Restored Contoso web site][RestoredContosoWebSite]

<a name="OperationLogs"></a>
##操作ログの表示
	
1. Web サイトの復元操作の成功または失敗に関する詳細を表示するには、Web サイトの [ダッシュボード] タブに移動します。**[概要]** セクションの **[管理サービス]** で **[操作ログ]** をクリックします。
	
	![Dashboard - Operation Logs Link][DashboardOperationLogsLink]
	
2. 管理サービス ポータルの **[操作ログ]** ページが表示され、このページで、操作ログの一覧に含まれている復元操作のログを確認できます。
	
	![Management Services Operation Logs page][ManagementServicesOperationLogsList]
	
3. 操作の詳細を表示するには、一覧でいずれかの操作を選択し、コマンド バーの **[詳細]** ボタンをクリックします。
	
	![Details Button][DetailsButton]
	
	この操作を実行すると、**[操作の詳細]** ウィンドウが開き、ログ ファイルの内容がコピー可能な形式で表示されます。
	
	![Operation Details][OperationDetails]
	

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
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


<!--HONumber=42-->
