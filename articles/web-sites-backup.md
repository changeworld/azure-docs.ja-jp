<properties 
	pageTitle="Azure Websites のバックアップ" 
	description="Azure Websites のバックアップを作成する方法について説明します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/19/2014" 
	ms.author="cephalin"/>

#Azure Websites のバックアップ

Azure Websites のバックアップと復元の機能で、手動または自動で簡単に Web サイトのバックアップを作成できます。Web サイトを以前の状態に復元したり、元のサイトのいずれかのバックアップに基づいて新しい Web サイトを作成したりできます。 


バックアップから Azure の Web サイトを復元する方法の詳細については、「[Microsoft Azure の Web サイトの復元](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-restore/)」をご覧ください。

##この記事の内容

- [自動の簡単なバックアップ (動画)](#video)
- [バックアップ対象](#whatsbackedup)
- [要件および制限](#requirements)
- [手動でバックアップを作成するには](#manualbackup)
- [自動バックアップを構成するには](#automatedbackups)
- [バックアップの保存](#aboutbackups)
- [注意事項](#notes)
- [次のステップ](#nextsteps)
	- [ストレージ アカウントの詳細](#moreaboutstorage)

<a name="video"></a>
##自動の簡単なバックアップ (動画)

このビデオでは、Eduardo Laureano と Scott Hanselman が Azure Websites のバックアップについて紹介しています(時間:11:43)  

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>
##バックアップ対象 
Azure Websites では、次の情報をバックアップします。

* Web サイトの構成
* Web サイトのファイルの内容
* サイトに接続されているすべての SQL Server または MySQL データベース (バックアップに含めるものを選択できます)

この情報は指定した Azure のストレージ アカウントにバックアップされます。 

> [AZURE.NOTE] 各バックアップは、増分更新ではなく、Web サイトの完全なオフライン コピーです。

<a name="requirements"></a>
##要件および制限

* バックアップと復元の機能には、標準レベルにあるサイトが必要です。標準レベルを使用する Web サイトを規模変更する方法の詳細については、「[Web サイトの規模の設定方法](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-scale/)」をご覧ください。 

* バックアップと復元の機能には、バックアップを作成しようとしている Web サイトと同じサブスクリプションに属する必要がある Azure のストレージ アカウントが必要です。まだストレージ アカウントを所有していない場合は、Azure ポータルの左側ウィンドウの **[ストレージ]** (グリッド アイコン) をクリックし、下部のコマンド バーで **[新規]** をクリックして作成できます。Azure ストレージ アカウントの詳細については、この記事の末尾に示されている[リンク](#moreaboutstorage)を ご覧ください。

<a name="manualbackup"></a>
## 手動でバックアップを作成するには

1. Web サイトの Azure ポータルで、**[バックアップ]** タブを選択します。
	
	![[バックアップ] ページ][ChooseBackupsPage]
	
2. Web サイトをバックアップするストレージ アカウントを選択します。ストレージ アカウントは、バックアップを作成しようとしている Web サイトと同じサブスクリプションに属している必要があります。
	
	![ストレージ アカウントの選択][ChooseStorageAccount]
	
3. **[含まれるデータベース]** オプションで、バックアップを作成する Web サイト (SQL Server または MySQL) に接続されているデータベースを選択します。 
	
	![含めるデータベースの選択][IncludedDatabases]

	> [AZURE.NOTE] 	このリストにデータベースを表示するには、データベースの接続文字列がポータルの [構成] タブの**接続文字列**セクションに存在する必要があります。
	
4. コマンド バーで、**[今すぐバックアップ]** をクリックします。
	
	![[今すぐバックアップ] ボタン][BackUpNow]
	
	バックアップ処理中に、進行状況についてのメッセージが表示されます。
	
	![バックアップの進行状況についてのメッセージ][BackupProgress]
	
手動バックアップはいつでもできます。プレビュー期間中、手動バックアップは 24 時間以内に 2 回まで実行できます (変更されることがあります)。  

<a name="automatedbackups"></a>
## 自動バックアップを構成するには

1. [バックアップ] ページで、**[自動バックアップ]** をオンにします。
	
	![自動化されたバックアップを有効にする][SetAutomatedBackupOn]
	
2. Web サイトをバックアップするストレージ アカウントを選択します。ストレージ アカウントは、バックアップを作成しようとしている Web サイトと同じサブスクリプションに属している必要があります。
	
	![ストレージ アカウントの選択][ChooseStorageAccount]
	
3. **[頻度]** ボックスで、自動化されたバックアップの頻度を指定します(プレビュー期間中、使用可能な時間単位は日数のみです)。
	
	![バックアップ頻度の選択][Frequency]
	
	日数は 1 ～ 90 (1 日 1 回から 90 日に 1 回まで) にする必要があります。
	
4. **[開始日]** オプションを使用して、自動化されたバックアップを開始する日時を指定します。 
	
	![開始日の選択][StartDate]
	
	時間は 30 分単位で選択できます。
	
	![開始時刻の選択][StartTime]
	
	> [AZURE.NOTE] Azure ではバックアップ時間は UTC 形式で保存されますが、ポータルの表示に使用しているコンピューターのシステム時刻に基づいて表示されます。
	
5. **[含まれるデータベース]** セクションで、バックアップを作成する Web サイト (SQL Server または MySQL) に接続されているデータベースを選択します。リストにデータベースを表示するには、データベースの接続文字列がポータルの [構成] タブの **[接続文字列]** セクションに存在する必要があります。
	
	![含めるデータベースの選択][IncludedDatabases]
	
	> [AZURE.NOTE] 1 つ以上のデータベースをバックアップに含めて、7 日未満の頻度を指定すると、頻繁なバックアップはデータベースのコストを増やす可能性があるという警告が表示されます。
	
6. コマンド バーをクリックし、**[保存]** をクリックして、構成の変更を保存します (保存しない場合は、**[破棄]** を選択します)。
	
	![[保存] ボタン][SaveIcon]

<a name="aboutbackups"></a>
## バックアップの保存

1 つ以上のバックアップを行うと、ストレージ アカウントの [コンテナー] タブに表示されます。バックアップは **websitebackups** というコンテナーに含まれます。各バックアップは、バックアップしたデータを含む .zip ファイルと、.zip ファイルの内容のマニフェストを含む .xml ファイルで構成されます。 

.zip バックアップ ファイルおよび .xml バックアップ ファイルの名前は、Web サイトの名前の後にアンダー スコアとバックアップされたときのタイムスタンプを付けて構成されます。タイムスタンプには YYYYMMDD 形式の日付 (スペースなしの数字) に UTC 形式の 24 時間表記を付けたものが含まれます (たとえば、fabrikam_201402152300.zip)。実際には Web サイトの復元を実行せずにバックアップにアクセスする場合は、これらのファイルの内容を解凍して、参照できます。

zip ファイルで保存されている XML ファイルは、*backupdescription* > *databases* > *databasebackupdescription* > *filename* のデータベース ファイル名を示します。

データベース バックアップ ファイル自体は、.zip ファイルのルートに保存されています。SQL データベースの場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。BACPAC エクスポートに基づいて新しい SQL データベースを作成するには、「[BACPAC ファイルのインポートによる新しいユーザー データベースの作成](http://technet.microsoft.com/ja-jp/library/hh710052.aspx)」の手順に従います。

Azure 管理ポータルを使用した Azure Website (データベースを含む) の復元の詳細については、「[Microsoft Azure の Web サイトの復元]( http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-restore/)」をご覧ください。

> [AZURE.NOTE] **websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。

<a name="notes"></a>
## メモ

* バックアップと復元の機能をデータベースに含めることができるように、各データベースの接続文字列を Web サイトの [構成] タブで適切に設定していることを確認します。
* プレビュー期間中、ストレージ アカウントに保存されているバックアップ コンテンツを管理します。バックアップをストレージ アカウントから削除して別の場所にコピーを作成していない場合は、後でバックアップを復元することはできません。 
* 複数の Web サイトを同じストレージ アカウントにバックアップできますが、保守しやすくするために、各 Web サイトに別々のストレージ アカウントを作成することを検討します。
* プレビュー期間中、バックアップ操作と復元操作は、Azure 管理ポータルを通じてのみ使用可能です。

<a name="nextsteps"></a>
## 次のステップ
バックアップから Azure の Web サイトを復元する方法の詳細については、「[Microsoft Azure の Web サイトの復元](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-restore/)」をご覧ください。

Azure の利用を開始するには、「[1 か月間の無料評価版](http://azure.microsoft.com/ja-jp/pricing/free-trial/)」をご覧ください。


<a name="moreaboutstorage"></a>
### ストレージ アカウントの詳細

[ストレージ アカウントとは](http://www.windowsazure.com/ja-jp/documentation/articles/storage-whatis-account/)

[方法:ストレージ アカウントを作成する](http://www.windowsazure.com/ja-jp/documentation/articles/storage-create-storage-account/)

[ストレージ アカウントの監視方法](http://www.windowsazure.com/ja-jp/documentation/articles/storage-monitor-storage-account/)

[Winidows Azure ストレージでの課金について](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

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


<!--HONumber=42-->
