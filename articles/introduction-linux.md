<properties linkid="manage-linux-fundamentals-intro-to-linux" urlDisplayName="Linux 入門" pageTitle="Azure での Linux 入門 - Azure チュートリアル" metaKeywords="Azure Linux vm, Linux vm" description="Azure での仮想マシンの使用に関する詳細です。" metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Azure での Linux 入門" authors=""  solutions="" writer="" manager="" editor=""  />





#Azure での Linux 入門

このトピックでは、Azure クラウドで Linux 仮想マシンを使用するさまざまな局面について概説します。Linux 仮想マシンのデプロイは、ギャラリー内に既に存在するイメージを使用する際に簡単に使用できるプロセスです。

## 目次##

* [認証: ユーザー名、パスワード、SSH 鍵。](#authentication)
* [Linux 仮想マシンへのログイン用の SSH 鍵の生成と使用。](#keygeneration)
* [sudo を使用したスーパーユーザー権限の取得](#superuserprivileges)
* [ファイアウォールの構成](#firewallconfiguration)
* [ホスト名の変更](#hostnamechanges)
* [仮想マシン イメージのキャプチャ](#virtualmachine)
* [ディスクの接続](#attachingdisks)

<h2><a id="authentication"></a>認証: ユーザー名、パスワード、SSH 鍵。</h2>

Azure の管理ポータルを使用して Linux 仮想マシンを作成すると、ユーザー名、パスワード、および (任意で) SSH 公開キーの入力が求められます。Azure で Linux 仮想マシンをデプロイするユーザー名を選択する場合、既に仮想マシン内に存在するシステム アカウントの名前 (たとえば root) は許可されない、という制約があります。SSH 公開キーを設定しない場合は、指定したユーザー名とパスワードを使用して仮想マシンにログインできます。管理ポータルで SSH 公開キーをアップロードすることを選択した場合は、パスワード ベースの認証は無効となり、対応する SSH 秘密キーを使用して仮想マシンで認証を行い、ログインする必要があります。

<h2><a id="keygeneration"></a>SSH 鍵の生成</h2>

管理ポータルの現在のバージョンのみが、X509 証明書にカプセル化された SSH 公開キーを受領します。以下に示す、Azure で SSH 鍵を生成および使用する手順に従ってください。

1. openssl を使用して、2048 ビットの RSA 公開キーと秘密キーで X509 証明書を作成します。
		
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

	openssl からの質問に答えてください (空白のままにしてもかまいません)。これらのフィールドの内容はプラットフォームでは使用されません。

2. 公開キーのアクセス許可を変更して安全を確保します。

		chmod 600 myPrivateKey.key

3. myCert.pem を myCert.cer に変換します (DER 形式でエンコードされた x509 証明書)

		openssl  x509 -outform der -in myCert.pem -out myCert.cer

4. Linux 仮想マシンの作成中に myCert.cer をアップロードします。プロビジョニング中に、仮想マシン内の指定されたユーザーに対応する authorized_keys ファイルにこの証明書の公開キーが自動的にインストールされます。

5. ssh を使用して Linux 仮想マシンに接続します。

		ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

	最初にログインすると、ホストの公開キーの指紋に同意するように求められます。

6. openssh クライアントが -i オプションを使用しないで自動的に myPrivateKey.key を選択できるように、myPrivateKey.key を ~/.ssh/id_rsa にコピーできます。

<h2><a id="superuserprivileges"></a>sudo を使用したスーパーユーザー権限の取得</h2>

Azure での仮想マシン インスタンス デプロイ中に指定されたユーザー アカウントは、特権を持つアカウントです。このアカウントは、Azure Linux エージェントにより、sudo ツールを使用して権限を root (スーパーユーザー アカウント) に昇格できるように構成されています。このユーザー アカウントを使用してログインしたら、sudo コマンドを使用して、root としてコマンドを実行できます。**sudo -s** を使用して root シェルを取得することもできます。

<h2><a id="firewallconfiguration"></a>ファイアウォールの構成</h2>

Azure では、接続を管理ポータルで指定されたポートに制限する受信パケット フィルターを提供します。既定では、許可されている唯一のポートは SSH です。管理ポータルでルールを追加することで、Linux 仮想マシンの追加ポートへのアクセスを設定できます。

ギャラリー内の Linux イメージを使用して、Linux 仮想マシン内部で iptables ファイアウォールを有効にすることはできません。有効にしたい場合は、追加の機能を提供するように iptables ファイアウォールを構成できます。

<h2><a id="hostnamechanges"></a>ホスト名の変更</h2>

最初に Linux イメージのインスタンスをデプロイするときに、仮想マシンのホスト名を提供するように求められます。仮想マシンが実行されると、このホスト名はプラットフォーム DNS サーバーに公開されます。相互に接続された複数の仮想マシンがホスト名を使用して IP アドレス検索を実行できるようになります。仮想マシンがデプロイされた後でホスト名を変更する場合は、**hostname newname** コマンドを使用してください。Azure Linux エージェントには、この名前の変更を自動的に検出する機能、この変更を保持するように仮想マシンを構成する機能、この変更をプラットフォーム DNS サーバーに公開する機能があります。この機能に関する詳細と構成オプションについては、Azure Linux エージェントの README ファイルを参照してください。

<h2><a id="virtualmachine"></a>仮想マシン イメージのキャプチャ</h2>

Azure には、既存の仮想マシンの状態をイメージにキャプチャする機能があります。イメージは、後で追加の仮想マシンのインスタンスをデプロイするために使用できます。Azure Linux エージェントを使用して、プロビジョニング中に実行したカスタマイズをロールバックできます。仮想マシンをイメージとしてキャプチャする手順は次のとおりです。

1. **waagent -deprovision** を実行してプロビジョニング中のカスタマイズを元に戻します。または、**waagent -deprovision+user** を実行して、プロビジョニング中に指定したユーザー アカウントとすべての関連データを削除します。

2. 管理ポータル/ツールを使用して仮想マシンをシャットダウン/電源オフします。

3. 管理ポータルで [キャプチャ] をクリックするか、ツールを使用して仮想マシンをイメージとしてキャプチャします。

<h2><a id="attachingdisks"></a>ディスクの接続</h2>

ディスクの接続については、[Linux 仮想マシンを作成する](../virtual-machine-from-gallery/)チュートリアルで詳しく説明しています。


