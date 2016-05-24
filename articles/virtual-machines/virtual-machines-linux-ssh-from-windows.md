<properties 
	pageTitle="Windows 上での SSH を使用した Linux 仮想マシンへの接続 | Microsoft Azure" 
description="Windows コンピューターで SSH キーを生成して使用し、Azure 上の Linux 仮想マシンに接続する方法について説明します。" 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2016" 
	ms.author="rasquill"/>

#Azure 上の Windows における SSH の使用方法

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux と Mac](virtual-machines-linux-ssh-from-linux.md)

このトピックでは、**ssh** コマンドを使用して Azure 上の Linux VM に接続するために使用できる、**ssh-rsa** 形式と **.pem** 形式の公開キー ファイルおよび秘密キー ファイルを Windows 上で作成して使用する方法について説明します。**.pem** フィルが既に作成されている場合は、これらのファイルを使用して、**ssh** を使って接続できる Linux VM を作成することができます。他のいくつかのコマンドでは、**SSH** プロトコルおよびキー ファイルを使用して、作業を安全に実行することができます。特に **scp** ([Secure Copy](https://en.wikipedia.org/wiki/Secure_copy)) では、**SSH** 接続をサポートするコンピューターとの間でファイルを安全にコピーできます。


## 必要な SSH およびキー作成プログラム

**SSH** &#8212; ([secure shell](https://en.wikipedia.org/wiki/Secure_Shell) &#8212;) は、セキュリティで保護されていない接続においてセキュリティで保護されたログインを有効にする、暗号化された接続プロトコルです。これは、他の何らかの接続メカニズムを有効にするように Linux VM を構成しない限り、Azure でホストされる Linux VM の既定の接続プロトコルとなります。Windows ユーザーも、**ssh** クライアント実装を使用して Azure で Linux VM に接続して管理することができます。ただし、通常、Windows コンピューターには **ssh** クライアントが含まれないため、クライアントを選択する必要があります。

インストールできる一般的なクライアントは次のとおりです。

- [puTTY と puTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- [Git For Windows](https://git-for-windows.github.io/) (環境とツールに含まれる)

高度な設定を行う場合は、[Windows への **OpenSSH** ツールセットの新しいポート](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx)を試してみることもできます。ただし、このコードは現在開発途中であり、運用システムで使用する前にコードベースを確認する必要があります。

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 作成する必要があるキー ファイル

Azure の基本的な SSH 設定には、**ssh-rsa** の 2048 ビットの公開キー/秘密キーのペア (既定値を変更しない限り、**ssh-keygen** にはこれらのファイルが **~/.ssh/id\_rsa** および **~/.ssh/id-rsa.pub** として保存されます) と、クラシック ポータルのクラシック デプロイメント モデルで使用するために **id\_rsa** 秘密キー ファイルから生成された `.pem` ファイルが含まれます。

次にデプロイメント シナリオとそれぞれのシナリオで使用されるファイルの種類を紹介します。

1. **ssh-rsa** キーは、デプロイメント モデルに関係なく、[Azure ポータル](https://portal.azure.com)を使用したどのデプロイでも必須です。
2. .pem ファイルは[従来のポータル](https://manage.windowsazure.com)で VM を作成する際に必須です。.pem ファイルは、[Azure CLI](../xplat-cli-install.md) を使用する従来のデプロイメントでもサポートされます。

> [AZURE.NOTE] クラシック デプロイメント モデルでデプロイされたサービスを管理する場合、**.cer** 形式のファイルを作成してポータルにアップロードすることもできます。ただし、その場合、この記事のテーマである **ssh** も Linux VM への接続も使用しません。Windows でこれらのファイルを作成するには、「<br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer」と入力します。

## Windows 上で ssh-keygen および openssl を入手する ##

上記の[セクション](#What-SSH-and-key-creation-programs-do-you-need)では、Windows 用の `ssh-keygen` や `openssl` を含むいくつかのユーティリティを紹介しました。いくつかの例を以下に示します。

###Windows 用の GitHub を使用する###

1.	[https://git-for-windows.github.io/](https://git-for-windows.github.io/) から Windows 用の GitHub をダウンロードしてインストールします。
2.	[スタート] メニューをクリックし、[すべてのプログラム]、[GitHub] の順にクリックして、Git Bash を実行します。

> [AZURE.NOTE] 上記の `openssl` コマンドを実行すると、次のエラーが発生することがあります。

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

## 秘密キーを作成する##

1.	前の手順のどれかを実行して、`openssl.exe` を実行できるようにします。
2.	次のコマンドを入力します。

  ```
  openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  ```
3.	画面は次のようになります。

  ```
  $ openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  Generating a 2048 bit RSA private key
  .......................................+++
  .......................+++
  writing new private key to 'myPrivateKey.key'
  -----
  You are about to be asked to enter information that will be incorporated
  into your certificate request.
  What you are about to enter is what is called a Distinguished Name or a DN.
  There are quite a few fields but you can leave some blank
  For some fields there will be a default value,
  If you enter '.', the field will be left blank.
  -----
  Country Name (2 letter code) [AU]:
  ```

4.	画面に表示されるメッセージに従って、設定します。
5.	`myPrivateKey.key` および `myCert.pem` の 2 ファイルが作成されます。
6.	API を直接使用し、管理ポータルを使用しない場合は、次のコマンドを使用して `myCert.pem` を `myCert.cer` (DER エンコードされた X509 証明書) に変換します。

  ```
  openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
  ```

## Putty 用の PPK を作成する ##

1. 次の場所から puttygen をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen は、以前作成された秘密キー (`myPrivateKey.key`) を読み取ることができない可能性があります。それを Puttygen で認識できる RSA 秘密キーに変換するには、次のコマンドを使用してください。

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	このコマンドにより、myPrivateKey\_rsa という秘密キーが新たに生成されます。

3. `puttygen.exe` を実行します。

4. [File] の [Load a Private Key] をクリックします。

5. 先ほど生成した `myPrivateKey_rsa` という名前の秘密キー ファイルを見つけます。ファイル フィルターを **[All Files (*.*)]** に変更する必要があります。

6. **[開く]** をクリックします。次のような画面が表示されます。

	![linuxgoodforeignkey](./media/virtual-machines-linux-ssh-from-linux/linuxgoodforeignkey.png)

7. **[OK]** をクリックします。

8. 次の図でハイライト表示されている **[Save Private Key]** をクリックします。

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-linux/linuxputtygenprivatekey.png)

9. ファイルを PPK として保存します。


## Putty を使用して Linux 仮想マシンに接続する ##

1.	次の場所から putty をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	putty.exe を実行します。
3.	ホスト名として、管理ポータルの IP を入力します。

	![linuxputtyconfig](./media/virtual-machines-linux-ssh-from-linux/linuxputtyconfig.png)

4.	**[Open]** をクリックする前に、[Connection]、[SSH]、[Auth] タブの順にクリックして、自分のキーを選択します。入力するフィールドについては、下図を参照してください。

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-linux/linuxputtyprivatekey.png)

5.	**[Open]** をクリックして、仮想マシンに接続します。
 

<!---HONumber=AcomDC_0511_2016-->