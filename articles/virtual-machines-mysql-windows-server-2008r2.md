<properties 
	pageTitle="Azure 上で MySQL を実行する仮想マシンの作成" 
	description="Windows Server 2012 R2 を実行する Azure 仮想マシンを作成し、そこで MySQL データベースのインストールと構成を行います。" 
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
	ms.date="02/17/2015" 
	ms.author="kathydav"/>


# Azure で Windows Server 2012 R2 を実行する仮想マシンへの MySQL のインストール


[MySQL](http://www.mysql.com)は、一般的なオープン ソースで、SQL データベースです。[Azure 管理ポータル](http://manage.windowsazure.com)では、Windows Server 2012 R2 を実行する仮想マシンをイメージ ギャラリーから作成できます。次に、MySQL Server としてこれをインストールおよび構成することができます。


このチュートリアルでは、次の操作方法について説明します。

- 管理ポータルを使用して Windows Server 2012 R2 を実行するカスタム仮想マシンを作成します。

- MySQL 5.6.23 の Community バージョンを MySQL Server としてこの仮想マシン上にインストールおよび実行します。


## Windows Server を実行する仮想マシンの作成する

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## データ ディスクの接続

仮想マシンを作成したら、追加のデータ ディスクを必要に応じて接続することができます。現在、OS ドライブ (C:) はオペレーティング システムを含めて容量が 127 GB に制限されていますが、ここでデータ ディスクを追加すると、ドライブ容量の不足を回避できるため運用環境のワークロードで推奨されています。

「[データ ディスクを Windows 仮想マシンに追加する方法](../storage-windows-attach-disk/) 」を参照し、指示に従って空のディスクを接続してください。ホストのキャッシュ設定を **[なし]** または **[読み取り専用]** に設定します。

## 仮想マシンへのログオン

次に、MySQL をインストールするために、仮想マシンにログオンします。

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]
 
##仮想マシンに MySQL Community Server をインストールして実行する

MySQL Server の Community バージョンをインストール、構成、および実行するには、次の手順を実行します。

> [AZURE.NOTE] これらの手順は、MySQL 5.6.23.0 の Community バージョンおよび Windows Server 2012 R2 で実行する手順です。異なるバージョンの MySQL または Windows Server を使用しているユーザーは、同一の結果にならない場合があります。

1.	リモート デスクトップを使用して仮想マシンに接続し、スタート画面で **[Internet Explorer]** をクリックします。
2.	右上隅の **[ツール]** (歯車のアイコン) を選択して、**[インターネット オプション]** をクリックします。**[セキュリティ]** タブ、**[信頼済みサイト]** アイコン、**[サイト]** の順にクリックします。信頼済みサイトの一覧に **http://*.mysql.com** を追加します。**[閉じる]**、**[OK]** の順にクリックします。
3.	Internet Explorer のアドレス バーに「**http://dev.mysql.com/downloads/mysql/**」と入力します。
4.	MySQL サイトで MySQL Installer for Windows の最新バージョンを見つけ、インストールします。MySQL Installer を選ぶ際、すべてのファイル セットが揃っているバージョンをダウンロードし (ファイル サイズが 282.4 MB の mysql-installer-community-5.6.23.0.msi など)、このインストーラー ファイルを Windows デスクトップに保存します。
5.	デスクトップからこのインストーラー ファイルをダブルクリックしてインストールを開始します。
6.	**[License Agreement]** ページで、ライセンス契約の内容に同意し、**[Next]** をクリックします。
7.	**[Choosing a Setup Type]** ページで目的のセットアップの種類をクリックしてから、**[Next]** をクリックします。次の手順は、セットアップの種類として **[Server only]** を選択したことを前提にしています。
8.	**[Installation]** ページで、**[Execute]** をクリックします。インストールが完了したら、**[Next]** をクリックします。
9.	**[Product Configuration]** ページで、**[Next]** をクリックします。
10.	**[Type and Networking]** ページで、必要な構成の種類を指定します。さらに、TCP ポートなど接続性のオプションについても必要に応じて指定します。**[Show Advanced Options]**、**[Next]** の順にクリックします。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_TypeNetworking.png)
 
