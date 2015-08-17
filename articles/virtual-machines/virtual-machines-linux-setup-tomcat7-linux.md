<properties 
	pageTitle="Microsoft Azure で Linux 仮想マシンに Tomcat7 を設定する方法" 
	description="Linux を実行する Azure の仮想マシン (VM) を使用して Microsoft Azure で Tomcat7 を設定する方法について説明します。" 
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
	ms.date="05/21/2015" 
	ms.author="ningk"/>

#Microsoft Azure で Linux 仮想マシンに Tomcat7 を設定する方法 

Apache Tomcat (または単に Tomcat、以前は Jakarta Tomcat) は Apache Software Foundation (ASF) によって開発されたオープン ソース Web サーバーであり、サーブレット コンテナーです。Tomcat は、Sun Microsystems の Java Servlet および JavaServer Pages (JSP) 仕様を実装し、純粋な Java HTTP Web サーバー環境を提供して Java コードを実行します。最も単純な構成では Tomcat は単一のオペレーティング システムのプロセスで実行されます。このプロセスは、Java 仮想マシン (JVM) を実行します。ブラウザーからの Tomcat に対するすべての HTTP 要求は、Tomcat プロセスで個別のスレッドとして処理されます。

このガイドでは、Linux イメージ上に Tomcat7 をインストールして Microsoft Azure にデプロイすることができます。

学習内容:

-	Azure 上での仮想マシンの作成方法。
-	Tomcat7 の仮想マシンを準備する方法。
-	Tomcat7 をインストールする方法。

