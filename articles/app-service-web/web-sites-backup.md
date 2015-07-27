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
	ms.date="07/03/2015" 
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

* バックアップと復元の機能では、バックアップする Web アプリと同じサブスクリプションに属する Azure ストレージ アカウントとコンテナーが必要です。ストレージ アカウントをまだ持っていない場合は、[Azure プレビュー ポータル](http://portal.azure.com)の **[バックアップ]** ブレードで **[ストレージ アカウント]** をクリックし、**[バックアップ先]** ブレードで **[ストレージ アカウント]** と **[コンテナー]** を選択することで作成できます。Azure のストレージ アカウントの詳細については、この記事の末尾に示されている[リンク](#moreaboutstorage)を参照してください。

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

<a name="notes"></a>
## メモ

* バックアップと復元の機能にデータベースを含めることができるように、Web アプリの **[設定]** 内の **[Web アプリ設定]** ブレードで、各データベースの接続文字列を適切に設定していることを確認します。
* 複数の Web アプリを同じストレージ アカウントにバックアップできますが、保守しやすくするために、各 Web アプリに別々のストレージ アカウントを作成することを検討します。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページをご覧ください。このページでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="partialbackups"></a>
## Web サイトの一部のみをバックアップする

Web アプリのすべてをバックアップしたくない場合があります。次に例をいくつか示します。

-	古いブログの投稿や画像などの、変更されることがない静的コンテンツを含む Web アプリを[毎週バックアップするように設定](web-sites-backup.md#configure-automated-backups)している。
-	Web アプリのコンテンツが 10 GB を越えている (つまり、一度にバックアップできる最大量を超えている)。
-	ログ ファイルはバックアップしない。

部分バックアップでは、バックアップするファイルを詳細に選択できます。

### バックアップからファイルを除外する

バックアップからファイルとフォルダーを除外するには、`_backup.filter` ファイルを Web アプリの wwwroot フォルダー内に作成し、除外するファイルとフォルダーをその中に指定します。このファイルにアクセスする簡単な方法は、[Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)を使用することです。

今後まったく変更されることがない過去のログ ファイルや静止画像を含む Web アプリがあるとします。古い画像を含む Web アプリの完全バックアップは既に存在します。今後、Web アプリのバックアップを毎日実行しますが、変更されることがないログ ファイルや静止画像ファイルはバックアップしないようにするとします。

![Logs フォルダー][LogsFolder] ![images フォルダー][ImagesFolder]
	
次の手順は、これらのファイルをバックアップから除外する方法を示しています。

1. `http://{yourapp}.scm.azurewebsites.net/DebugConsole` に移動し、バックアップから除外するフォルダーを識別します。この例では、UI に表示されている次のファイルとフォルダーを除外します。

		D:\home\site\wwwroot\Logs
		D:\home\LogFiles
		D:\home\site\wwwroot\Images\2013
		D:\home\site\wwwroot\Images\2014
		D:\home\site\wwwroot\Images\brand.png

	[AZURE.NOTE]最後の行は、フォルダーと同じように個々のファイルを除外できることを示しています。

2. `_backup.filter` という名前のファイルを作成し、上のリストをこのファイルに配置します。ただし `D:\home` は削除します。1 つのディレクトリまたはファイルを 1 行に配置します。したがって、ファイルの内容は次のようになります。

    \\site\\wwwroot\\Logs \\LogFiles \\site\\wwwroot\\Images\\2013 \\site\\wwwroot\\Images\\2014 \\site\\wwwroot\\Images\\brand.png

3. このファイルを、[ftp](web-sites-deploy.md#ftp)やその他の方法を使用して、サイトの `D:\home\site\wwwroot` ディレクトリにアップロードします。ファイルを `http://{yourapp}.scm.azurewebsites.net/DebugConsole` 内に直接作成し、そこにコンテンツを挿入することもできます。

4. 通常と同じ方法 ([手動](#create-a-manual-backup)または[自動](#configure-automated-backups)) でバックアップを実行します。

これで、`_backup.filter` 内に指定されたファイルとフォルダーはバックアップから除外されます。この例では、ログ ファイル、2013 年と 2014 年の画像ファイル、および brand.png がバックアップされなくなります。

>[AZURE.NOTE][定期的なバックアップからの復元](web-sites-restore.md)と同様の手順で、サイトを部分バックアップから復元することもできます。復元プロセスは正常に実行されます。
>
>完全バックアップが復元されると、サイト上のすべてのコンテンツは、バックアップの中身に置き換えられます。サイト上に存在したファイルも、バックアップに存在しなければ削除されます。ただし、部分バックアップが復元されるとき、ブラック リスト化されたディレクトリまたはファイルのいずれかに配置されているコンテンツは放置されます。

<a name="aboutbackups"></a>

## バックアップの保存方法

Web アプリの 1 つ以上のバックアップを作成すると、ストレージ アカウントの **[コンテナー]** ブレードに、Web アプリだけではなくバックアップも表示されます。ストレージ アカウントでは、各バックアップは、バックアップ データを含む .zip ファイルと、.zip ファイルの内容のマニフェストを含む .xml ファイルで構成されます。バックアップにアクセスする場合は、これらのファイルを解凍して参照でき、Web アプリを実際に復元する必要はありません。

Web アプリのデータベースのバックアップは、.zip ファイルのルートに保存されます。SQL データベースの場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。BACPAC のエクスポートに基づく新しい SQL データベースの作成については、「[BACPAC ファイルのインポートによる新しいユーザー データベースの作成](http://technet.microsoft.com/library/hh710052.aspx)」を参照してください。

> [AZURE.WARNING]**websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。

<a name="bestpractices"></a>
##ベスト プラクティス

障害や自然災害の発生に備えて、既存のバックアップと復元戦略を必ず事前に準備します。

バックアップ戦略は、以下に似たものにする必要があります。

-	Web アプリの完全バックアップを最低 1 回実行します。
-	完全バックアップが用意できたら、Web アプリの部分バックアップを実行します。

復元戦略は、以下に似たものにする必要があります。
 
-	Web アプリ用の[ステージング スロット](web-sites-staged-publishing.md)を作成します。
-	ステージング スロットで、Web アプリの完全バックアップを復元します。
-	同様に、ステージング スロットで、復元された完全バックアップに重ねて、最新の部分バックアップを復元します。
-	復元をテストして、ステージング アプリが正常に機能することを確認します。
-	ステージング Web アプリを、実稼働スロットに[スワップ](web-sites-staged-publishing.md#Swap)します。

>[AZURE.NOTE]復元プロセスは常にテストしてください。詳細については、[災害復旧計画のテストに関するページ](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/)を参照してください。たとえば、[Ghost](https://ghost.org/) などの特定のブログ プラットフォームには、バックアップ中の動作に関する明確な注意事項があります。まだ障害や災害が発生していないときに復元プロセスをテストしておくことで、これらの注意事項を確認できます。

<a name="nextsteps"></a>
## 次のステップ
Web アプリをバックアップから復元する方法については、「[Azure App Service での Web アプリの復元](web-sites-restore.md)」をご覧ください。

Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト](/pricing/free-trial/)」を参照してください。


<a name="moreaboutstorage"></a>
### ストレージ アカウントの詳細

[ストレージ アカウントとは](../storage-whatis-account.md)

[方法: ストレージ アカウントを作成する](../storage-create-storage-account/)

[ストレージ アカウントの監視方法](../storage-monitor-storage-account.md)

[Azure ストレージでの課金について](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
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
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!---HONumber=July15_HO3-->