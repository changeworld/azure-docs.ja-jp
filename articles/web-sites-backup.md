<properties 
	pageTitle="Azure App Service での Web アプリのバックアップ" 
	description="Azure App Service で Web アプリのバックアップを作成する方法を説明します。" 
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


[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) のバックアップと復元の機能により、Web アプリのバックアップを手動または自動で簡単に作成できます。Web アプリを以前の状態に復元するか、または元のアプリのいずれかのバックアップに基づいて、新しい Web アプリを作成できます。

Azure Web アプリをバックアップから復元する方法については、[Web アプリの復元](web-sites-restore.md)に関するページをご覧ください。

<a name="whatsbackedup"></a>
## バックアップ対象 
Web アプリでは、次の情報をバックアップできます。

* Web アプリの構成
* Web アプリ ファイルの内容
* アプリに接続されているすべての SQL Server または MySQL データベース (バックアップに含めるデータベースを選択できます)

この情報は、指定した Azure のストレージ アカウントとコンテナーにバックアップされます。

> [AZURE.NOTE]各バックアップは増分更新ではなく、アプリの完全なオフライン コピーです。

<a name="requirements"></a>
## 要件および制限

* バックアップと復元の機能を使用するには、サイトを標準モードにする必要があります。標準モードを使用するための Web アプリの拡張の詳細については、「[Azure App Service での Web アプリの拡張](web-sites-scale.md)」をご覧ください。プレミアム モードでは、標準モードよりも多くの毎日のバックアップを実行できることに注意してください。

