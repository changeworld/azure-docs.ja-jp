<properties 
	pageTitle="Azure App Service での Web アプリのバックアップ" 
	description="Azure App Service で Web アプリのバックアップを作成する方法を説明します。." 
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

# Azure App Service での Web アプリのバックアップ


[Azure App Service の Web アプリ](http://go.microsoft.com/fwlink/?LinkId=529714)のバックアップおよび復元機能によって、簡単に Web アプリのバックアップを手動または自動で作成できます。Web アプリを以前の状態に復元するか、または元のアプリのいずれかのバックアップに基づいて、新しい Web アプリを作成できます。 

Azure Web アプリをバックアップから復元する方法の詳細については、「[Web アプリの復元]」(web-sites-restore.md) を参照してください。

<a name="whatsbackedup"></a>
## バックアップ対象 
Web アプリでは、次の情報をバックアップできます。

* Web アプリの構成
* Web アプリ ファイルの内容
* アプリに接続されているすべての SQL Server または MySQL データベース (バックアップに含めるデータベースを選択できます)

この情報は、指定した Azure のストレージ アカウントとコンテナーにバックアップされます。 

> [AZURE.NOTE] 各バックアップは増分更新ではなく、アプリの完全なオフライン コピーです。

<a name="requirements"></a>
## 要件および制限

* バックアップと復元の機能を使用するには、サイトを標準モードにする必要があります。標準モードを使用するための Web アプリのスケーリングの詳細については、「[Scale a web app in Azure App Service (Azure App Serviceでの Web アプリのスケール)]」(web-sites-scale.md) を参照してください。プレミアム モードでは、標準モードよりも多くの毎日のバックアップを実行できることに注意してください。

* バックアップと復元の機能では、バックアップする Web アプリと同じサブスクリプションに属する Azure ストレージ アカウントとコンテナーが必要です。ストレージ アカウントをまだ持っていない場合は、**Azure ポータル**の **[バックアップ]** ブレードで [[ストレージ アカウント]]( http://go.microsoft.com/fwlink/?LinkId=529715)をクリックし、**[バックアップ先]** ブレードで **[ストレージ アカウント]** と **[コンテナー]** を選択して、作成できます。Azure ストレージ アカウントの詳細については、この記事の末尾にある[リンク](#moreaboutstorage)を参照してください。

<a name="manualbackup"></a>
## 手動バックアップの作成

1. Azure ポータルでは、[Web アプリ] ブレードから Web アプリを選択します。これにより、新しいブレードで、Web アプリの詳細が表示されます。
2. **[設定]** オプションを選択します。**[設定]** ブレードが表示され、Web アプリを変更できます。
	
	![Backups page][ChooseBackupsPage]

3. **[設定]** ブレードで **[バックアップ]** オプションを選択します。**[バックアップ]** ブレードが表示されます。
	
4. **[バックアップ]** ブレードから、**[ストレージ アカウント]** と **[コンテナー]** を選択して、バックアップ先を選択します。ストレージ アカウントは、バックアップする Web アプリと同じサブスクリプションに属する必要があります。
	
	![Choose storage account][ChooseStorageAccount]
	
5. **[バックアップ]** ブレードの **[含まれるデータベース]** オプションで、バックアップする Web アプリに接続されているデータベース (SQL Server または MySQL) を選択します。 

	> [AZURE.NOTE] 	この一覧に表示されるデータベースに対して、ポータルの **[Web アプリ設定]** ブレードの **[接続文字列]** セクションに、その接続文字列が存在する必要があります。
	
6. **[バックアップ]** ブレードで、**[バックアップ先]** を選択します。既存のストレージ アカウントとコンテナーを選択する必要があります。
7. コマンド バーで、**[今すぐバックアップ]** をクリックします。
	
	![BackUpNow button][BackUpNow]
	
	バックアップ処理中に、進行状況についてのメッセージが表示されます。
	

手動バックアップはいつでもできます。  

<a name="automatedbackups"></a>
## 自動バックアップの構成

1. **[バックアップ]** ブレードで、**[スケジュールされたバックアップ]** をオンにします。
	
	![Enable automated backups][SetAutomatedBackupOn]
	
2. Web アプリをバックアップするストレージ アカウントを選択します。ストレージ アカウントは、バックアップする Web アプリと同じサブスクリプションに属する必要があります。
	
	![Choose storage account][ChooseStorageAccount]
	
3. **[頻度]** ボックスで、自動化されたバックアップの頻度を指定します 
	日数は 1 ～ 90 (1 日 1 回から 90 日に 1 回まで) にする必要があります。
	
4. **[開始]** オプションを使用して、自動化されたバックアップを開始する日時を指定します。 
	
	> [AZURE.NOTE] Azure はバックアップの時刻を UTC 形式で格納しますが、この時刻は、ポータルを表示するために使用しているコンピューターのシステム時刻に従って表示されます。
	
5. **[含まれるデータベース]** セクションで、バックアップを作成する Web アプリ (SQL Server または MySQL) に接続されているデータベースを選択します。この一覧に表示されるデータベースに対して、ポータルの [**Web アプリ設定**] ブレードの [**接続文字列**] セクションに、その接続文字列が存在する必要があります。
	
	> [AZURE.NOTE] 1 つ以上のデータベースをバックアップに含めて、7 日未満の頻度を指定すると、頻繁なバックアップはデータベースのコストを増やす可能性があるという警告が表示されます。
	
6. さらに、**[保有期間 (日数)] ** の値に、バックアップを保持する日数を指定します。
7. コマンド バーをクリックし、**[保存]** をクリックして、構成の変更を保存します (保存しない場合は、**[破棄]** を選択します)。
	
	![Save button][SaveIcon]

<a name="aboutbackups"></a>
## バックアップの保存方法

1 回以上のバックアップを行った後、バックアップは、**ストレージ アカウント**の **[コンテナー]** ブレードと、Web アプリに表示されます。**ストレージ アカウント**からの各バックアップは、バックアップしたデータを含む .zip ファイルと、.zip ファイルの内容のマニフェストを含む .xml ファイルで構成されます。 

.zip バックアップ ファイルおよび .xml バックアップ ファイルの名前は、Web アプリの名前の後にアンダー スコアとバックアップされたときのタイム スタンプを付けて構成されます。タイム スタンプには YYYYMMDD 形式の日付 (スペースなしの数字) に UTC 形式の 24 時間表記を付けたものが含まれます (たとえば、fabrikam_201402152300.zip)。実際には Web アプリの復元を実行せずにバックアップにアクセスする場合は、これらのファイルの内容を解凍して、参照することができます。

Zip ファイルに格納されている XML ファイルは、 *backupdescription* > *databases* > *databasebackupdescription* > *filename* にデータベースのファイル名を示します。

データベース バックアップ ファイル自体は、.zip ファイルのルートに保存されています。SQL データベースの場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。BACPAC エクスポートに基づいて新しい SQL データベースを作成するには、「[BACPAC ファイルのインポートによる新しいユーザー データベースの作成](http://technet.microsoft.com/library/hh710052.aspx)」の手順に従います。

Azure ポータルを使用して Web アプリ (データベースを含む) を復元する方法については、「[Restore a web app in Azure App Service (Azure App Service での Web アプリの復元)]」(web-sites-restore.md) を参照してください。

> [AZURE.NOTE] **websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。

<a name="notes"></a>
## メモ

* バックアップと復元の機能にデータベースを含めることができるように、Web アプリの**設定**内の **[Web アプリ設定]** ブレードで、各データベースの接続文字列を適切に設定したことを確認します。
* 複数の Web アプリを同じストレージ アカウントにバックアップできますが、保守しやすくするために、各 Web アプリに別々のストレージ アカウントを作成することを検討します。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure アプリ サービスを開始する場合は、「[Try App Service (アプリ サービスの試用)](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。アプリ サービスで、有効期間が短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="nextsteps"></a>
## 次のステップ
Azure Web アプリをバックアップから復元する方法の詳細については、「[Restore a web app in Azure App Service （Azure App Service の Web アプリの復元）](web-sites-restore.md)」を参照してください。

Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト](/pricing/free-trial/)」を参照してください。


<a name="moreaboutstorage"></a>
### ストレージ アカウントの詳細

[ストレージ アカウントとは](storage-whatis-account.md

[方法:ストレージ アカウントを作成する](../storage-create-storage-account/)

[ストレージ アカウントの監視方法](storage-monitor-storage-account.md)

[Azure ストレージでの課金について](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## 変更内容
* Web サイトからアプリ サービスへの変更に関するガイドについては、以下を参照してください。[Azure App Service and Its Impact on Existing Azure Services (Azure アプリ サービスと既存の Azure サービスへの影響)](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更に関するガイドについては、以下を参照してください。[Reference for navigating the preview portal （プレビュー ポータルをナビゲートするためのリファレンス）](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png

<!--HONumber=49-->