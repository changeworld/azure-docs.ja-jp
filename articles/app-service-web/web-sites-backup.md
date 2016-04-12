<properties 
	pageTitle="Azure App Service での Web アプリのバックアップ" 
	description="Azure App Service で Web アプリのバックアップを作成する方法を説明します。" 
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
	ms.date="01/26/2016" 
	ms.author="cephalin"/>

# Azure App Service での Web アプリのバックアップ


[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) のバックアップと復元の機能により、Web アプリのバックアップを手動または自動で簡単に作成できます。Web アプリを以前の状態に復元するか、または元のアプリのいずれかのバックアップに基づいて、新しい Web アプリを作成できます。

Azure Web アプリをバックアップから復元する方法については、[Web アプリの復元](web-sites-restore.md)に関するページを参照してください。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="whatsbackedup"></a>
## バックアップ対象 
Web Apps では、次の情報をバックアップできます。

* Web アプリの構成
* Web アプリ ファイルの内容
* アプリに接続されているすべての Azure SQL データベースまたは MySQL データベース (バックアップに含めるデータベースを選択できます)

この情報は、指定した Azure のストレージ アカウントとコンテナーにバックアップされます。

> [AZURE.NOTE] 各バックアップは増分更新ではなく、アプリの完全なオフライン コピーです。

<a name="requirements"></a>
## 要件および制限

* バックアップと復元の機能には、Standard 以上のレベルにある App Service プランが必要です。上位レベルを使用するための App Service プランの拡張の詳細については、「[Azure App Service での Web アプリの拡張](web-sites-scale.md)」を参照してください。Premium レベルでは、Standard レベルよりも多くの回数の日次バックアップが可能です。

