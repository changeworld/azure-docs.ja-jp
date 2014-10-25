<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="Install MySQL" pageTitle="Create a virtual machine running MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav"></tags>

# Azure で Windows Server 2008 R2 を実行する仮想マシンへの MySQL のインストール

[MySQL][MySQL] は広く普及しているオープン ソースの SQL データベースです。[Azure 管理ポータル][Azure 管理ポータル]では、Windows Server 2008 R2 を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MySQL データベースをインストールして構成することができます。

このチュートリアルで学習する内容は次のとおりです。

-   管理ポータルを使用して Windows Server 2008 R2 を実行するカスタム仮想マシンを作成する。

-   仮想マシンに MySQL Community Server をインストールして実行する。

## Windows Server 2008 R2 を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal][create-and-configure-windows-server-2008-vm-in-portal]]

## データ ディスクの接続

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal][attach-data-disk-windows-server-2008-vm-in-portal]]

## 仮想マシンに MySQL Community Server をインストールして実行する

MySQL Community Server をインストール、構成、および実行するには、次の手順に従います。

1.  リモート デスクトップを使用して仮想マシンに接続した後で、**[スタート]** メニューから **Internet Explorer** を開きます。

2.  右上にある **[ツール]** を選択します。**[インターネット オプション]** で、**[セキュリティ]** タブ、**[信頼済みサイト]** アイコンの順に選択し、**[サイト]** をクリックします。信頼済みサイトの一覧に *http://\*.mysql.com* を追加します。

3.  [MySQL Community Server のダウンロード][MySQL Community Server のダウンロード] ページに移動します。

4.  **[Select Platform]** ドロップダウン メニューの **[Microsoft Windows]** を選択し、**[Select]** をクリックします。

5.  最新リリースの**Windows (x86, 64-bit), MSI Installer**を見つけて **[Download]** をクリックします。

6.  **[No thanks, just start my download]** を選択します (または、アカウント登録を行います)。確認メッセージが表示されたら、MySQL インストーラーをダウンロードするミラー サイトを選択して、インストーラーをデスクトップに保存します。

7.  デスクトップに保存したインストーラー ファイルをダブルクリックして、インストールを開始します。

8.  **[次へ]** をクリックします。

    ![MySQL のセットアップ][MySQL のセットアップ]

9.  使用許諾契約に同意し、**[次へ]** をクリックします。

    ![MySQL のセットアップ][1]

10. 一般的な機能をインストールするために **[Typical]** をクリックします。

    ![MySQL のセットアップ][2]

11. **[インストール]** をクリックします。

    ![MySQL のセットアップ][3]

12. MySQL 構成ウィザードを開始し、**[次へ]** をクリックします。

    ![MySQL の構成][MySQL の構成]

13. **[Detailed Configuration]** を選択し、[次へ] をクリックします。

    ![MySQL の構成][4]

14. サーバーで他のアプリケーションと共に MySQL を実行する場合は **[Server Machine]** を選択します。または、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

    ![MySQL の構成][5]

15. **[Multifunctional Database]** を選択するか、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

    ![MySQL の構成][6]

16. 先ほど接続したデータ ドライブを選択します。

    ![MySQL の構成][7]

17. **[Decision Support (DSS)/OLAP]** を選択するか、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

    ![MySQL の構成][8]

18. **[Enable TCP/IP Networking]** と **[Add firewall exception for this port]** をオンにします (これにより、Windows ファイアウォールに **MySQL Server** という名前の受信規則が作成されます)。

    ![MySQL の構成][9]

19. テキストを多言語で格納する必要がある場合は **[Best Support For Multilingualism]** を選択します。または、ニーズに合う他のオプションを選択します。**[次へ]** をクリックします。

    ![MySQL の構成][10]

20. **[Install As Windows Service]** と **[Launch the MySQL Server automatically]** をオンにします。**[Include Bin Directory in Windows PATH]** もオンにします。**[次へ]** をクリックします。

    ![MySQL の構成][11]

21. ルート パスワードを入力します。**[Enable root access from remote machines]** と **[Create An Anonymous Account]** はオンにしないでください。**[次へ]** をクリックします。

    ![MySQL の構成][12]

22. **[Execute]** をクリックして、構成が完了するまで待ちます。

    ![MySQL の構成][13]

23. **[完了]** をクリックします。

    ![MySQL の構成][14]

24. **[スタート]** をクリックし、**[MySQL 5.x Command Line Client]** を選択して、コマンド ライン クライアントを起動します。

25. 先ほど作成したルート パスワードをプロンプトで入力すると、データベースを操作するための SQL ステートメントを実行できるプロンプトが表示されます。

26. 新しい MySQL ユーザーを作成するには、**mysql\>** プロンプトで次のコマンドを実行します。

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

30. MySQL をインストールしたら、MySQL にリモートでアクセスできるように、エンドポイントを構成する必要があります。[Azure 管理ポータル][Azure 管理ポータル]にログインします。Azure ポータルで、**[仮想マシン]** をクリックし、新しい仮想マシンの名前をクリックして、**[エンドポイント]**、**[エンドポイントの追加]** の順にクリックします。

    ![エンドポイン][エンドポイン]

31. **[エンドポイントの追加]** を選択し、矢印をクリックして続行します。

    ![エンドポイント][15]

32. エンドポイントを追加します。名前に「MySQL」、プロトコルに **[TCP]** を指定し、**[パブリック ポート]** と **[プライベート ポート]** の両方に「3306」を指定します。チェック マークをクリックします。これにより、MySQL へのリモート アクセスが可能になります。

    ![エンドポイント][16]

33. Azure の仮想マシンで実行されている MySQL に、リモートでアクセスすることができます。MySQL を実行しているローカル コンピューターから次のコマンドを実行すると、先ほど作成した **mysqluser** ユーザーとしてログインできます。

        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

    たとえば、このチュートリアルで作成した仮想マシンを使用する場合、コマンドは次のようになります。

        mysql -u mysqluser -p -h testwinvm.cloudapp.net

## まとめ

このチュートリアルでは、Windows Server 2008 R2 仮想マシンを作成し、リモート接続する方法を説明しました。仮想マシンに MySQL をインストールして構成し、データベースと新しい MySQL ユーザーを作成する方法も学習しました。MySQL の詳細については、[MySQL のドキュメント][MySQL のドキュメント]を参照してください。

  [MySQL]: http://www.mysql.com
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [create-and-configure-windows-server-2008-vm-in-portal]: ../includes/create-and-configure-windows-server-2008-vm-in-portal.md
  [attach-data-disk-windows-server-2008-vm-in-portal]: ../includes/attach-data-disk-windows-server-2008-vm-in-portal.md
  [MySQL Community Server のダウンロード]: http://www.mysql.com/downloads/mysql/
  [MySQL のセットアップ]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
  [1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
  [2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
  [3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
  [MySQL の構成]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
  [4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
  [5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
  [6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
  [7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
  [8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
  [9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
  [10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
  [11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
  [12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
  [13]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
  [14]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
  [エンドポイン]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
  [15]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
  [16]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
  [MySQL のドキュメント]: http://dev.mysql.com/doc/
