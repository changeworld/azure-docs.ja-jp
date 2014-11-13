<properties urlDisplayName="Use SSH" pageTitle="SSH を使用した Azure の Linux 仮想マシンへの接続" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Azure 上の Linux 仮想マシンで SSH キーを生成して使用する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure 上の Linux における SSH の使用方法" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Azure 上の Linux における SSH の使用方法

このトピックでは、Azure と互換性のある SSH キーを生成する手順について説明します。

Azure 管理ポータルの現在のバージョンのみが、X509 証明書にカプセル化された SSH 公開キーを受領します。次に示している、Azure で SSH キーを生成および使用する手順に従ってください。

## Linux で Microsoft Azure 互換のキーを生成する

1.  必要に応じて `openssl` ユーティリティをインストールします。

    **CentOS/Oracle Linux**

        # sudo yum install openssl

    **Ubuntu**

        # sudo apt-get install openssl

    **SLES と openSUSE**

        # sudo zypper install openssl

2.  `openssl` を使用して、2048 ビットの RSA 公開キーと秘密キーで X509 証明書を作成します。`openssl` からの質問に答えてください (回答しなくてもかまいません)。これらのフィールドの内容はプラットフォームでは使用されません。

        # openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  公開キーのアクセス許可を変更して安全を確保します。

        # chmod 600 myPrivateKey.key

4.  Linux 仮想マシンの作成中に `myCert.pem` をアップロードします。プロビジョニング中に、仮想マシン内の指定されたユーザーに対応する `authorized_keys` ファイルにこの証明書の公開キーが自動的にインストールされます。

5.  API を直接使用し、管理ポータルを使用しない場合は、次のコマンドを使用して `myCert.pem` を `myCert.cer` (DER エンコードされた X509 証明書) に変換します。

        # openssl  x509 -outform der -in myCert.pem -out myCert.cer

## OpenSSH と互換性のある既存のキーからキーを生成する

前の例では、Microsoft Azure 用に新しいキーを作成する方法について説明しています。場合によっては、OpenSSH と互換性のある公開キーと秘密キーのペアが既にあり、Microsoft Azure 用にそれらの同じキーを使用する必要があります。

OpenSSH の秘密キーは `openssl` ユーティリティで直接読み取ることができます。次のコマンドは、SSH の既存の秘密キー (ここでは id\_rsa) を受け取り、Windows Azure に必要な `.pem` 公開キーを作成します。

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

**myCert.pem** ファイルは、Microsoft Azure 上の Linux 仮想マシンをプロビジョニングするために使用できる公開キーです。プロビジョニング中、`.pem` ファイルは `openssh` と互換性のある公開キーに翻訳され、`~/.ssh/authorized_keys` に配置されます。

## Linux から Windows Azure の仮想マシンに接続する

1.  Linux 仮想マシンの SSH エンドポイントは、既定のポート 22 以外のポートに構成されることがあります。適切なポート番号は、管理ポータルの VM のダッシュボードで確認できます ([SSH の詳細] に表示されます)。

2.  `ssh` を使用して Linux 仮想マシンに接続します。最初にログインすると、ホストの公開キーの指紋に同意するように求められます。

        # ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  (省略可能) OpenSSH クライアントが `-i` オプションを使用しないで自動的に `myPrivateKey.key` を選択できるように、このファイルを `~/.ssh/id_rsa` にコピーできます。

## Windows 上で OpenSSL を入手する

### msysgit を使用する

1.  次の場所から msysgit をダウンロードしてインストールします: [][]<http://msysgit.github.com/></a>
2.  インストールしたディレクトリ (c:\\msysgit\\msys.exe など) から `msys` を実行します。
3.  「`cd bin`」と入力し、`bin` ディレクトリに移動します。

### Windows 用の GitHub を使用する

1.  次の場所から Windows 用の GitHub をダウンロードしてインストールします: [][1]<http://windows.github.com/></a>
2.  [スタート] メニューをクリックし、[すべてのプログラム]、[GitHub] の順にクリックして、Git シェルを実行します。

### cygwin を使用する

1.  次の場所から Cygwin をダウンロードしてインストールします: [][2]<http://cygwin.com/></a>
2.  OpenSSL パッケージとその依存パッケージがすべてインストールされていることを確認します。
3.  `cygwin` を実行します。

## Windows 上で秘密キーを作成する

1.  前の手順のどれかを実行して、`openssl.exe` を実行できるようにします。
2.  次のコマンドを入力します。

        # openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  画面は次のようになります。

    ![linuxwelcomegit][linuxwelcomegit]

4.  画面に表示されるメッセージに従って、設定します。
5.  ファイルが 2 つ作成されます: `myPrivateKey.key` や `myCert.pem` となります。
6.  API を直接使用し、管理ポータルを使用しない場合は、次のコマンドを使用して `myCert.pem` を `myCert.cer` (DER エンコードされた X509 証明書) に変換します。

        # openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Putty 用の PPK を作成する

1.  次の場所から Puttygen をダウンロードしてインストールします: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>

2.  Puttygen は、以前作成された秘密キー (`myPrivateKey.key`) を読み取ることができない可能性があります。それを Puttygen で認識できる RSA 秘密キーに変換するには、次のコマンドを使用してください。

        # openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
        # chmod 600 ./myPrivateKey_rsa

    このコマンドにより、myPrivateKey\_rsa という秘密キーが新たに生成されます。

3.  `puttygen.exe` を実行します。

4.  [File] の [Load a Private Key] をクリックします。

5.  先ほど生成した `myPrivateKey_rsa` という名前の秘密キー ファイルを見つけます。ファイル フィルターを **[All Files (\*.\*)]** に変更する必要があります。

6.  **[開く]** をクリックします。次のような画面が表示されます。

    ![linuxgoodforeignkey][linuxgoodforeignkey]

7.  **[OK]** をクリックします。

8.  次の図でハイライト表示されている **[Save Private Key]** をクリックします。

    ![linuxputtyprivatekey][linuxputtyprivatekey]

9.  ファイルを PPK として保存します。

## Putty を使用して Linux 仮想マシンに接続する

1.  次の場所から putty をダウンロードしてインストールします: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  putty.exe を実行します。
3.  ホスト名として、管理ポータルの IP を入力します。

    ![linuxputtyconfig][linuxputtyconfig]

4.  **[Open]** をクリックする前に、[Connection]、[SSH]、[Auth] タブの順にクリックして、自分のキーを選択します。入力するフィールドについては、下図を参照してください。

    ![linuxputtyprivatekey][4]

5.  **[Open]** をクリックして、仮想マシンに接続します。

  []: http://msysgit.github.com/
  [1]: http://windows.github.com/
  [2]: http://cygwin.com/
  [linuxwelcomegit]: ./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png
  [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [linuxgoodforeignkey]: ./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png
  [linuxputtyprivatekey]: ./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png
  [linuxputtyconfig]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png
  [4]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png
