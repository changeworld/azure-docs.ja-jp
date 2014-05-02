<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="MySQL のインストール" pageTitle="Azure で MySQL を実行する仮想マシンを作成する" metaKeywords="Azure の仮想マシン, Azure Windows Server, Azure での MySQL のインストール, Azure での MySQL の構成, Azure データベース" description="Windows Server 2008 R2 を実行する Azure の仮想マシンを作成し、仮想マシンに MySQL データベースをインストールして構成します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure で Windows Server 2008 R2 を実行する仮想マシンへの MySQL のインストール" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





#Azure で Windows Server 2008 R2 を実行する仮想マシンへの MySQL のインストール

[MySQL](http://www.mysql.com) は広く普及しているオープン ソースの SQL データベースです。[Azure 管理ポータル][AzurePreviewPortal]では、Windows Server 2008 R2 を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MySQL データベースをインストールして構成することができます。

このチュートリアルで学習する内容は次のとおりです。

- 管理ポータルを使用して Windows Server 2008 R2 を実行するカスタム仮想マシンを作成する。

- 仮想マシンに MySQL Community Server をインストールして実行する。

##Windows Server 2008 R2 を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

##データ ディスクの接続

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

##仮想マシンへの MySQL Community Server のインストールと実行
MySQL Community Server をインストール、構成、および実行するには、次の手順に従います。

1. リモート デスクトップを使用して仮想マシンに接続した後で、**[スタート]** メニューから **Internet Explorer** を開きます。

2. 右上にある **[ツール]** ボタンを選択します。**[インターネット オプション]** で、**[セキュリティ]** タブ、**[信頼済みサイト]** アイコンの順に選択し、**[サイト]** をクリックします。信頼済みサイトの一覧に *http://\*.mysql.com* を追加します。

3. [MySQL Community Server のダウンロード ページ][MySQLDownloads]に移動します。

4. **[Select Platform]** ドロップダウン メニューの **[Microsoft Windows]** を選択し、**[Select]** をクリックします。

5. 最新リリースの **[Windows (x86, 64-bit), MSI Installer]** を見つけて **[Download]** をクリックします。

6. **[No thanks, just start my download]** を選択します (または、アカウント登録を行います)。確認メッセージが表示されたら、MySQL インストーラーをダウンロードするミラー サイトを選択して、インストーラーをデスクトップに保存します。

7. デスクトップに保存したインストーラー ファイルをダブルクリックして、インストールを開始します。

8. **[次へ]** をクリックします。

	![MySQL のセットアップ][MySQLInstall1]

9. ライセンス契約の内容に同意し、**[Next]** をクリックします。

	![MySQL のセットアップ][MySQLInstall2]

10. 一般的な機能をインストールするために **[Typical]** をクリックします。

	![MySQL のセットアップ][MySQLInstall3]

11. **[Install]** をクリックします。

	![MySQL のセットアップ][MySQLInstall4]

12. MySQL 構成ウィザードを開始し、**[Next]** をクリックします。

	![MySQL の構成][MySQLConfig1]

13. **[Detailed Configuration]** を選択して [Next] をクリックします。

	![MySQL の構成][MySQLConfig2]

14. サーバーで他のアプリケーションと共に MySQL を実行する場合は **[Server Machine]** を選択します。または、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

	![MySQL の構成][MySQLConfig3]

15. **[Multifunctional Database]** を選択するか、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

	![MySQL の構成][MySQLConfig4]

16. 先ほど接続したデータ ドライブを選択します。

	![MySQL の構成][MySQLConfig5]

17. **[Decision Support (DSS)/OLAP]** を選択するか、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

	![MySQL の構成][MySQLConfig6]

18. **[Enable TCP/IP Networking]** と **[Add firewall exception for this port]** をオンにします (これにより、Windows ファイアウォールに **MySQL Server** という名前の受信規則が作成されます)。

	![MySQL の構成][MySQLConfig7]

19. テキストを多言語で格納する必要がある場合は **[Best Support For Multilingualism]** を選択します。または、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

	![MySQL の構成][MySQLConfig8]

20. **[Install As Windows Service]** と **[Launch the MySQL Server automatically]** をオンにします。**[Include Bin Directory in Windows PATH]** もオンにします。**[次へ]** をクリックします。

	![MySQL の構成][MySQLConfig9]

21. ルート パスワードを入力します。**[Enable root access from remote machines]** と **[Create An Anonymous Account]** はオンにしないでください。**[次へ]** をクリックします。

	![MySQL の構成][MySQLConfig10]

22. **[Execute]** をクリックして、構成が完了するまで待ちます。

	![MySQL の構成][MySQLConfig11]

23. **[完了]** をクリックします。

	![MySQL の構成][MySQLConfig12]

24. **[スタート]** をクリックし、**[MySQL 5.x Command Line Client]** を選択して、コマンド ライン クライアントを起動します。

25. 先ほど作成したルート パスワードをプロンプトで入力すると、データベースを操作するための SQL ステートメントを実行できるプロンプトが表示されます。

26. 新しい MySQL ユーザーを作成するには、**mysql>** プロンプトで次のコマンドを実行します。

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	行末のセミコロン (;) は、コマンドの終わりを示すために必要です。

27. データベースを作成し、このデータベースに対するアクセス許可を `mysqluser` ユーザーに付与するには、次のコマンドを実行します。

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	データベースのユーザー名とパスワードは、データベースに接続するスクリプトのみが使用します。データベース ユーザー アカウントは、コンピューター上の実際のユーザー アカウントを表しているとは限りません。

28. 別のコンピューターにログインするには、次のコマンドを実行します。

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	`ip-address` は、MySQL への接続元コンピューターの IP アドレスです。
	
29. MySQL コマンド ライン クライアントを終了するには、次のコマンドを実行します。

		quit

30. MySQL をインストールしたら、MySQL にリモートでアクセスできるように、エンドポイントを構成する必要があります。[Azure 管理ポータル][AzurePreviewPortal]にログインします。Azure ポータルで、**[仮想マシン]** をクリックし、新しい仮想マシンの名前をクリックして、**[エンドポイント]**、**[エンドポイントの追加]** の順にクリックします。

	![エンドポイント][AddEndPoint]

31. **[エンドポイントの追加]** を選択し、矢印をクリックして続行します。
	
	![エンドポイント][AddEndPoint2]

32. エンドポイントを追加します。名前に「MySQL」、プロトコルに **[TCP]** を指定し、**[パブリック ポート]** と **[プライベート ポート]** の両方に「3306」を指定します。チェック マークをクリックします。これにより、MySQL へのリモート アクセスが可能になります。
	
	![エンドポイント][AddEndPoint3]

33. Azure の仮想マシンで実行されている MySQL に、リモートでアクセスすることができます。MySQL を実行しているローカル コンピューターから次のコマンドを実行すると、先ほど作成した **mysqluser** ユーザーとしてログインできます。

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	たとえば、このチュートリアルで作成した仮想マシンを使用する場合、コマンドは次のようになります。

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##まとめ

このチュートリアルでは、Windows Server 2008 R2 仮想マシンを作成し、リモート接続する方法を説明しました。仮想マシンに MySQL をインストールして構成し、データベースと新しい MySQL ユーザーを作成する方法も学習しました。MySQL の詳細については、[MySQL のドキュメント](http://dev.mysql.com/doc/)を参照してください。

[AzurePreviewPortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLInstall1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
[MySQLInstall2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
[MySQLInstall3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
[MySQLInstall4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
[MySQLConfig1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
[MySQLConfig2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
[MySQLConfig3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
[MySQLConfig4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
[MySQLConfig11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
[MySQLConfig12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
[AddEndPoint]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
[AddEndPoint2]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
[AddEndPoint3]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png