* バックアップと復元の機能では、バックアップする Web アプリと同じサブスクリプションに属する Azure ストレージ アカウントとコンテナーが必要です。ストレージ アカウントをまだ持っていない場合は、[Azure ポータル](https://portal.azure.com/)の **[バックアップ]** ブレードで **[ストレージ アカウント]** をクリックし、**[バックアップ先]** ブレードで **[ストレージ アカウント]** と **[コンテナー]** を選択することで作成できます。Azure のストレージ アカウントの詳細については、この記事の末尾に示されている[リンク](#moreaboutstorage)を参照してください。

* バックアップと復元の機能では、Web サイトとデータベースの最大 10 GB のコンテンツをサポートします。ペイロードがこの上限を超えるためにバックアップ機能を続行できない場合は、エラーが示されます。

<a name="manualbackup"></a>
## 手動バックアップの作成

1. Azure ポータルでは、[Web Apps] ブレードから Web アプリを選択します。これにより、新しいブレードで、Web アプリの詳細が表示されます。
2. アプリのブレードで **[設定]**、**[バックアップ]** の順に選択します。**[バックアップ]** ブレードが表示されます。
	
	![バックアップ ページ][ChooseBackupsPage]

3. **[バックアップ]** ブレードで、**[ストレージ: 未構成]** をクリックして、ストレージ アカウントを構成します。

	![ストレージ アカウントの選択][ChooseStorageAccount]
	
4. **[ストレージ アカウント]** と **[コンテナー]** を選択して、バックアップ先を選択します。ストレージ アカウントは、バックアップする Web アプリと同じサブスクリプションに属する必要があります。必要に応じて、各ブレードで新しいストレージ アカウントまたは新しいコンテナーを作成できます。完了したら、**[選択]** をクリックします。
	
	![ストレージ アカウントの選択](./media/web-sites-backup/02ChooseStorageAccount1.png)
	
5. 開いたままになっている **[バックアップ設定の構成]** ブレードで、**[データベースの設定]** をクリックし、バックアップに含めるデータベース (SQL データベースまたは MySQL) を選択してから、**[OK]** をクリックします。

	![ストレージ アカウントの選択](./media/web-sites-backup/03ConfigureDatabase.png)

	> [AZURE.NOTE] 	この一覧に表示されるデータベースに対して、ポータルの **[Web アプリ設定]** ブレードの **[接続文字列]** セクションに、その接続文字列が存在する必要があります。

6. **[バックアップ設定の構成]** ブレードで、**[保存]** をクリックします。
6. **[バックアップ]** ブレードで、**[バックアップ先]** を選択します。既存のストレージ アカウントとコンテナーを選択する必要があります。
7. **[バックアップ]** ブレードのコマンド バーで、**[今すぐバックアップ]** をクリックします。
	
	![BackUpNow ボタン][BackUpNow]
	
	バックアップ処理中に、進行状況についてのメッセージが表示されます。
	

手動バックアップはいつでもできます。

<a name="automatedbackups"></a>
## 自動バックアップの構成

1. **[バックアップ]** ブレードで、**[スケジュール: 未構成]** をクリックします。 

	![ストレージ アカウントの選択](./media/web-sites-backup/05ScheduleBackup.png)
	
1. **[バックアップ スケジュールの設定]** ブレードで、**[スケジュールされたバックアップ]** を**オン**に設定し、必要に応じてバックアップ スケジュールを構成し、**[OK]** をクリックします。
	
	![自動化されたバックアップを有効にする][SetAutomatedBackupOn]
	
4. 開いたままになっている **[バックアップ設定の構成]** ブレードで、**[ストレージ設定]** をクリックし、**[ストレージ アカウント]** と **[コンテナー]** を選択してバックアップ先を選択します。ストレージ アカウントは、バックアップする Web アプリと同じサブスクリプションに属する必要があります。必要に応じて、各ブレードで新しいストレージ アカウントまたは新しいコンテナーを作成できます。完了したら、**[選択]** をクリックします。
	
	![ストレージ アカウントの選択](./media/web-sites-backup/02ChooseStorageAccount1.png)
	
5. **[バックアップ設定の構成]** ブレードで、**[データベースの設定]** をクリックし、バックアップに含めるデータベース (SQL データベースまたは MySQL) を選択してから、**[OK]** をクリックします。

	![ストレージ アカウントの選択](./media/web-sites-backup/03ConfigureDatabase.png)

	> [AZURE.NOTE] 	この一覧に表示されるデータベースに対して、ポータルの **[Web アプリ設定]** ブレードの **[接続文字列]** セクションに、その接続文字列が存在する必要があります。

6. **[バックアップ設定の構成]** ブレードで、**[保存]** をクリックします。

<a name="notes"></a>
## メモ

* バックアップと復元の機能にデータベースを含めることができるように、Web アプリの **[設定]** 内の **[Web アプリ設定]** ブレードで、各データベースの接続文字列を適切に設定していることを確認します。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="partialbackups"></a>
## Web アプリの一部のみをバックアップする

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

	[AZURE.NOTE] 最後の行は、フォルダーと同じように個々のファイルを除外できることを示しています。

2. `_backup.filter` という名前のファイルを作成し、上のリストをこのファイルに配置します。ただし `D:\home` は削除します。1 つのディレクトリまたはファイルを 1 行に配置します。したがって、ファイルの内容は次のようになります。

    \\site\\wwwroot\\Logs \\LogFiles \\site\\wwwroot\\Images\\2013 \\site\\wwwroot\\Images\\2014 \\site\\wwwroot\\Images\\brand.png

3. このファイルを、[ftp](web-sites-deploy.md#ftp)やその他の方法を使用して、サイトの `D:\home\site\wwwroot` ディレクトリにアップロードします。ファイルを `http://{yourapp}.scm.azurewebsites.net/DebugConsole` 内に直接作成し、そこにコンテンツを挿入することもできます。

4. 通常と同じ方法 ([手動](#create-a-manual-backup)または[自動](#configure-automated-backups)) でバックアップを実行します。

これで、`_backup.filter` 内に指定されたファイルとフォルダーはバックアップから除外されます。この例では、ログ ファイル、2013 年と 2014 年の画像ファイル、および brand.png がバックアップされなくなります。

>[AZURE.NOTE] [定期的なバックアップからの復元](web-sites-restore.md)と同様の手順で、サイトを部分バックアップから復元することもできます。復元プロセスは正常に実行されます。
>
>完全バックアップが復元されると、サイト上のすべてのコンテンツは、バックアップの中身に置き換えられます。サイト上に存在したファイルも、バックアップに存在しなければ削除されます。ただし、部分バックアップが復元されるとき、ブラック リスト化されたディレクトリまたはファイルのいずれかに配置されているコンテンツは放置されます。

<a name="aboutbackups"></a>

## バックアップの保存方法

Web アプリの 1 つ以上のバックアップを作成すると、ストレージ アカウントの **[コンテナー]** ブレードに、Web アプリだけではなくバックアップも表示されます。ストレージ アカウントでは、各バックアップは、バックアップ データを含む .zip ファイルと、.zip ファイルの内容のマニフェストを含む .xml ファイルで構成されます。バックアップにアクセスする場合は、これらのファイルを解凍して参照でき、Web アプリを実際に復元する必要はありません。

Web アプリのデータベースのバックアップは、.zip ファイルのルートに保存されます。SQL データベースの場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。BACPAC のエクスポートに基づく新しい SQL データベースの作成については、「[BACPAC ファイルのインポートによる新しいユーザー データベースの作成](http://technet.microsoft.com/library/hh710052.aspx)」を参照してください。

> [AZURE.WARNING] **websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。

<a name="nextsteps"></a>
## 次のステップ
Web アプリをバックアップから復元する方法については、「[Azure App Service での Web アプリの復元](web-sites-restore.md)」を参照してください。REST API を使用して、App Service アプリのバックアップと復元を実行することもできます (「[REST を使用して App Service アプリのバックアップと復元を実行する](websites-csm-backup.md)」を参照してください)。

Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト](/pricing/free-trial/)」を参照してください。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。

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
 

<!---HONumber=AcomDC_0330_2016------>