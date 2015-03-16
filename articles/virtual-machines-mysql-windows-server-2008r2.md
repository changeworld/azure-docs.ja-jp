<properties 
	pageTitle="Azure 上で MySQL を実行する仮想マシンの作成" 
	description="Windows Server 2008 R2 を実行する Azure 仮想マシンを作成し、その仮想マシンで MySQL データベースのインストールと構成を行います。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="kathydav"/>


#Azure で Windows Server 2008 R2 を実行する仮想マシンへの MySQL のインストール

[MySQL](http://www.mysql.com) は、一般的なオープン ソースで、SQL データベースです。[Azure 管理ポータル][AzurePreviewPortal] を使用して、イメージ ギャラリーから Windows Server 2008 R2 を実行する仮想マシンを作成することができます。この仮想マシンには、MySQL データベースをインストールして構成することができます。

このチュートリアルでは、次の操作方法について説明します。

- 管理ポータルを使用して Windows Server 2008 R2 を実行するカスタム仮想マシンを作成します。

- 仮想マシンに MySQL Community Server をインストールして実行します。

##Windows Server を実行する仮想マシンの作成する

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

##データ ディスクの接続

仮想マシンを作成したら、データ ディスクを接続します。このディスクには、MySQL をインストールするときに必要なデータ ストレージが用意されています。「[Windows 仮想マシンにデータ ディスクをアタッチする方法](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)」を参照して、 空のディスクをアタッチする指示に従います。

##仮想マシンへのログオン
次に、MySQL をインストールするために、仮想マシンにログオンします。

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

##仮想マシンに MySQL Community Server をインストールして実行する
MySQL Community Server をインストール、構成、および実行するには、次の手順に従います。

1. リモート デスクトップを使用して、仮想マシンに接続し、**[スタート]** メニューから **Internet Explorer** を開きます。 

2. 右上にある **[ツール]** を選択します。**[インターネット オプション]** で、**[セキュリティ]** タブ、**[信頼済みサイト]** アイコンの順に選択し、**[サイト]** をクリックします。信頼済みサイトの一覧に  *http://\*.mysql.com* を追加します。

3. [MySQL Community Server のダウンロード][MySQLDownloads] に移動します。

4. **[Platform (プラットフォーム)]** ドロップ ダウン メニューで **[Microsoft Windows]** を選択し、**[Select (選択)]** をクリックします。

5. 最新リリースの **Windows (x86, 64-bit), MSI Installer** を見つけ、**[Download (ダウンロード)]** をクリックします。 

6. **[No thanks, just start my download! (いいえ、ダウンロードのみ行います)]** を選択します。(または、アカウント登録を行います)。確認メッセージが表示されたら、MySQL インストーラーをダウンロードするミラー サイトを選択して、インストーラーをデスクトップに保存します。

7. デスクトップに保存したインストーラー ファイルをダブルクリックして、インストールを開始します。

8. **[次へ]** をクリックします。

9. ライセンス条項に同意し、**[次へ]** をクリックします。

10. 一般的な機能をインストールするために **[Typical (一般)]** をクリックします。

11. **[インストール]** をクリックします。

12. MySQL 構成ウィザードを開始し、**[次へ]** をクリックします。

13. **[詳細な構成]** を選択し、[次へ] をクリックします。

14. サーバーで他のアプリケーションと共に MySQL を実行する場合は、**[Server Machine (サーバー マシン)]** を選択します。または、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

15. **[Multifunctional Database (マルチファンクション データベース)]** を選択するか、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

16. 前のセクションで接続したデータ ドライブを選択します。

	![Configure MySQL][MySQLConfig5]

17. **[Decision Support (DSS)/OLAP (意思決定支援 (DSS)/OLAP)]** を選択するか、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

18. **[Enable TCP/IP Networking (TCP/IP ネットワーキングを有効にする)]** と **[Add firewall exception for this port (このポートのファイアウォール例外を追加)]** をオンにします (これで、Windows ファイアウォールに **MySQL Serverという名前の受信規則が作成されます**)。

	![Configure MySQL][MySQLConfig7]

19. 多様な言語のテキストを格納する必要がある場合は、**[Best Support For Multilingualism (マルチリンガルのベスト サポート)]** を選択します。それ以外の場合は、他のいずれかのオプションを選択します。**[次へ]** をクリックします。

	![Configure MySQL][MySQLConfig8]

20. **[Install As Windows Service (Windows サービスとしてインストール)]** と **[Launch the MySQL Server automatically (MySQL Server を自動的に起動)]** を選択します。**[Include Bin Directory in Windows PATH (Windows パスに Bin ディレクトリーを含める)]** もオンにします。**[次へ]** をクリックします。

	![Configure MySQL][MySQLConfig9]

21. ルート パスワードを入力します。**[Enable root access from remote machines (リモート マシンのルート アクセスを有効にする)]** と **[Create An Anonymous Account (匿名アカウントを作成)]** はオンにしないでください。**[次へ]** をクリックします。

	![Configure MySQL][MySQLConfig10]

22. **[Execute (実行)]** をクリックし、構成が完了するまで待ちます。

23. **[完了]** をクリックします。

24. **[スタート]** をクリックし、**[MySQL 5.x Command Line Client (MySQL 5.x コマンド ライン クライアント)]** を選択し、コマンド ライン クライアントを起動します。

25.  先ほど作成したルート パスワードをプロンプトで入力すると、データベースを操作するための SQL ステートメントを実行できるプロンプトが表示されます。

26. 新しい MySQL ユーザーを作成するには、**mysql>** プロンプトで次のコマンドを実行します。

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	行末のセミコロン (;) は、コマンドの終わりを示すために必要です。

27. データベースを作成し、このデータベースに対するアクセス許可を  `mysqluser` ユーザーに付与するには、次のコマンドを実行します。

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	データベースのユーザー名とパスワードは、データベースに接続するスクリプトのみが使用します。データベース ユーザー アカウントは、コンピューター上の実際のユーザー アカウントを表しているとは限りません。

28. 別のコンピューターからログインするには、次のコマンドを実行します。

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	`ip-address` は、MySQL への接続元コンピューターの IP アドレスです。
	
29. MySQL コマンド ライン クライアントを終了するには、次のコマンドを実行します。

		quit

30. MySQL をインストールしたら、MySQL にリモートでアクセスできるように、エンドポイントを構成します。[Azure 管理ポータル][AzurePreviewPortal]にログインします。Azure ポータルで、**[仮想マシン]** をクリックし、新しい仮想マシンの名前をクリックして、**[エンドポイント]**、**[エンドポイントの追加]** の順にクリックします。

31. **[エンドポイントの追加]** を選択し、矢印をクリックして続行します。
	

32. エンドポイントを追加します。名前に「MySQL」、プロトコルに **[TCP]** を指定し、**[パブリック ポート]** と **[プライベート ポート]** の両方に「3306」を指定します。チェック マークをクリックします。これにより、リモートで MySQL にアクセスできます。
	

33. MySQL へのリモート接続をテストします。MySQL を実行しているローカル コンピューターから、次のようなコマンドを実行して **mysqluser** ユーザーとしてログインします。

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	仮想マシンの名前が "testwinvm" である場合は、次のコマンドを実行します。

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##リソース
MySQL の詳細については、「[MySQL Documentation](http://dev.mysql.com/doc/)」を参照してください。

[AzurePortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/



<!--HONumber=42-->