読者は既に Azure サブスクリプションを持っていることを前提にしています。持っていない場合は、[http://azure.microsoft.com](http://azure.microsoft.com) で無料試用版にサインアップしてください。MSDN サブスクリプションを持っている場合は、[Microsoft Azure 特別料金: MSDN、MPN、BizSpark の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)をご覧ください。Azure の詳細については、「[Azure とは](http://azure.microsoft.com/overview/what-is-azure/)」を参照してください。

このトピックは、Tomcat や Linux の基本的な知識を持つ読者を想定しています。

##フェーズ 1: イメージを作成する
このフェーズでは、Azure の Linux イメージを使用して仮想マシンを作成します。

###手順 1. SSH 認証キーを生成する
SSH はシステム管理者にとって重要なツールです。ただし、人間が指定したパスワードに基づいたアクセス セキュリティを構成することはベスト プラクティスではありません。悪意のあるユーザーは、ユーザー名と、脆弱なパスワードに基づいたシステムに侵入することができます。

リモート アクセスを開いたままにして、パスワードについて心配する必要のない方法があります。このメソッドは、非対称暗号化方式による認証で構成されています。ユーザーの秘密キーが認証を付与するキーとなります。ユーザーのアカウントをロックすれば、パスワード認証を完全に無効にできます。

このメソッドのもう 1 つの利点は、別のサーバーにログオンするための別のパスワードが必要ないことです。すべてのサーバーで個人の秘密キーを使用して認証できるため、パスワードをいくつか覚えておく必要がありません。

この方法でパスワードを必要とするログインも可能です。

次の手順に従って、SSH 認証のキーを生成します。

1.	次の場所から puttygen をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 
2.	PUTTYGEN.EXE を実行します。
3.	**[生成]** をクリックしてキーを生成します。処理中にウィンドウの空白の領域にマウスを移動すると、ランダム性を高めることができます。![][1]
4.	生成処理が終わると、生成されたキーが表示されます。次に例を示します。![][2]
5.	**[キー]** の公開キーを選択してコピーし、publicKey.pem という名前のファイルに保存します。保存された公開キーの形式は使用する公開キーの形式と異なるため、**[公開キーの保存]** をクリックしないでください。
6.	**[秘密キーの保存]** をクリックし、privateKey.ppk という名前のファイルに保存します。 

###手順 2. Azure プレビュー ポータルでイメージを作成する
[Azure プレビュー ポータル](https://portal.azure.com/)で、タスク バーの **[新規]** をクリックし、ニーズに合った Linux イメージを選択してイメージを作成します。次の例では、Ubuntu 14.04 イメージを使用します。![][3]
 
**[ホスト名]** に、ユーザーとインターネット クライアントがこの仮想マシンにアクセスするのに使用する URL の名前を指定します。DNS 名の最後の部分 (tomcatdemo など) を定義すると、Azure は URL を tomcatdemo.cloudapp.net として生成します。

**[SSH 認証キー]** に、**publicKey.pem** ファイルのキーの値をコピーします。このファイルには、puttygen によって生成された公開キーが含まれています。![][4]
  
必要に応じて、他の設定を構成し、[作成] をクリックします。

##フェーズ 2: Tomcat7 の仮想マシンを準備する
このフェーズでは、tomcat トラフィックのエンドポイントを構成し、新しい仮想マシンに接続します。
###手順 1. Web アクセスを許可する HTTP ポートを開く
Azure のエンドポイントはプロトコル (TCP または UDP) の他に、パブリック ポートとプライベート ポートで構成されます。プライベート ポートとは、サービスが仮想マシンをリッスンするポートです。パブリック ポートとは、Azure クラウド サービスがインターネット ベースのトラフィックを外部でリッスンするポートです。

TCP ポート 8080 は、tomcat がリッスンする既定のポート番号です。このポートを Azure エンドポイントで開くと、ユーザーやその他のインターネット クライアントが tomcat のページにアクセスできるようになります。

1.	Azure プレビュー ポータルで、**[参照]**、**[仮想マシン]** の順にクリックし、作成した仮想マシンをクリックします。![][5]
2.	仮想マシンにエンドポイントを追加するには、**[エンドポイント]** ボックスをクリックします。![][6] 
3.	**[追加]** をクリックします。  
	1.	**エンドポイント**では、[エンドポイント] にエンドポイントの名前を入力し、**[パブリック ポート]** に「80」を入力します。  
	  
		80 に設定している場合は、tomcat に接続するための URL にポート番号を含める必要はありません。たとえば、「http://tomcatdemo.cloudapp.net」のように入力します。

		81 などの別の値に設定した場合は、tomcat にアクセスするための URL にポート番号を追加する必要があります。たとえば、「http://tomcatdemo.cloudapp.net:81/」のように入力します。
	2.	プライベート ポートに「8080」を入力します。既定では、tomcat は TCP ポート 8080 でリッスンします。tomcat の既定のリッスン ポートを変更した場合は、tomcat のリッスン ポートと同じになるように [プライベート ポート] を更新する必要があります。![][7]
 
4.	**[OK]** をクリックして、仮想マシンにエンドポイントを追加します。



###手順 2. 作成したイメージに接続する
SSH ツールを選択すると、仮想マシンに接続できます。この例では、Putty を使用します。

最初に、Azure プレビュー ポータルから仮想マシンの DNS 名を取得します。**[参照]**、**[仮想マシン]**、仮想マシン名、**[プロパティ]** の順にクリックし、**[プロパティ]** タイルの**ドメイン名**を調べます。

**[SSH]** フィールドから、SSH 接続のポート番号を取得します。たとえば次のようになります。![][8]
 
[ここ](http://www.putty.org/)から Putty をダウンロードします

ダウンロード後、実行可能ファイル PUTTY.EXE をクリックします。仮想マシンのプロパティから取得したホスト名とポート番号を使用して、基本オプションを構成します。たとえば次のようになります。![][9]
 
左側のウィンドウで、**[接続]**、**[SSH]**、**[認証]** の順にクリックし、**[参照]** をクリックして **privateKey.ppk** ファイルの場所を指定します。このファイルには、「フェーズ 1: イメージを作成する」で puttygen によって生成された秘密キーが含まれています。たとえば次のようになります。![][10]
 
**[開く]** をクリックします。メッセージ ボックスによるアラートが表示される場合があります。DNS 名とポート番号を正しく構成してある場合は、**[はい]** をクリックします。![][11]


次のように表示されます。![][12]

「フェーズ 1: イメージを作成する」で仮想マシンを作成したときに指定したユーザー名を入力します。次のように表示されます。![][13]





##フェーズ 3: ソフトウェアをインストールする
このフェーズでは、Java ランタイム環境、tomcat、およびその他の tomcat のコンポーネントをインストールします。

###Java ランタイム環境
Tomcat は Java で記述します。Java 開発キットには 2 種類 (JDK と、OpenJDK および Oracle JDK) があり、必要なものを選択できます。

>AZURE.NOTE: どちらの JDK も Java API のクラスにほぼ同じコードを持っていますが、仮想マシンのコードは実際には異なります。ライブラリに関しては、Oracle が閉じているライブラリを使用する傾向がある一方で、OpenJDK は開いているライブラリを使用する傾向があります。Oracle JDK はより多くのクラスを持ち、いくつかのバグを修正していて、OpenJDK よりも安定しています。

次のコマンドで、さまざまな JDK をダウンロードします。

open-jdk

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk

-	JDK を Oracle の Web サイトからダウンロードするには:  

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	JDK のファイルを格納するディレクトリを作成するには:

		sudo mkdir /usr/lib/jvm  

-	JDK のファイルを /usr/lib/jvm/ ディレクトリに抽出するには:

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	Oracle JDK を既定の JVM として設定するには:

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####テスト:
次のようなコマンドを使用して Java ランタイム環境が正しくインストールされているかどうかをテストすることができます。

	java -version  

open-jdk をインストールした場合は、次のようなメッセージが表示されます。![][14]

oracle-jdk をインストールした場合は、次のようなメッセージが表示されます。![][15]

###Tomcat7
次のコマンドを使用して Tomcat7 をインストールします。

	sudo apt-get install tomcat7  

Tomcat7 を使用していない場合は、このコマンドの適切なバリエーションを使用します。

####テスト:

Tomcat7 が正常にインストールされたかどうかを確認するには、Tomcat サーバーの DNS 名を参照します (この記事のサンプル URL の場合は、http://tomcatexample.cloudapp.net/)。次のようなページが表示された場合は、Tomcat7 が正常にインストールされています。![][16]


###Tomcat のその他のコンポーネントをインストールします。
Tomcat のその他のコンポーネントをインストールすることもできます。

**sudo apt-cache search tomcat7** コマンドを使用して、使用可能なすべてのコンポーネントを参照します。次のコマンドは、役立ついくつかのパーツをインストールする例です。

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##フェーズ 4: Tomcat を構成する
このフェーズでは、tomcat を管理します。
###Tomcat7 の開始と停止
Tomcat7 サーバーはインストールすると、自動的に開始されます。次のコマンドでも開始できます。

	sudo /etc/init.d/tomcat7 start

Tomcat7 を停止するには:

	sudo /etc/init.d/tomcat7 stop 

Tomcat7 の状態を表示するには:

	sudo /etc/init.d/tomcat7 status

Tomcat サービスを再起動するには:

	sudo /etc/init.d/tomcat7 restart

###Tomcat 管理
次のコマンドで、Tomcat ユーザー構成ファイルを編集して管理者の資格情報を設定することができます。

	sudo vi  /etc/tomcat7/tomcat-users.xml   

たとえば次のようになります。![][17]

>AZURE.NOTE: admin ユーザー名の強力なパスワードを作成してください。

このファイルの編集後に、次のコマンドで Tomcat7 サービスを再起動し、変更が有効になっていることを確認する必要があります。

	sudo /etc/init.d/tomcat7 restart  

ブラウザーを開き、「**http://<your tomcat server DNS name>/manager/html**」という URL を入力します。たとえば、この記事では、URL は http://tomcatexample.cloudapp.net/manager/html です。

接続後は、次のように表示されます。![][18]
 
##一般的な問題

###Tomcat や Moodle を使用してインターネットから仮想マシンにアクセスできない。

-	**症状:** Tomcat は実行されているが、ブラウザーに Tomcat の既定のページが表示されない。
-	**考えられる根本原因**   
	1.	tomcat のリッスン ポートが、仮想マシンの tomcat トラフィック用のエンドポイントのプライベート ポートと異なっている。  
	
		パブリック ポートとプライベート ポートのエンドポイント設定を確認し、プライベート ポートが tomcat のリッスン ポートと同じになっていることを確認します。仮想マシンのエンドポイントを構成する手順については、「フェーズ 1: イメージを作成する」を参照してください。

		tomcat のリッスン ポートを確認するには、/etc/httpd/conf/httpd.conf (Red Hat リリース) または /etc/tomcat7/server.xml (Debian リリース) を開きます。既定では、tomcat のリッスン ポートは 8080 です。たとえば次のようになります。

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		Debian または Ubuntu のような仮想マシンを使用していて、Tomcat リッスンの既定のポート (8081 など) を変更する場合は、OS のポートも開く必要があります。最初に、プロファイルを開きます。

			sudo vi /etc/default/tomcat7  

		最後の行のコメントを解除し、"no" を "yes" に変更します。

			AUTHBIND=yes

	2.	ファイアウォールによって tomcat のリッスン ポートが無効になっている。

		ローカル ホストから Tomcat の既定のページを見ることができたら、多くの場合、Tomcat がリッスンしているポートがファイアウォールによってブロックされていることが問題になっていると考えられます。w3m ツールを使用すると、Web ページを参照できます。次のコマンドは w3m をインストールして、Tomcat の既定のページを参照します。

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**解決策:**
	1. tomcat のリッスン ポートが、仮想マシンのトラフィック用のエンドポイントのプライベート ポートと異なっている場合は、tomcat のリッスン ポートと同じになるように、プライベート ポートを変更する必要があります。   
	
	2.	問題の原因がファイアウォールや iptables の場合は、/etc/sysconfig/iptables に次の行を追加します。
	
			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		2 つ目の行は https トラフィックの場合にのみ必要です。

		次のように、グローバルにアクセスが制限されている行の上に配置します。

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		iptables を再読込するには、次のコマンドを実行します。

			service iptables restart  

		このテストは CentOS 6.3 で実行したものです。

###/var/lib/tomcat7/webapps/ にプロジェクト ファイルをアップロードしようとすると、アクセスが拒否される  

-	**症状:** SFTP クライアント (FileZilla など) を使用して仮想マシンに接続し、/var/lib/tomcat7/webapps/ に移動してサイトを公開しようとすると、次のようなエラー メッセージが表示される。  

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**考えられる根本原因:** /var/lib/tomcat7/webapps フォルダーへのアクセス許可が付与されていない。
-	**解決策:** root アカウントからアクセス許可を取得する必要があります。フォルダーの所有権を、root からマシンをプロビジョニングしたときに使用したユーザー名に変更できます。次は、azureuser のアカウント名の例です。  

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	ディレクトリ内のすべてのファイルにアクセス許可を適用するには、-R オプションを使用します。

	このコマンドはディレクトリにも使用できます。-R オプションは、ディレクトリ内のすべてのファイルとディレクトリに対するアクセス許可を変更します。たとえば次のようになります。

		sudo chown -R username:group directory  

	このコマンドでは、ディレクトリ内のすべてのファイルとディレクトリ、さらにはディレクトリ自身の所有権 (ユーザーとグループの両方) が変更されます。

	次のコマンドでは、フォルダー ディレクトリのアクセス許可のみが変更され、ディレクトリ内のファイルやフォルダーのアクセス許可は変更されません。

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-18.png
 

<!---HONumber=August15_HO6-->