* バックアップと復元の機能では、バックアップする Web アプリと同じサブスクリプションに属する Azure ストレージ アカウントとコンテナーが必要です。ストレージ アカウントをまだ持っていない場合は、[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)の **[バックアップ]** ブレードで **[ストレージ アカウント]** をクリックし、**[バックアップ先]** ブレードで **[ストレージ アカウント]** と **[コンテナー]** を選択することで作成できます。Azure のストレージ アカウントの詳細については、この記事の末尾に示されている[リンク](#moreaboutstorage)を参照してください。

* バックアップと復元の機能では、Web サイトとデータベースの最大 10 GB のコンテンツをサポートします。ペイロードがこの上限を超えるためにバックアップ機能を続行できない場合は、処理ログにエラーが示されます。

<a name="manualbackup"></a>
## 手動バックアップの作成

1. Azure ポータルでは、[Web アプリ] ブレードから Web アプリを選択します。これにより、新しいブレードで、Web アプリの詳細が表示されます。
2. **[設定]** オプションを選択します。**[設定]** ブレードが表示され、Web アプリを変更できるようになります。
	
	![バックアップ ページ][ChooseBackupsPage]

3. **[設定]** ブレードで **[バックアップ]** オプションを選択します。**[バックアップ]** ブレードが表示されます。
	
4. **[バックアップ]** ブレードから、**[ストレージ アカウント]** と **[コンテナー]** を選択して、バックアップ先を選択します。ストレージ アカウントは、バックアップする Web アプリと同じサブスクリプションに属する必要があります。
	
	![ストレージ アカウントの選択][ChooseStorageAccount]
	
5. **[バックアップ]** ブレードの **[含まれるデータベース]** オプションで、バックアップする Web アプリに接続されているデータベース (SQL Server または MySQL) を選択します。

	> [AZURE.NOTE]この一覧に表示されるデータベースに対して、ポータルの **[Web アプリ設定]** ブレードの **[接続文字列]** セクションに、その接続文字列が存在する必要があります。
	
6. **[バックアップ]** ブレードで、**[バックアップ先]** を選択します。既存のストレージ アカウントとコンテナーを選択する必要があります。
7. コマンド バーで、**[今すぐバックアップ]** をクリックします。
	
	![BackUpNow ボタン][BackUpNow]
	
	バックアップ処理中に、進行状況についてのメッセージが表示されます。
	

手動バックアップはいつでもできます。

<a name="automatedbackups"></a>
## 自動バックアップの構成

1. **[バックアップ]** ブレードで、**[スケジュールされたバックアップ]** をオンに設定します。
	
	![自動化されたバックアップを有効にする][SetAutomatedBackupOn]
	
2. Web アプリをバックアップするストレージ アカウントを選択します。ストレージ アカウントは、バックアップする Web アプリと同じサブスクリプションに属する必要があります。
	
	![ストレージ アカウントの選択][ChooseStorageAccount]
	
3. **[頻度]** ボックスで、自動化されたバックアップの頻度を指定します日数は 1 ～ 90 (1 日 1 回から 90 日に 1 回まで) にする必要があります。
	
4. **[開始]** オプションを使用して、自動化されたバックアップを開始する日時を指定します。
	
	> [AZURE.NOTE]Azure はバックアップの時刻を UTC 形式で格納しますが、この時刻は、ポータルを表示するために使用しているコンピューターのシステム時刻に従って表示されます。
	
5. **[含まれるデータベース]** セクションで、バックアップする Web アプリに接続されているデータベース (SQL Server または MySQL) を選択します。この一覧に表示されるデータベースに対して、ポータルの **[Web アプリ設定]** ブレードの **[接続文字列]** セクションに、その接続文字列が存在する必要があります。
	
	> [AZURE.NOTE]1 つ以上のデータベースをバックアップに含めて、7 日未満の頻度を指定すると、頻繁なバックアップはデータベースのコストを増やす可能性があるという警告が表示されます。
	
6. さらに、**[保有期間 (日数)]** の値を、バックアップを保持する日数に設定します。
7. コマンド バーをクリックし、**[保存]** をクリックして、構成の変更を保存します (保存しない場合は、**[破棄]** を選択します)。
	
	![保存ボタン][SaveIcon]

<a name="aboutbackups"></a>
## バックアップの保存方法

1 つ以上のバックアップを作成すると、**ストレージ アカウント**の **[コンテナー]** ブレードと Web アプリに表示されます。**ストレージ アカウント**からの各バックアップは、バックアップしたデータを含む .zip ファイルと、.zip ファイルの内容のマニフェストを含む .xml ファイルで構成されます。

.zip バックアップ ファイルおよび .xml バックアップ ファイルの名前は、Web アプリの名前の後にアンダー スコアとバックアップされたときのタイム スタンプを付けて構成されます。タイムスタンプには YYYYMMDD 形式の日付 (スペースなしの数字) に UTC 形式の 24 時間表記を付けたものが含まれます (たとえば、fabrikam_201402152300.zip)。実際には Web アプリの復元を実行せずにバックアップにアクセスする場合は、これらのファイルの内容を解凍して、参照することができます。

zip ファイルで保存されている XML ファイルは、*backupdescription* > *databases* > *databasebackupdescription* > *filename* のデータベース ファイル名を示します。

データベース バックアップ ファイル自体は、.zip ファイルのルートに保存されています。SQL データベースの場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。BACPAC エクスポートに基づいて新しい SQL データベースを作成するには、「[BACPAC ファイルのインポートによる新しいユーザー データベースの作成](http://technet.microsoft.com/library/hh710052.aspx)」の手順に従います。

Azure ポータルを使用して Web アプリ (データベースを含む) を復元する方法については、「[Azure App Service での Web アプリの復元](web-sites-restore.md)」をご覧ください。

> [AZURE.NOTE]**websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。

<a name="notes"></a>
## メモ

* バックアップと復元の機能にデータベースを含めることができるように、Web アプリの **[設定]** 内の **[Web アプリ設定]** ブレードで、各データベースの接続文字列を適切に設定していることを確認します。
* 複数の Web アプリを同じストレージ アカウントにバックアップできますが、保守しやすくするために、各 Web アプリに別々のストレージ アカウントを作成することを検討します。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページをご覧ください。このページでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="nextsteps"></a>
## 次のステップ
Azure Web アプリをバックアップから復元する方法については、「[Azure App Service での Web アプリの復元](web-sites-restore.md)」をご覧ください。

Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト](/pricing/free-trial/)」を参照してください。


<a name="moreaboutstorage"></a>
### ストレージ アカウントの詳細

[ストレージ アカウントとは](storage-whatis-account.md)

[方法: ストレージ アカウントを作成する](../storage-create-storage-account/)

[ストレージ アカウントの監視方法](storage-monitor-storage-account.md)

[Azure ストレージでの課金について](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## 変更内容
* Websites から App Service への変更ガイドについては、[Azure App Service と既存の Azure Services への影響](http://go.microsoft.com/fwlink/?LinkId=529714)に関するページをご覧ください。
* 古いポータルから新しいポータルへの変更ガイドについては、[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)をご覧ください。

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

<!--HONumber=54-->