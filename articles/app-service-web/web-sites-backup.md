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

<a name="notes"></a>
## メモ

* バックアップと復元の機能にデータベースを含めることができるように、Web アプリの **[設定]** 内の **[Web アプリ設定]** ブレードで、各データベースの接続文字列を適切に設定していることを確認します。
* 複数の Web アプリを同じストレージ アカウントにバックアップできますが、保守しやすくするために、各 Web アプリに別々のストレージ アカウントを作成することを検討します。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページをご覧ください。このページでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="partialbackups"></a>
## サイトの一部のみのバックアップ

サイトを定期的にバックアップする場合や、サイトのコンテンツの容量が 10 GB (一度にバックアップを作成する場合の最大容量) を超えている場合など、サイト上のすべてのデータをバックアップしない場合があります。

これには、ログ ファイルをバックアップしない場合などが考えられます。また、[毎週のバックアップをセットアップ](https://azure.microsoft.com/ja-jp/documentation/articles/web-sites-backup/#configure-automated-backups)する場合にも、古いブログ記事や画像などその後変更されることのない静的コンテンツなどでストレージ アカウントの容量を消費することは望ましくありません。

部分バックアップでは、バックアップするファイルを詳細に選択できます。

###バックアップしないファイルを指定する
バックアップ対象から除外するファイルとフォルダーのリストを作成することができます。

リストを _backup.filter という名前のテキスト ファイルとしてサイトの wwwroot フォルダーに保存します。このファイルにアクセスする場合は、[Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console) `http://{yoursite}.scm.azurewebsites.net/DebugConsole` で  にアクセスすると簡単です。

次の手順では、Kudu コンソールを使用して _backup.filter ファイルを作成していますが、このファイルをこの場所に配置する場合にはユーザーのお好みのデプロイ方法を使用できます。

###操作方法
今後まったく変更されることのない過去のログ ファイルや静的な画像を含むサイトがあるとします。

また、この古い画像が含まれているサイトは完全バックアップを作成済みです。今後、サイトのバックアップを毎日実行しますが、今後まったく変更されることのないログ ファイルや静的な画像ファイルはバックアップしないようにするとします。

![Logs フォルダー][LogsFolder] ![images フォルダー][ImagesFolder]
	
これらのファイルをバックアップ対象から除外するには、次の手順に従います。

####バックアップ対象から除外するファイルとフォルダーの指定
この作業は簡単です。ログ ファイルはすべてバックアップしないことが決まっていたので、`D:\home\site\wwwroot\Logs` は対象から除外します。

他に、Azure Web Apps のログ ファイルがすべて格納されているフォルダー (`D:\home\LogFiles`) もあります。こちらも除外します。

さらに、過去数年分の画像も、何度も繰り返しバックアップしないようにします。このため、`D:\home\site\wwwroot\Images\2013` と `D:\home\site\wwwroot\Images\2014` もリストに追加します。

最後に、画像フォルダーの brand.png ファイルもバックアップ対象から除外します。この場合、個々のファイルをブラック リストに追加する方法を使用します。このファイルは `D:\home\site\wwwroot\Images\brand.png` に格納されています。

以上から、次のフォルダをバックアップ対象から除外します。

* D:\home\site\wwwroot\Logs
* D:\home\LogFiles
* D:\home\site\wwwroot\Images\2013
* D:\home\site\wwwroot\Images\2014
* D:\home\site\wwwroot\Images\brand.png

#### 除外リストの作成
バックアップ対象から除外するファイルとフォルダーのブラック リストを、_backup.filter という名前の特殊なファイルに保存します。ファイルを作成し、`D:\home\site\wwwroot_backup.filter` に格納します。

_backup.filter ファイルには、バックアップ対象から除外するファイルおよびフォルダーがすべて含まれているリストが保存されています。バックアップ対象から除外するフォルダーまたはファイルの完全パスから D:\home 以下の部分を、1 行に 1 つずつ追加します。

自分のサイト場合、`D:\home\site\wwwroot\Logs` は `\site\wwwroot\Logs`、`D:\home\LogFiles` は `\LogFiles` というようになります。このため、_backup.filter の内容は次のようになります。

    \site\wwwroot\Logs
    \LogFiles
    \site\wwwroot\Images\2013
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\brand.png

このとき、各行の先頭は必ず `` という文字から始まります。このことは重要ですので、ご注意ください。

###バックアップの実行
これで、バックアップを通常の手順と同様に実行できるようになりました。[手動](https://azure.microsoft.com/ja-jp/documentation/articles/web-sites-backup/#create-a-manual-backup) または[自動](https://azure.microsoft.com/ja-jp/documentation/articles/web-sites-backup/#configure-automated-backups) のどちらでも実行できます。

_backup.filter のリストに含まれているすべてのファイルとフォルダーがバックアップ対象から除外されます。つまり、ログ ファイルと 2013 年と 2014 年の画像ファイルがバックアップされないようになります。

###バックアップされたサイトの復元
[定期的なバックアップからの復元](https://azure.microsoft.com/ja-jp/documentation/articles/web-sites-restore/)と同様の手順で、サイトを部分バックアップから復元することもできます。この方法でも正しく処理が実行されます。

####技術的な詳細
部分バックアップではない完全バックアップでは、サイトのすべてのコンテンツがバックアップの内容と置き換えられます。サイト上に存在したファイルも、バックアップに存在しなければ削除されます。

しかし、そのようなファイルも、ブラック リストに含まれているフォルダー (自分のサイトの場合は `D:\home\site\wwwroot\images\2014` など) のいずれかに保存されているコンテンツから部分バックアップで復元する場合はそのまま残されます。個々のファイルがブラック リストに含まれている場合は、それらのファイルも復元中そのまま残されます。

<a name="aboutbackups"></a>
## バックアップの保存方法

1 つ以上のバックアップを作成すると、**ストレージ アカウント**の **[コンテナー]** ブレードと Web アプリに表示されます。**ストレージ アカウント**からの各バックアップは、バックアップしたデータを含む .zip ファイルと、.zip ファイルの内容のマニフェストを含む .xml ファイルで構成されます。

.zip バックアップ ファイルおよび .xml バックアップ ファイルの名前は、Web アプリの名前の後にアンダー スコアとバックアップされたときのタイム スタンプを付けて構成されます。タイムスタンプには YYYYMMDD 形式の日付 (スペースなしの数字) に UTC 形式の 24 時間表記を付けたものが含まれます (たとえば、fabrikam_201402152300.zip)。実際には Web アプリの復元を実行せずにバックアップにアクセスする場合は、これらのファイルの内容を解凍して、参照することができます。

zip ファイルで保存されている XML ファイルは、*backupdescription* > *databases* > *databasebackupdescription* > *filename* のデータベース ファイル名を示します。

データベース バックアップ ファイル自体は、.zip ファイルのルートに保存されています。SQL データベースの場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。BACPAC エクスポートに基づいて新しい SQL データベースを作成するには、「[BACPAC ファイルのインポートによる新しいユーザー データベースの作成](http://technet.microsoft.com/library/hh710052.aspx)」の手順に従います。

Azure ポータルを使用して Web アプリ (データベースを含む) を復元する方法については、「[Azure App Service での Web アプリの復元](web-sites-restore.md)」をご覧ください。

> [AZURE.NOTE]**websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。

<a name="bestpractices"></a>
##ベスト プラクティス
災害発生によりサイトを復元する場合は、どのようにするのですか。 事前に準備が完了していることを確認します。

部分バックアップがあること、および最悪の場合にはすべての内容をバックアップできるように、少なくとも 1 回はサイトの完全バックアップを作成していることが必要です。バックアップを復元する場合は、まずサイトの完全バックアップを復元し、それから最新の部分バックアップを上書きして復元します。

これは、復元されたサイトのテストで[デプロイ スロット](https://azure.microsoft.com/ja-jp/documentation/articles/web-sites-staged-publishing/)を使用するために必要です。これにより、実稼働サイトにまったく影響を与えないように復元プロセスをテストすることができます復元プロセスをテストすることは[非常に重要](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/)です。私は、ブログ サイトを復元しようとしてそのコンテンツの半分を失ったことがあります。皆様も何らかのトラブルに遭遇して同じような結果になる可能性があります。

###悪い事例

私のブログは、[Ghost](https://ghost.org/) というブログ作成プラットフォームで運用しています。責任感の強い開発者のように、私も自分のサイトのバックアップを作成し、万事が順調に進んでいました。ある日、新バージョンの Ghost の提供が開始され、私のブログをアップグレードできるようになったことを通知するメッセージを受け取りました。以上です。

そこで、最新のブログ記事を含むバックアップをもう 1 つ作成し、Ghost をアップグレードしました。

実稼働サイトでです。

しかし、これは大きな間違いでした。

このアップグレードで問題が発生し、ホーム画面が真っ白になりました。しかしこのときは、バックアップを復元するだけで問題は解決すると思っていました。

アップグレード処理を復元し、すべてを元に戻そうとしましたが、ブログ記事は戻りませんでした。

何が起こったのでしょうか。

[Ghost のアップグレードに関する注意](http://support.ghost.org/how-to-upgrade/)をよく読んでみると、次のような警告がありました。

![コンテンツやデータからデータベースのコピーを取得できますが、この操作は Ghost を実行しているときには行わないでください。まず、停止してください。][GhostUpgradeWarning]

Ghost の実行中にデータのバックアップを実行しても、実際にはデータはバックアップされないのです。

これは困りました。

テスト スロットで復元していれば、この問題を発見していたでしょうから、記事をすべて失うことはなかったでしょう。

これは何よりも重要なことでした。同様のことは、[最高レベルの開発者](http://blog.codinghorror.com/international-backup-awareness-day/)にも起こる可能性があります。

バックアップのテストは必ず実施しましょう。

<a name="nextsteps"></a>
## 次のステップ
Azure Web アプリをバックアップから復元する方法については、「[Azure App Service での Web アプリの復元](web-sites-restore.md)」をご覧ください。

Azure を利用し始めるには、「[Microsoft Azure の無料評価版サイト](/pricing/free-trial/)」を参照してください。


<a name="moreaboutstorage"></a>
### ストレージ アカウントの詳細

[ストレージ アカウントとは](../storage-whatis-account.md)

[方法: ストレージ アカウントを作成する](../storage-create-storage-account/)

[ストレージ アカウントの監視方法](../storage-monitor-storage-account.md)

[Azure ストレージでの課金について](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 以前のポータルから新しいポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

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
 

<!---HONumber=62-->