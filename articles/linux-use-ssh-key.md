<properties linkid="article" urlDisplayName="SSH の使用" pageTitle="SSH を使用した Windows Azure の Linux 仮想マシンへの接続" metaKeywords="Azure SSH キー Linux, Linux vm SSH" description="Windows Azure 上の Linux 仮想マシンで SSH キーを生成して使用する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Windows Azure 上の Linux における SSH の使用方法" authors=""  solutions="" writer="" manager="" editor=""  />





#Windows Azure 上の Linux における SSH の使用方法

このトピックでは、Windows Azure と互換性のある SSH キーを生成する手順について説明します。

Windows Azure の管理ポータルの現在のバージョンでは、X509 証明書にカプセル化された SSH 公開キーだけを受け付けます。以下に示す、Windows Azure で SSH キーを生成および使用する手順に従ってください。

## Linux 上で OpenSSL を入手する##
Linux マシンで openssl を入手するには、使っている Linux ディストリビューションでネイティブのパッケージ管理ソリューションを使用します。

*  Redhat/CentOS: `yum install openssl`
*  Debian: `apt-get install openssl`
*  Ubuntu: `apt-get install openssl`

## Linux で Windows Azure 互換のキーを生成する##

1. 'openssl' を使用して、2048 ビットの RSA 公開キーと秘密キーで X509 証明書を作成します。'openssl' からの質問に答えてください (回答しなくてもかまいません)。これらのフィールドの内容はプラットフォームでは使用されません。

			openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
2.	公開キーのアクセス許可を変更して安全を確保します。

			chmod 600 myPrivateKey.key
3.	Linux 仮想マシンの作成中に 'myCert.pem' をアップロードします。プロビジョニング中に、仮想マシン内の指定されたユーザーの 'authorized_keys' ファイルにこの証明書の公開キーが自動的にインストールされます。

4.	API を直接使用し、管理ポータルを使用しない場合は、次のコマンドを使用して 'myCert.pem' を 'myCert.cer' (DER エンコードされた X509 証明書) に変換します。

			openssl  x509 -outform der -in myCert.pem -out myCert.cer

## Linux から Windows Azure の仮想マシンに接続する##
どの Linux 仮想マシンも、特定のポートで SSH がプロビジョニングされており、それは標準ポートと異なる可能性があります。そのため、以下の手順を実行します。

1.	管理ポータルで、Linux 仮想マシンに接続するときに使用するポートを確認します。
2.	'ssh' を使用して Linux 仮想マシンに接続します。最初にログインすると、ホストの公開キーの指紋に同意するように求められます。

		ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net
3.	(省略可能) openssh クライアントが '-i' オプションを使用しないで自動的に 'myPrivateKey.key' を選択できるように、'myPrivateKey.key' を '~/.ssh/id_rsa' にコピーできます。

## Windows 上で OpenSSL を入手する##
### msysgit を使用する###

1.	次の場所から msysgit をダウンロードしてインストールします: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	インストールしたディレクトリ (例: c:\msysgit\msys.exe) から 'msys' を実行します。	「cd bin」と入力して、'bin' ディレクトリに移動します。

###Windows 用の GitHub を使用する###

1.	次の場所から Windows 用の GitHub をダウンロードしてインストールします: [http://windows.github.com/](http://windows.github.com/)
2.	[スタート] メニューをクリックし、[すべてのプログラム]、[GitHub] の順にクリックして、Git シェルを実行します。

###cygwin を使用する###

1.	次の場所から Cygwin をダウンロードしてインストールします: [http://cygwin.com/](http://cygwin.com/)
2.	OpenSSL パッケージとその依存パッケージがすべてインストールされていることを確認します。
3.	'cygwin' を実行します。

## Windows 上で秘密キーを作成する##

1.	上記の手順のどれかを実行して、'openssl.exe' を実行できるようにします。
2.	次のコマンドを入力します。

		openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	画面は次のようになります。

	![linuxwelcomegit](./media/linux-use-ssh-key/linuxwelcomegit.png)

4.	画面に表示されるメッセージに従って、設定します。
5.	ファイルが 2 つ作成されます ('myPrivateKey.key' および 'myCert.pem')。
6.	API を直接使用し、管理ポータルを使用しない場合は、次のコマンドを使用して 'myCert.pem' を 'myCert.cer' (DER エンコードされた X509 証明書) に変換します。

		openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Putty 用の PPK を作成する##

1.	次の場所から puttygen をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	'puttygen.exe' を実行します。
3.	[File] の [Load a Private Key] をクリックします。
4.	'myPrivateKey.key' という名前の秘密キー ファイルを見つけます。ファイル フィルターを **[All Files (\*.*)]** に変更する必要があります。
5.	**[開く]** をクリックします。次のような画面が表示されます。

	![linuxgoodforeignkey](./media/linux-use-ssh-key/linuxgoodforeignkey.png)

6.	**[OK]** をクリックします。
7.	下図でハイライト表示されている **[Save Private Key]** をクリックします。

	![linuxputtyprivatekey](./media/linux-use-ssh-key/linuxputtyprivatekey.png)

8.	ファイルを PPK として保存します。

## Putty を使用して Linux 仮想マシンに接続する##

1.	次の場所から putty をダウンロードしてインストールします: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	putty.exe を実行します。
3.	ホスト名として、管理ポータルの IP を入力します。

	![linuxputtyconfig](./media/linux-use-ssh-key/linuxputtyconfig.png)

4.	**[Open]** をクリックする前に、[Connection]、[SSH]、[Auth] タブの順にクリックして、自分のキーを選択します。入力するフィールドについては、下図を参照してください。

	![linuxputtyprivatekey](./media/linux-use-ssh-key/linuxputtyprivatekey.png)

5.	**[Open]** をクリックして、仮想マシンに接続します。





