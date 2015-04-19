<properties 
	pageTitle="Azure での Linux 入門 - Azure チュートリアル" 
	description="Azure での Linux 仮想マシンの使用に関する詳細です。" 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/13/2014" 
	ms.author="szark"/>





#Azure での Linux 入門

このトピックでは、Azure クラウドで Linux 仮想マシンを使用するさまざまな局面について概説します。Linux 仮想マシンのデプロイは、ギャラリー内に存在するイメージを使用する際に簡単に使用できるプロセスです。 

## 目次 ##

* [認証:ユーザー名、パスワード、SSH 鍵](#authentication)
* [Linux 仮想マシンへのログイン用の SSH 鍵の生成と使用](#keygeneration)
* [sudo を使用したスーパー ユーザー権限の取得](#superuserprivileges)
* [ファイアウォールの構成](#firewallconfiguration)
* [ホスト名の変更](#hostnamechanges)
* [仮想マシン イメージのキャプチャ](#virtualmachine)
* [ディスクの接続](#attachingdisks)

## <a id="authentication"></a>認証:ユーザー名、パスワード、SSH 鍵

Azure の管理ポータルを使用して Linux 仮想マシンを作成すると、ユーザー名、パスワード、および SSH 公開キー (任意) の入力が求められます。Azure で Linux 仮想マシンをデプロイするユーザー名を選択する場合、 'root' など、既に仮想マシン内に存在するシステム アカウント (UID <100) の名前は許可されない、という制約があります。

 - 詳細については、「[Azure 上の Linux における SSH の使用方法](linux-use-ssh-key.md)」を参照してください。


### <a id="keygeneration"></a>SSH 鍵の生成

管理ポータルの現在のバージョンのみが、X509 証明書にカプセル化された SSH 公開キーを受領します。Azure で SSH キーを生成して使用する場合は、次の手順に従ってください。

1. openssl を使用して、2048 ビットの RSA 公開キーと秘密キーで X509 証明書を作成します。
		
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

	openssl からの質問に答えてください (空白のままにしてもかまいません)。これらのフィールドの内容はプラットフォームでは使用されません。

2. 公開キーのアクセス許可を変更して安全を確保します。

		chmod 600 myPrivateKey.key

3.  `myCert.pem` を  `myCert.cer` に変換します (DER 形式でエンコードされた x509 証明書)

		openssl  x509 -outform der -in myCert.pem -out myCert.cer

4. Linux 仮想マシンの作成中に  `myCert.cer` をアップロードします。プロビジョニング中に、仮想マシン内の指定されたユーザーに対応する `~/.ssh/authorized_keys` ファイルにこの証明書の公開キーが自動的にインストールされます。

5. ssh を使用して Linux 仮想マシンに接続します。

		ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

	最初にログインすると、ホストの公開キーの指紋に同意するように求められます。

6. openssh クライアントが -i オプションを使用しないで自動的に `myPrivateKey.key` を選択できるように、 `myPrivateKey.key` を `~/.ssh/id_rsa` にコピーできます。
   また、`~/.ssh/config` を変更して、仮想マシンのセクションを含めることもできます。

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key


### OpenSSH と互換性のある既存のキーからキーを生成する
前の例では、Microsoft Azure 用に新しいキーを作成する方法について説明しています。ユーザーによっては、OpenSSH と互換性のある公開キーと秘密キーのペアが既にあり、Microsoft Azure 用にそれらの同じキーを使用する場合があります。

OpenSSH の秘密キーは  `openssl` ユーティリティで直接読み取ることができます。次のコマンドは、SSH の既存の秘密キー (ここでは id_rsa) を受け取り、Windows Azure に必要な  `.pem` 公開キーを作成します。

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

**myCert.pem** ファイルは、Microsoft Azure 上の Linux 仮想マシンをプロビジョニングするために使用できる公開キーです。プロビジョニング中、 `.pem` ファイルは  `openssh` と互換性のある公開キーに翻訳され、`~/.ssh/authorized_keys` に配置されます。


## <a id="superuserprivileges"></a> `sudo` を使用したスーパーユーザー権限の取得

Azure での仮想マシン インスタンスをデプロイする際に指定したユーザー アカウントは、特権を持つアカウントです。このアカウントは、Azure Linux エージェントによって  `sudo` ユーティリティを使用して権限を root (スーパーユーザー アカウント) に昇格できるように構成されます。このユーザー アカウントを使用してログインすると、コマンド構文を使用して、root としてコマンドを実行できるようになります。

	# sudo <COMMAND>

**sudo -s** を使用して root シェルを取得することもできます。

- 詳細については、「[Azure 上の Linux 仮想マシンでの root 権限の使用](virtual-machines-linux-use-root-privileges.md)」を参照してください。


## <a id="firewallconfiguration"></a>ファイアウォールの構成

Azure では、管理ポータルで指定されたポートに接続を制限する受信パケット フィルターが用意されています。既定では、許可されている唯一のポートは SSH です。管理ポータルでエンドポイントを構成することで、Linux 仮想マシンの追加ポートへのアクセスを設定できます。

 - 参照:[仮想マシンに対してエンドポイントを設定する方法](virtual-machines-set-up-endpoints.md)

既定では、Azure ギャラリー内の Linux のイメージを使用して  *iptables* ファイアウォールを有効にすることはできません。必要があれば、このファイアウォールにフィルターを追加するように構成することはできます。


## <a id="hostnamechanges"></a>ホスト名の変更

最初に Linux イメージのインスタンスをデプロイするときに、仮想マシンのホスト名を提供するように求められます。仮想マシンが実行されると、このホスト名はプラットフォーム DNS サーバーに公開されます。相互に接続された複数の仮想マシンがホスト名を使用して IP アドレス検索を実行できるようになります。

仮想マシンがデプロイされた後でホスト名を変更する場合は、次のコマンドを使用します。

	# sudo hostname <newname>

Azure Linux エージェントには、この名前の変更を自動的に検出する機能、この変更を保持するように仮想マシンを正しく構成する機能、この変更をプラットフォーム DNS サーバーに公開する機能があります。

 - [Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md)

### Ubuntu イメージ
Ubuntu イメージは、cloud-init を使用できます。cloud-init の追加機能を利用すると、仮想マシンをブートストラップすることができます。

 - 詳細については、「[Custom Data and Cloud-Init on Microsoft Azure (Microsoft Azure のカスタム データと Cloud-Init)](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)」を参照してください。


## <a id="virtualmachine"></a>仮想マシン イメージのキャプチャ

Azure には、既存の仮想マシンの状態をイメージにキャプチャする機能があります。このイメージは、後で追加の仮想マシンのインスタンスをデプロイするために使用できます。Azure Linux エージェントは、プロビジョニング中に実行したカスタマイズをロールバックするために使用することもできます。仮想マシンをイメージとしてキャプチャする手順は次のとおりです。

1. **waagent -deprovision** を実行してプロビジョニング中のカスタマイズを元に戻します。または、**waagent -deprovision+user** を実行して、プロビジョニング中に指定したユーザー アカウントとすべての関連データを削除します。

2. 仮想マシンをシャットダウン/電源オフします。

3. 管理ポータルで  *Capture* をクリックするか、Powershell ツールまたは CLI ツールを使用して仮想マシンをイメージとしてキャプチャします。

 - 参照:[テンプレートとして使用するために Linux 仮想マシンをキャプチャする方法](virtual-machines-linux-capture-image.md)


## <a id="attachingdisks"></a>ディスクの接続

各仮想マシンには、一時的なローカル  *リソース ディスク*が接続されています。リソース ディスク上のデータには、再起動を行った場合の持続性がないため、多くの場合は仮想マシン上で実行されているアプリケーションとプロセスが、このディスクを過渡的および**一時的な**ストレージとして使用します。また、オペレーティング システムがページ ファイルやスワップ ファイルを格納する場合にも使用されます。

Linux では通常、リソース ディスクは Azure Linux エージェントによって管理され、**/mnt/resource** (または Ubuntu イメージでは **/mnt**) に自動的にマウントされます。

	>[AZURE.NOTE] リソース ディスクは**一時**ディスクであるため、仮想マシンが再起動されると削除され再フォーマットされることに注意してください。

Linux では、データ ディスクはカーネルによって  `/dev/sdc` という名前が付けられる場合があり、ユーザーはこのリソースをパーティション分割し、フォーマットした上で、マウントする必要があります。詳細な手順については、チュートリアル「[データ ディスクを仮想マシンに接続する方法](virtual-machines-linux-how-to-attach-disk.md)。

 - 関連項目:[Linux でのソフトウェア RAID の構成](virtual-machines-linux-configure-raid.md)



<!--HONumber=42-->
