<properties
	pageTitle="Microsoft Azure を使用した LAMP スタックの作成方法"
	description="Linux を実行する Azure の仮想マシン (VM) を使用して、Microsoft Azure で LAMP スタックを設定する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/10/2015"
	ms.author="ningk"/>

#Microsoft Azure を使用した LAMP スタックの作成方法

"LAMP" スタックはオープン ソースのソフトウェアのグループであり、通常はまとめてインストールすることで、サーバーが動的な Web サイトや Web アプリケーションをホストできるようになります。この用語は、Apache Web サーバーを使用した Linux オペレーティング システムを表す頭字語です。サイト データは MySQL データベースに格納され、動的なコンテンツは PHP が処理します。

このガイドでは、Linux イメージにインストールした LAMP スタックを取得して、Microsoft Azure にデプロイします。

学習内容:

-	Azure 仮想マシンを作成する方法。
-	LAMP スタック用に仮想マシンを準備する方法。
-	仮想マシンの LAMP サーバーに必要なソフトウェアのをインストールする方法。

読者は既に Azure サブスクリプションを持っていることを前提にしています。持っていない場合は、[http://azure.microsoft.com](http://azure.microsoft.com) で無料試用版にサインアップしてください。MSDN サブスクリプションを持っている場合は、[Microsoft Azure 特別料金: MSDN、MPN、BizSpark の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)をご覧ください。Azure の詳細については、「[Azure とは](http://azure.microsoft.com/overview/what-is-azure/)」を参照してください。

このトピックに加え、既に仮想マシンをインストールしていており、別の Linux ディストリビューションへの LAMP スタックのインストールの基本について知りたい場合は、「[Azure 上の Linux 仮想マシンへの LAMP スタックのインストール](virtual-machines-linux-install-lamp-stack.md)」を参照してください。

Azure Marketplace から事前構成済みの LAMP イメージをデプロイすることもできます。次の 10 分間のビデオは、Azure Marketplace から構築済みの LAMP イメージをデプロイする方法を紹介しています (Azure VM 上の LAMP スタック (https://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman))。

##フェーズ 1: イメージを作成する
このフェーズでは、Azure の Linux イメージを使用して仮想マシンを作成します。

###手順 1. SSH 認証キーを生成する
SSH はシステム管理者にとって重要なツールです。しかし、セキュリティを考慮すると、人間が決めたパスワードに依存することが常に賢い選択であるとはいえません。強力な SSH キーを使用すれば、パスワードの心配をすることなく、リモート アクセスをオープンにしておくことができます。このメソッドは、非対称暗号化方式による認証で構成されています。ユーザーの秘密キーが認証を付与するキーとなります。ユーザーのアカウントをロックすれば、パスワード認証を完全に無効にできます。

SSH 認証キーを生成するには、次の手順に従います。

-	次の場所から puttygen をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/)
-	puttygen.exe を実行します。
-	**[生成]** をクリックしてキーを生成します。処理中にウィンドウの空白の領域にマウスを移動すると、ランダム性を高めることができます。![][1]
-	生成処理が終わると、生成されたキーが表示されます。次に例を示します。![][2]
-	**[キー]** の公開キーを選択してコピーし、**publicKey.pem** という名前のファイルに保存します。保存された公開キーの形式は使用する公開キーの形式と異なるため、**[公開キーの保存]** をクリックしないでください。
-	**[秘密キーの保存]** をクリックし、**privateKey.ppk** という名前のファイルに保存します。

###手順 2. Azure ポータルでイメージを作成する
[Azure ポータル](https://portal.azure.com/)で、タスク バーの **[新規]** をクリックし、次の手順に従ってイメージを作成して、ニーズに合った Linux イメージを選択します。この例では Ubuntu 14.04 イメージを使用します。

![][3]

**[ホスト名]** に、ユーザーとインターネット クライアントがこの仮想マシンへのアクセスに使用する URL の名前を指定します。DNS 名の最後の部分を定義すると (LAMPDemo など)、Lampdemo.cloudapp.net のように Azure が URL を生成します。

**[ユーザー名]** に、後で仮想マシンにログインするときに使用する名前を選択します。

**[SSH 認証キー]** に、**publicKey.pem** ファイルのキーの値をコピーします。このファイルには、puttygen によって生成された公開キーが含まれています。

![][4]

必要に応じて、他の設定を構成し、**[作成]** をクリックします。

##フェーズ 2: LAMP スタック用の仮想マシンを準備する
このフェーズでは、Web トラフィックのエンドポイントを構成し、新しい仮想マシンに接続します。

###手順 1. Web アクセスを許可する HTTP ポートを開く
Azure のエンドポイントはプロトコル (TCP または UDP) の他に、パブリック ポートとプライベート ポートで構成されます。プライベート ポートとは、サービスが仮想マシンをリッスンするポートです。パブリック ポートとは、Azure クラウド サービスがインターネット ベースのトラフィックを外部でリッスンするポートです。これが同じポート番号になる場合もあります。

TCP ポート 80 は、Apache がリッスンする既定のポート番号です。このポートを Azure エンドポイントで開くと、ユーザーやその他のインターネット クライアントが Apache Web サーバーにアクセスできるようになります。

Azure ポータルで、**[参照]、[仮想マシン]** の順にクリックし、作成した仮想マシンをクリックします。

![][5]

仮想マシンにエンドポイントを追加するには、**[エンドポイント]** ボックスをクリックします。

![][6]

**[追加]** をクリックします。新しい仮想マシンをプロビジョニングするときには、必要に応じてエンドポイントを有効または無効にできます。

エンドポイントを構成します。

1.	**[エンドポイント]** にエンドポイントの名前を入力します。
2.	**[パブリック ポート]** に「80」と入力します。Apache の既定のリッスン ポートを変更している場合は、Apache のリッスン ポートと同じになるように [プライベート ポート] を更新する必要があります。
3.	**[パブリック ポート]** に「80」と入力します。既定では、HTTP トラフィックはポート 80 を使用します。80 に設定している場合は、Apache Web サービスに接続するための URL にポート番号を含める必要はありません。たとえば、「http://lampdemo.cloudapp.net」のように入力します。Apache のリッスン ポートを 81 などの別の値に設定している場合は、Apache Web サービスにアクセスするための URL にポート番号を追加する必要があります。(例: http://lampdemo.cloudapp.net:81/)。

![][7]

**[OK]** をクリックして、仮想マシンにエンドポイントを追加します。




###手順 2. 作成したイメージに接続する
SSH ツールを選択すると、新しい仮想マシンに接続できます。この例では、Putty を使用します。

最初に、Azure ポータルから仮想マシンの DNS 名を取得します。**[参照]、[仮想マシン]**、仮想マシン名、**[プロパティ]** の順にクリックし、**[プロパティ]** タイルの "**ドメイン名**" フィールドを調べます。

**[SSH]** フィールドから、SSH 接続のポート番号を取得します。たとえば次のようになります。

![][8]

[ここ](http://www.putty.org/)から Putty をダウンロードします

ダウンロード後、実行可能ファイル PUTTY.EXE をクリックします。仮想マシンのプロパティから取得したホスト名とポート番号を使用して、基本オプションを構成します。たとえば次のようになります。

![][9]

左側のウィンドウで、**[接続]、[SSH]、[認証]** の順にクリックし、**[参照]** をクリックして **privateKey.ppk** ファイルの場所を指定します。このファイルには、「フェーズ 1: イメージを作成する」で puttygen によって生成された秘密キーが含まれています。たとえば次のようになります。

![][10]

**[開く]** をクリックします。メッセージ ボックスによるアラートが表示される場合があります。DNS 名とポート番号を正しく構成してある場合は、**[はい]** をクリックします。

![][11]


次のように表示されます。

![][12]

「フェーズ 1: イメージを作成する」で仮想マシンを作成したときに指定したユーザー名を入力します。次のように表示されます。

![][13]

##フェーズ 3: LAMP スタックをインストールする

仮想マシンの作成に使用した Linux ディストリビューションによって、LAMP スタックのインストール方法が異なります。次のセクションには、よく使用される一部の Linux OS の一般的な手順が記載されています。

###Red Hat、CentOS ベース

####Apache のインストール
Apache をインストールするには、ターミナルを開いて次のコマンドを実行します。

	sudo yum install httpd

インストールが終わったら、次のコマンドを使用して Apache を起動します。

	sudo service httpd start
####Apache のテスト
Apache が正常にインストールされたかどうかを確認するには、Apache サーバーの DNS 名を参照します (この記事のサンプル URL の場合は、http://lampdemo.cloudapp.net/))。ページには "It works! (成功です)" と表示されます。![][14]

####トラブルシューティング
Apache は実行されているが、上述の Apache の既定ページが表示されない場合は、次の項目を確認する必要があります。

-	Apache Web サービス リッスン アドレス / ポート
	-	Azure 仮想マシンのエンドポイント設定を確認します。エンドポイントが適切に構成されていることを確認します。この記事の「フェーズ 1: イメージを作成する」の手順を参照してください。
	-	/etc/httpd/conf/httpd.conf を開き、「Listen」の文字列を検索します。Apache のリッスン ポートが、エンドポイントに構成した [プライベート ポート] と同じになっていることを確認します。Apache の既定のポートは 80 です。たとえば次のようになります。  

			……
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			……
			……  

-	ファイアウォール、iptables の構成: ローカル ホストから Apache の既定のページを見られる場合は、Apache がリッスンしているポートがファイアウォールによってブロックされていることが原因になっている場合があります。w3m ツールを使用すると、Apache Web ページを参照できます。次のコマンドは w3m をインストールして、Apache の既定のページを参照します。

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	問題の原因がファイアウォールや iptables の場合は、/etc/sysconfig/iptables に次の行を追加します。

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	2 つ目の行は https トラフィックの場合にのみ必要です。

	次のように、これらの行はグローバルにアクセスが制限されている行の上に配置します。

		-A INPUT -j REJECT --reject-with icmp-host-prohibited  

	新しい設定を有効にするには、次のコマンドを使用します。

		service iptables restart

####MySQL のインストール
MySQL をインストールするには、ターミナルを開いて次のコマンドを実行します。

	sudo yum install mysql-server
	sudo service mysqld start

インストール中に、MySQL よって許可が 2 度求められます。両方に [はい] を選ぶと、MySQL がインストールされます。

####MySQL の構成
インストールが完了したら、次の方法で root MySQL パスワードを設定できます。

	sudo /usr/bin/mysql_secure_installation  

プロンプトで、現在の root パスワードを尋ねられます。

MySQL をインストールしたばかりであるため、多くの場合はパスワードが設定されていません。その場合は空白のままにして、ENTER キーを押します。

	Enter current password for root (enter for none):
	OK, successfully used password, moving on...  

root パスワードを設定するよう求められます。パスワードを設定し、[Y] を選択して手順に従います。

CentOS では MySQL のセットアップ プロセスが自動化されており、「はい」や「いいえ」で答える一連の質問が表示されます。これらの質問は次のとおりです。構成に対して [Y] か [N] を選択します。最後に、MySQL が再読み込みされ、新しい変更が実装されます。

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] y
	... Success!

	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	Cleaning up...

	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.

	Thanks for using MySQL!  

####PHP のインストール
PHP は、動的な Web ページを構築するのに広く使われているオープン ソースの Web スクリプト言語です。

仮想マシンに PHP をインストールするには、ターミナルを開いて次のコマンドを実行します。

	sudo yum install php php-mysql  

ソフトウェア パッケージをダウンロードするには、“y” で回答します。次に、「Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key)」に “y” で回答します。PHP がインストールされます。

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Debian、Ubuntu ベース
このテストは Ubuntu 14.04 で実行したものです。

Ubuntu は Debian をベースにしています。Red Hat シリーズと同じ方法で LAMP スタックをインストールできます。手順を簡略化するには、Tasksel ツールを使用します。

Tasksel は、複数の関連パッケージを一連のタスクとしてシステムにインストールする、Debian/Ubuntu ツールです。詳細については、[Tasksel のコミュニティ ヘルプ Wiki](https://help.ubuntu.com/community/Tasksel) を参照してください。

Tasksel を使用して、LAMP スタックに必要なソフトウェアをインストールします。

- リポジトリからパッケージの一覧をダウンロードし、一覧を更新してパッケージの最新のバージョンとその依存関係を取得するには:  

		sudo apt-get update
-	Tasksel を使用して Ubuntu LAMP スタックをインストールするには:  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

次に、ウィザードを進めて **[MySQL root パスワード]** を選択します。

![][15]


##サーバーでの LAMP のテスト
簡単な php 情報ページを作成すると、LAMP システムをテストできます。

最初に新しいファイルを作成します。

	sudo nano /var/www/html/info.php  

次の行を追加します。

	<?php
	phpinfo();
	?>  

保存して終了します。

Apache を再起動して、すべての変更内容をコンピューター上で有効にします。仮想マシンの OS が CentOS の場合、次のコマンドを使用して Apache を再起動します。

	sudo service httpd restart

仮想マシンの OS が Ubuntu の場合、次のコマンドを使用して Apache を再起動します。

	sudo service apache2 restart  

最後に、php 情報ページを参照します (このトピックのサンプル Web サーバーの場合、URL は http://lampdemo.cloudapp.net/info.php) のようになります)。

ブラウザーには次のように表示されます。

![][16]

##追加の手順

一般的な手順として、Web アプリケーションのデプロイ準備のために、一部の既定の設定を変更することがあります。

###MySQL へのリモート アクセスの許可
MySQL を使用した複数の VM がインストールされており、その VM 同士でデータの交換が必要な場合は、MySQL リモート アクセスを有効にし、適切なアクセス許可を付与する必要があります。

**コマンドの参照の形式:**

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**例:**

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

/etc/mysql/my.cnf profile も変更します。次のような行がある場合:

	skip-networking
	bind-address = 127.0.0.1  

コメント アウトし (行の先頭に # を追加)、MySQL を再起動します。

リモート アクセスを許可するためのエンドポイントを追加するには、「フェーズ 1: イメージを作成する」 の手順を参照して、新しいエンドポイントを作成してください。MySQL の既定のリモート アクセス TCP ポート番号は 3306 です。たとえば次のようになります。

![][17]

###Apache サーバーへの Web アプリケーションのデプロイ
LAMP スタックを正しく設定したら、既存の Web アプリケーションを Apache Web サーバー (仮想マシン) にデプロイできます。デプロイの方法は、物理 Web サーバーに既存の Web アプリケーションをデプロイする方法と同じです。

-	Web サーバーの root は **/var/www/html** にあります。このフォルダーにファイルをアップロードするユーザーに権限を付与する必要があります。次の例は、書き込み権限を lampappgroup という名前のグループに追加し、このグループに azureuser のユーザー名を追加する方法を示しています。  

		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup

	>[AZURE.NOTE]
-	SFTP クライアント (FileZilla など) を使用して仮想マシンの DNS 名 (例: lampdemo.cloudapp.net) に接続し、/**var/www/html** に移動してサイトを公開します。![][18]



##一般的な問題とトラブルシューティング

###インターネットから、Apache と Moodle を使用している仮想マシンにアクセスできない

-	**症状: **Apache は実行されているが、Apache の既定のページがブラウザーに表示されない。
-	**考えられる根本原因**
	1.	Apache のリッスン ポートが、仮想マシンの Web トラフィック用のエンドポイントのプライベート ポートと異なっている。</br> パブリック ポートとプライベート ポートのエンドポイント設定をチェックし、プライベート ポートが Apache のリッスン ポートと同じになっていることを確認します。仮想マシンのエンドポイントを構成する手順については、「フェーズ 1: イメージを作成する」を参照してください。</br> Apache のリッスン ポートを確認するには、/etc/httpd/conf/httpd.conf (Red Hat リリース) または /etc/apache2/ports.conf (Debian リリース) を開き、「Listen」の文字列を検索します。既定のポートは 80 です。

	2.	ファイアウォールによって Apache のリッスン ポートが無効になっている。</br> ローカル ホストから Apache の既定のページを見ることができたら、多くの場合、Apache がリッスンしているポートがファイアウォールによってブロックされていることが問題になっていると考えられます。w3m ツールを使用すると、Web ページを参照できます。次のコマンドは w3m をインストールして、Apache の既定のページを参照します。

			sudo yum  install w3m w3m-img
			w3m http://localhost

-	**解決策:**

	1.	Apache のリッスン ポートが、仮想マシンの Web トラフィック用のエンドポイントのプライベート ポートと異なっている場合は、Apache のリッスン ポートと同じになるように、エンドポイントのプライベート ポートを変更する必要があります。
	2.	問題の原因がファイアウォールや iptables の場合は、/etc/sysconfig/iptables に次の行を追加します。  

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

		2 つ目の行は https トラフィックの場合にのみ必要です。

		次のように、グローバルにアクセスが制限されている行の上に配置します。

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		iptables を再読込するには、次のコマンドを実行します。

			service iptables restart  

		このテストは CentOS 6.3 で実行したものです。

###/var/www/html/ にプロジェクト ファイルをアップロードしようとすると、アクセスが拒否される  

-	**症状: **SFTP クライアント (FileZilla など) を使用して仮想マシンに接続し、/var/www/html に移動してサイトを公開しようとすると、次のようなエラー メッセージが表示される。  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**考えられる根本原因: **/var/www/html フォルダーへのアクセス許可が付与されていない。
-	**解決策:** root アカウントからアクセス許可を取得する必要があります。フォルダーの所有権を、root からマシンをプロビジョニングしたときに使用したユーザー名に変更できます。次は、azureuser のアカウント名の例です。  

		sudo chown azureuser -R /var/www/html  

	ディレクトリ内のすべてのファイルにアクセス許可を適用するには、-R オプションを使用します。

	このコマンドはディレクトリにも使用できます。-R オプションは、ディレクトリ内のすべてのファイルとディレクトリに対するアクセス許可を変更します。たとえば次のようになります。

		sudo chown -R username:group directory  

	このコマンドでは、ディレクトリ内のすべてのファイルとディレクトリ、さらにはディレクトリ自身の所有権 (ユーザーとグループの両方) が変更されます。

	次のコマンドでは、フォルダー ディレクトリのアクセス許可のみが変更され、ディレクトリ内のファイルやフォルダーのアクセス許可は変更されません。

		sudo chown username:group directory  

###サーバーの完全修飾ドメイン名を確実に判断できない

-	**症状: **次のいずれかのコマンドを使用して Apache サーバーを再起動した場合:  

		sudo /etc/init.d/apache2 restart  # Debian release  

	または

		sudo /etc/init.d/httpd restart   # Red Hat release  

	次のエラーが表示された場合:

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**考えられる根本原因: **Apache のサーバー名が設定されていない。

-	**解決策: **/etc/apache2 の httpd.conf (Red Hat リリース) または apache2.conf (Debian リリース) に “ServerName localhost” 行を挿入して Apache を再起動します。通知が表示されなくなります。



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg
 

<!---HONumber=July15_HO4-->