11.	**[Accounts and Roles]** ページで強力な MySQL のルート パスワードを指定します。必要に応じて別の MySQL ユーザー アカウントを追加し、**[Next]** をクリックします。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AccountsRoles_Filled.png)
 
12.	**[Windows Service]** ページで、Windows サービスとして MySQL Server を実行するための、既定の設定に対する変更を必要に応じて指定し、**[Next]** をクリックします。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_WindowsService.png)
 
13.	**[Advanced Options]** ページで、必要に応じてログ オプションに対する変更を指定し、**[Next]** をクリックします。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AdvOptions.png)
 
14.	**[Apply Server Configuration]** ページで、**[Execute]** をクリックします。構成手順の終了後、**[Finish]** をクリックします。
15.	**[Product Configuration]** ページで、**[Next]** をクリックします。
16.	**[Installation Complete]** ページで、後で内容を検討する場合は **[Copy Log to Clipboard]** をクリックしてから **[Finish]** をクリックします。
17.	スタート画面で「**mysql**」と入力してから、**[MySQL 5.6 Command Line Client]** をクリックします。
18.	手順 11. で指定したルート パスワードを入力すると、MySQL を構成するためのコマンドを実行できるプロンプトが表示されます。コマンドと構文の詳細については、「[MySQL Reference Manuals](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)」を参照してください。

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_CommandPrompt.png)
 
19.	また、**C:\Program Files (x86)\MySQL\MySQL Server 5.6\my-default.ini** ファイルに入力することにより、ベース ディレクトリやデータ ディレクトリ、ドライブなど、サーバー構成に関する既定の設定についても構成できます。詳細については、「[5.1.2 Server Configuration Defaults](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)」を参照してください。


インターネット上の MySQL クライアント コンピューターで MySQL Server サービスを利用可能にするには、MySQL Server サービスがリッスンしている TCP ポートにエンドポイントを構成する必要があります。前の手順 10. で説明した [Type and Networking] ページで別のポートを指定した場合を除き、これは TCP ポート 3306 です。

MySQL Server サービスのエンドポイントを構成するには、次のようにします。

1.	Azure の管理ポータルで、**[仮想マシン]**、MySQL 仮想マシンの名前、**[エンドポイント]** の順にクリックします。
2.	コマンド バーで、**[追加]** をクリックします。
3.	**[仮想マシンにエンドポイントを追加します]** ページで、右矢印をクリックします。
4.	既定の MySQL TCP ポート 3306 を使用している場合は、**[Name]** の **[MySQL]** をクリックし、チェック マークをクリックします。
5.	別の TCP ポートを使用している場合は、**[Name]** に一意の名前を入力します。プロトコルに **[TCP]** を選択し、**[Public Port]** と **[Private Port]** の両方にポート番号を入力してからチェック マークをクリックします。


Azure 仮想マシンで実行する MySQL Server サービスへのリモート接続をテストするには、最初に、MySQL Server を実行する仮想マシンが属しているクラウド サービスに対応する DNS 名を特定する必要があります。 

1.	Azure の管理ポータルで、**[仮想マシン]**、MySQL Server 仮想マシンの名前、**[ダッシュボード]** の順にクリックします。
2.	仮想マシンのダッシュボードは、**[概要]** セクションにある **[DNS 名]** の値をメモします。たとえば次のようになります。 

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_DNSName.png)
 
3.	MySQL または MySQL クライアントを実行するローカル コンピューターで、次のコマンドを実行して MySQL ユーザーとしてログインします。

		mysql -u <yourMysqlUsername> -p -h <yourDNSname>
	
	たとえば、MySQL のユーザー名が **dbadmin3** で、仮想マシンの DNS 名が **testmysql.cloudapp.net** の場合、実際に実行するコマンドは次のようになります。

		mysql -u dbadmin3 -p -h testmysql.cloudapp.net


##  リソース

MySQL の詳細については、「[MySQL Documentation](http://dev.mysql.com/doc/)」を参照してください。



<!--HONumber=45--> 
