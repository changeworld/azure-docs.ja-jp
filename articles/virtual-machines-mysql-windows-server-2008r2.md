<properties urlDisplayName="Install MySQL" pageTitle="Azure 上で MySQL を実行する仮想マシンの作成 " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="kathydav" />


#Azure で Windows Server 2008 R2 を実行する仮想マシンへの MySQL のインストール

[MySQL](http://www.mysql.com)  は広く普及しているオープン ソースの SQL データベースです。[Azure の管理ポータル][AzurePreviewPortal]では、Windows Server 2008 R2 を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MySQL データベースをインストールして構成することができます。

このチュートリアルでは、次の操作方法について説明します。

- 管理ポータルを使用して Windows Server 2008 R2 を実行するカスタム仮想マシンを作成する。

- 仮想マシンに MySQL Community Server をインストールして実行する。

##Windows Server を実行する仮想マシンの作成

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

##データ ディスクの接続

仮想マシンが作成されたら、データ ディスクに接続します。このディスクには、MySQL をインストールするときに必要なデータ ストレージが用意されています。「[データ ディスクを Windows 仮想マシンに接続する方法](http://azure.microsoft.com/ja-jp/documentation/articles/storage-windows-attach-disk/) 」を参照し、指示に従って空のディスクを接続してください。

##仮想マシンへのログオン
次に、MySQL をインストールするために、仮想マシンにログオンします。

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

##仮想マシンに MySQL Community Server をインストールして実行する
MySQL Community Server をインストール、構成、および実行するには、次の手順に従います。

1. リモート デスクトップを使用して仮想マシンに接続した後で、**[スタート]** メニューから **Internet Explorer** を開きます。 

2. 右上にある **[ツール]** を選択します。**[インターネット オプション]** で、**[セキュリティ]** タブ、**[信頼済みサイト]** アイコンの順に選択し、**[サイト]** をクリックします。信頼済みサイトの一覧に *http://\*.mysql.com* を追加します。

3. [MySQL Community Server のダウンロード ページ][MySQLDownloads]に移動します。

4. **[Platform]** メニューの **[Microsoft Windows]** を選択し、**[Select]** をクリックします。

5. 最新リリースの **[Windows (x86, 64-bit), MSI Installer]** を見つけて **[Download]** をクリックします。 

6. **[No thanks, just start my download]** を選択します(または、アカウント登録を行います)。確認メッセージが表示されたら、MySQL インストーラーをダウンロードするミラー サイトを選択して、インストーラーをデスクトップに保存します。

7. デスクトップに保存したインストーラー ファイルをダブルクリックして、インストールを開始します。

8. **[次へ]** をクリックします。

9. ライセンス契約の内容に同意し、**[Next]** をクリックします。

10. 一般的な機能をインストールするために **[Typical]** をクリックします。

11. **[Install]** をクリックします。

12. MySQL 構成ウィザードを開始し、**[Next]** をクリックします。

13. **[Detailed Configuration]** を選択して [Next] をクリックします。

14. サーバーで他のアプリケーションと共に MySQL を実行する場合は **[Server Machine]** を選択します。または、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

15. **[Multifunctional Database]** を選択するか、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

16. 前のセクションで接続したデータ ドライブを選択します。

	![Configure MySQL][MySQLConfig5]

17. **[Decision Support (DSS)/OLAP]** を選択するか、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

18. **[Enable TCP/IP Networking]** と **[Add firewall exception for this port]** をオンにします (これにより、Windows ファイアウォールに **MySQL Server** という名前の受信規則が作成されます)。

	![Configure MySQL][MySQLConfig7]

19. 多様な言語のテキストを格納する必要がある場合は、**[Best Support For Multilingualism]** を選択します。それ以外の場合は、他のいずれかのオプションを選択します。**[次へ]** をクリックします。

	![Configure MySQL][MySQLConfig8]

20. **[Install As Windows Service]** と **[Launch the MySQL Server automatically]** オンにします。**[Include Bin Directory in Windows PATH]** もオンにします。**[次へ]** をクリックします。

	![Configure MySQL][MySQLConfig9]

21. ルート パスワードを入力します。**[Enable root access from remote machines]** と **[Create An Anonymous Account]** はオンにしないでください。**[次へ]** をクリックします。

	![Configure MySQL][MySQLConfig10]

22. **[Execute]** をクリックして、構成が完了するまで待ちます。

23. **[完了]** をクリックします。

24. **[スタート]** をクリックし、**[MySQL 5.x Command Line Client]** を選択して、コマンド ライン クライアントを起動します。

25.  先ほど作成したルート パスワードをプロンプトで入力すると、データベースを操作するための SQL ステートメントを実行できるプロンプトが表示されます。

26. 新しい MySQL ユーザーを作成するには、**mysql>** プロンプトで次のコマンドを実行します。

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	行末のセミコロン (;) は、コマンドの終わりを示すために必要です。

27. データベースを作成し、このデータベースに対するアクセス許可を `mysqluser` ユーザーに付与するには、次のコマンドを実行します。

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	データベースのユーザー名とパスワードは、データベースに接続するスクリプトのみが使用します。データベース ユーザー アカウントは、コンピューター上の実際のユーザー アカウントを表しているとは限りません。

28. 別のコンピューターからログインするには、次のコマンドを実行します。

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	`ip-address` は、MySQL への接続元コンピューターの IP アドレスです。
	
29. MySQL コマンド ライン クライアントを終了するには、次のコマンドを実行します。

		quit

30. MySQL をインストールしたら、MySQL にリモートでアクセスできるように、エンドポイントを構成します。[Azure の管理ポータル][AzurePreviewPortal]にログインします。Azure ポータルで、**[仮想マシン]** をクリックし、新しい仮想マシンの名前をクリックして、**[エンドポイント]**、**[エンドポイントの追加]** の順にクリックします。

31. **[エンドポイントの追加]** を選択し、矢印をクリックして続行します。
	

32. エンドポイントを追加します。名前に「MySQL」、プロトコルに **[TCP]** を指定し、**[パブリック ポート]** と **[プライベート ポート]** の両方に「3306」を指定します。チェック マークをクリックします。これにより、リモートで MySQL にアクセスできます。
	

33. MySQL へのリモート接続をテストします。MySQL を実行しているローカル コンピューターから、次のようなコマンドを実行して **mysqluser** ユーザーとしてログインします。

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	仮想マシンの名前が "testwinvm" である場合は、次のコマンドを実行します。

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##リソース
MySQL の詳細については、[MySQL のドキュメント](http://dev.mysql.com/doc/)を参照してください。

[AzurePortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png


<!--HONumber=35_1-->
