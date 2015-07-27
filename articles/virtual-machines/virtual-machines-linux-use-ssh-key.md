<properties 
	pageTitle="SSH を使用した Azure の Linux 仮想マシンへの接続" 
	description="Azure 上の Linux 仮想マシンで SSH キーを生成して使用する方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szark"/>

#Azure 上の Linux における SSH の使用方法

Azure の管理ポータルの現在のバージョンのみが、X509 証明書にカプセル化された SSH 公開キーを受領します。次に示している、Azure で SSH キーを生成および使用する手順に従ってください。

## Linux で Azure 互換のキーを生成する ##

1. 必要に応じて `openssl` ユーティリティをインストールします。

	**CentOS/Oracle Linux**

		# sudo yum install openssl

	**Ubuntu**

		# sudo apt-get install openssl

	**SLES と openSUSE**

		# sudo zypper install openssl


2. `openssl` を使用して、2048 ビットの RSA 公開キーと秘密キーで X509 証明書を作成します。`openssl` からの質問に答えてください (回答しなくてもかまいません)。これらのフィールドの内容はプラットフォームでは使用されません。

		# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	公開キーのアクセス許可を変更して安全を確保します。

		# chmod 600 myPrivateKey.key

4.	Linux 仮想マシンの作成中に `myCert.pem` をアップロードします。プロビジョニング中に、仮想マシン内の指定されたユーザーに対応する `authorized_keys` ファイルにこの証明書の公開キーが自動的にインストールされます。

5.	API を直接使用し、管理ポータルを使用しない場合は、次のコマンドを使用して `myCert.pem` を `myCert.cer` (DER エンコードされた X509 証明書) に変換します。

		# openssl  x509 -outform der -in myCert.pem -out myCert.cer


## OpenSSH と互換性のある既存のキーからキーを生成する
前の例では、Azure 用に新しいキーを作成する方法について説明しています。場合によっては、OpenSSH と互換性のある公開キーと秘密キーのペアが既にあり、Azure でこれらのキーを使用する必要がある可能性もあります。

OpenSSH の秘密キーは `openssl` ユーティリティで直接読み取ることができます。次のコマンドは、SSH の既存の秘密キー (ここでは id_rsa) を受け取り、Azure に必要な `.pem` 公開キーを作成します。

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

**myCert.pem** ファイルは、Azure 上の Linux 仮想マシンをプロビジョニングするために使用できる公開キーです。プロビジョニング中、`.pem` ファイルは `openssh` と互換性のある公開キーに変換され、`~/.ssh/authorized_keys` に配置されます。


## Linux から Azure の仮想マシンに接続する

1. Linux 仮想マシンの SSH エンドポイントは、既定の 22 以外のポートに構成されることがあります。管理ポータルの VM のダッシュボードで ([SSH の詳細] で) 正しいポート番号を検索できます。

2.	`ssh` を使用して Linux 仮想マシンに接続します。最初にログインすると、ホストの公開キーの指紋に同意するように求められます。

		# ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.	(省略可能) OpenSSH クライアントが `-i` オプションを使用しないで自動的に `myPrivateKey.key` を選択できるように、このファイルを `~/.ssh/id_rsa` にコピーできます。

## Windows 上で OpenSSL を入手する ##

Windows 用 `openssl` を含むさまざまなユーティリティがあります。いくつかの例を以下に示します。

### msysgit を使用する ###

1.	次の場所から msysgit をダウンロードしてインストールします: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	インストールしたディレクトリ (c:\\msysgit\\msys.exe など) から `msys` を実行します。
3.	「`cd bin`」と入力し、`bin` ディレクトリに移動します。


### Windows 用の GitHub を使用する ###

1.	次の場所から Windows 用の GitHub をダウンロードしてインストールします: [http://windows.github.com/](http://windows.github.com/)
2.	[スタート] メニューをクリックし、[すべてのプログラム]、[GitHub] の順にクリックして、Git シェルを実行します。

	**注:** 上記の `openssl` コマンドを実行している場合は、次のエラーが発生する可能性があります。

		Unable to load config info from /usr/local/ssl/openssl.cnf

	この問題を最も簡単に解決する方法として、`OPENSSL_CONF` 環境変数を設定します。この変数を設定するプロセスは、Github で構成されているシェルによって異なります。

	**PowerShell**

		$Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

	**CMD:**

		set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

	**Git Bash:**

		export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf


###Cygwin を使用する###

1.	次の場所から Cygwin をダウンロードしてインストールします: [http://cygwin.com/](http://cygwin.com/)
2.	OpenSSL パッケージとその依存パッケージがすべてインストールされていることを確認します。
3.	`cygwin` を実行します。


## Windows 上で秘密キーを作成する ##

1.	前の手順のどれかを実行して、`openssl.exe` を実行できるようにします。
2.	次のコマンドを入力します。

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	画面は次のようになります。

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	画面に表示されるメッセージに従って、設定します。
5.	`myPrivateKey.key` および `myCert.pem` の 2 ファイルが作成されます。
6.	API を直接使用し、管理ポータルを使用しない場合は、次のコマンドを使用して `myCert.pem` を `myCert.cer` (DER エンコードされた X509 証明書) に変換します。

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Putty 用の PPK を作成する ##

1. 次の場所から puttygen をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen は、以前作成された秘密キー (`myPrivateKey.key`) を読み取ることができない可能性があります。それを Puttygen で認識できる RSA 秘密キーに変換するには、次のコマンドを使用してください。

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	このコマンドにより、myPrivateKey_rsa という秘密キーが新たに生成されます。

3. `puttygen.exe` を実行します。

4. [File] の [Load a Private Key] をクリックします。

5. 先ほど生成した `myPrivateKey_rsa` という名前の秘密キー ファイルを見つけます。ファイル フィルターを **[All Files (*.*)]** に変更する必要があります。

6. **[開く]** をクリックします。次のような画面が表示されます。

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. **[OK]** をクリックします。

8. 次の図でハイライト表示されている **[Save Private Key]** をクリックします。

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. ファイルを PPK として保存します。


## Putty を使用して Linux 仮想マシンに接続する ##

1.	次の場所から putty をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	putty.exe を実行します。
3.	ホスト名として、管理ポータルの IP を入力します。

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	**[Open]** をクリックする前に、[Connection]、[SSH]、[Auth] タブの順にクリックして、自分のキーを選択します。入力するフィールドについては、下図を参照してください。

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	**[Open]** をクリックして、仮想マシンに接続します。
 

<!---HONumber=July15_HO3-->