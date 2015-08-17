<properties
	pageTitle="CoreOS を使用する方法 |Microsoft Azure"
	description="CoreOS、Azure 上で CoreOS 仮想マシン クラスターを作成する方法、その基本的な使用方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="08/03/2015"
	ms.author="rasquill"/>

# Azure 上で CoreOS を使用する方法

このトピックでは、このオペレーティング システムを理解するためのクイック スタートとして [CoreOS] について説明し、Azure 上に 3 台の CoreOS 仮想マシンで構成されるクラスターを作成する方法を示します。ここでは、CoreOS デプロイメントのごく基本的な要素と、[CoreOS と Azure に関するページ]、[Tim Park による CoreOS のチュートリアル]、および [Patrick Chanezon によるチュートリアル]の例を使用して、CoreOS デプロイメントの基本構造を理解し、3 台の仮想マシンから構成されるクラスターを正常に実行するための極限まで抑えた最小要件について説明します。

>[AZURE.NOTE]この記事では、Azure コマンド ライン インターフェイスとサービス管理のコマンドを使用して、CoreOS の仮想マシンを作成する方法を示します。Azure リソース マネージャーで CoreOS を開始するには、この[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/)を試してください。

## <a id='intro'>CoreOS、クラスター、Linux コンテナー</a>

CoreOS は、Linux の軽量バージョンです。唯一のパッケージ化のメカニズムとして [Docker] コンテナーなどの Linux コンテナーを使用する、きわめて大規模になる可能性がある VM のクラスターの迅速な作成をサポートします。CoreOS は、以下をサポートすることを目的としています。

+ きわめて高度な自動化
+ より容易で一貫性のあるアプリケーションのデプロイメント
+ アプリケーション レベルおよびシステム レベルでの拡張性

大まかに言えば、以下の CoreOS の機能がこれらの目的を支援します。

1. 1 つのパッケージ システム: CoreOS では、スピード、均一性、およびデプロイメントのしやすさを実現するため、Linux コンテナーで実行される Linux コンテナー イメージのみを実行します。
2. アトミックに実行されるオペレーティング システムの更新。そのため、オペレーティング システムは単一のエンティティとして更新され、容易に既知の状態にロールバックできます。
3. 動的 VM およびクラスター通信と管理用の組み込みの [etcd](https://github.com/coreos/etcd) および [fleet](https://github.com/coreos/fleet) デーモン (サービス)

以上は、CoreOS とその機能の一般的な説明です。CoreOS の詳細については、「[CoreOS Overview]」を参照してください。

## <a id='security'>セキュリティに関する考慮事項</a>
現在、CoreOS では、SSH でクラスターと通信できるユーザーには、そのクラスターを管理するためのアクセス許可が与えられていることを前提としています。そのため、CoreOS クラスターは、変更しなくてもテスト環境および開発環境として優れています。ただし、運用環境では、より強力なセキュリティ対策を実施する必要があります。

## <a id='usingcoreos'>方法: Azure での CoreOS の使用</a>

このセクションでは、[Azure コマンドライン インターフェイス (Azure CLI)] を使用して、3 台の CoreOS 仮想マシンを含む Azure Cloud Service を作成する方法について説明します。基本的な手順は次のとおりです。

1. SSH 証明書とキーを作成し、CoreOS 仮想マシンとのコミュニケーションを保護する
2. 相互に通信するために、使用するクラスターの etcd ID を取得する
3. cloud-config ファイルを [YAML] 形式で作成する
4. Azure CLI を使用して、新しい Azure Cloud Service と 3 台の CoreOS VM を作成する
5. Azure VM で CoreOS クラスターをテストする
6. localhost で CoreOS クラスターをテストする

### 通信用に公開キーと秘密キーを作成する

「[Azure 上の Linux における SSH の使用方法](virtual-machines-linux-use-ssh-key.md)」の指示に従って SSH 用の公開キーと秘密キーのペアを作成します(基本的な手順は、以下の指示に記載されています)。 これらのキーを使用してクラスターの VM に接続し、VM が動作中で互いに通信できることを確認します。

> [AZURE.NOTE]このトピックでは、わかりやすくするために、これらのキーがなく、`myPrivateKey.pem` ファイルおよび `myCert.pem` ファイルを作成する必要があることを前提にしています。既に公開キーと秘密キーのペアを `~/.ssh/id_rsa` に保存している場合は、「`openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem`」と入力するだけで、Azure にアップロードする必要のある .pem ファイルを取得できます。

1. 作業ディレクトリで、「`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem`」と入力し、秘密キーと秘密キーに関連付けられた X.509 証明書を作成します。

2. 秘密キーの所有者がファイルの読み取りや書き込みをできるかどうかをアサートするには、「`chmod 600 myPrivateKey.key`」と入力します。

これで、作業ディレクトリに、`myPrivateKey.key` と `myCert.pem` ファイルの両方が作成されます。


### 使用するクラスターの etcd ID を取得する

CoreOS の `etcd` デーモンでは、クラスターのすべてのノードを自動的に照会するのに検出 ID が必要です。検出 ID を取得して、`etcdid` ファイルに保存するには、次を入力します。

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### cloud-config ファイルの作成

引き続き同じ作業ディレクトリで、任意のテキスト エディターを使用して次のテキストを含むファイルを作成し、`cloud-config.yaml` として保存します。(任意のファイル名で保存できますが、次の手順で VM を作成するときに、**azure vm create** コマンドの **--custom-data** オプションでこのファイル名を参照する必要があります)。

> [AZURE.NOTE]「`cat etcdid`」と入力して、上で作成した `etcdid` ファイルから etcd 検出 ID を取得し、次の `cloud-config.yaml` ファイルの `<token>` を、`etcdid`ファイルから生成された番号で置き換えます。最後にクラスターを検証できない場合は、この手順を行っていない可能性があります。

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # deployments across multiple cloud services will need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

cloud-config ファイルの詳細については、CoreOS ドキュメントの「[Using Cloud-Config](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/)」を参照してください。

### Azure CLI を使用して、新しい CoreOS VM を作成する
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. [Azure コマンドライン インターフェイス (Azure CLI)] をまだインストールしていない場合はインストールし、仕事や学校の ID を使用してログインするか、publishsettings ファイルをダウンロードして、アカウントにインポートします。
2. CoreOS イメージを探します。「`azure vm image list | grep CoreOS`」と入力するといつでも使用可能なイメージを探すことができ、次のような結果が一覧表示されます。

	データ: 2b171e93f07c4903bcad35bda10acf22\_\_CoreOS-Stable-522.6.0 Public Linux

3. 「`azure service create <cloud-service-name>`」と入力し、基本的なクラスター用にクラウド サービスを作成します。ここで、<*cloud-service-name*> には、CoreOS クラウド サービスの名前を指定します。このサンプルでは、**`coreos-cluster`** という名前を使用します。この名前を再利用して、クラウド サービス内に CoreOS VM インスタンスを作成する場合に選択する必要があります。

	注意: [プレビュー ポータル](https://portal.azure.com)でここまでの作業を確認すると、次の画像で示すように、クラウド サービスの名前がリソース グループとドメインの両方になっていることがわかります。

	![][CloudServiceInNewPortal]

4. クラウド サービスに接続し、**azure vm create** コマンドを使用して、新しい CoreOS VM インスタンスを作成します。**--ssh-cert** オプションで X.509 証明書の場所を渡します。次を入力して、最初の VM イメージを作成し、次のように必ず **coreos-cluster** を作成したクラウド サービスの名前に置き換えます。

	```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location="West US" 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 core
```

5. 手順 4. のコマンドを繰り返し、2 番目のノードを作成します。その際、**--vm-name** 値は **node-2** に、**--ssh** ポートの値は 2022 に置き換えます。

6. 手順 4. のコマンドを繰り返し、3 番目のノードを作成します。その際、**--vm-name** 値は **node-3** に、**--ssh** ポートの値は 3022 に置き換えます。

次の画像では、ポータルに CoreOS クラスターが表示されている状態を示します。

![][EmptyCoreOSCluster]

### Azure VM で CoreOS クラスターをテストする

クラスターをテストするには、作業ディレクトリで次を入力し、**ssh** により **node-1** に接続して、秘密キーを渡します。

	ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

接続されたら、「`sudo fleetctl list-machines`」と入力して、既にクラスター内のすべての VM がクラスターにより識別されたかどうかを確認します。次のような応答を受け取ります。


	core@node-1 ~ $ sudo fleetctl list-machines
	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-


### localhost で CoreOS クラスターをテストする

最後に、ローカル Linux クライアントで CoreOS クラスターをテストします。**npm** を使用して **fleetctl** をインストールするか、ローカル クライアントに **fleet** をインストールして **fleetctl** を自身でビルドすることもできます。 **fleet** には **golang** が必要なため、次を入力してまずそれをインストールする必要があります。

`sudo apt-get install golang`

次を入力して、github から **fleet** リポジトリを複製します。

`git clone https://github.com/coreos/fleet.git`

`fleet` ディレクトリに変更し、次を入力して **fleet** をビルドします。

`./build`

最後に、次のように **fleet** を使いやすいように配置します (**sudo** 使用する必要があるかどうかは、構成によって変わります)。

`cp bin/fleetctl /usr/local/bin`

次を入力して、作業ディレクトリで **fleet** が `myPrivateKey.key` にアクセスできることを確認します。

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE]`~/.ssh/id_rsa` キーを既に使用している場合は、`ssh-add ~/.ssh/id_rsa` を使用して追加します。

これで、**node-1** で使用したのと同じ **fleetctl** コマンドを使用してリモートでテストできます。ただし、以下のようにリモート引数をいくつか渡します。

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

以下とまったく同じ結果が得られます。


	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-

## 次のステップ

これで、3 ノード構成の CoreOS クラスターが Azure 上で実行されています。ここから、より複雑なクラスターを作成する方法、Docker を使用する方法、さらにより有益なアプリケーションを作成する方法を調べることができます。いくつかの簡単な例については、「[Azure 上の CoreOS で fleet を使ってみる]」を参照してください。

<!--Anchors-->
[CoreOS, Clusters, and Linux Containers]: #intro
[Security Considerations]: #security
[How to use CoreOS on Azure]: #usingcoreos
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Azure コマンドライン インターフェイス (Azure CLI)]: ../xplat-cli.md
[CoreOS]: https://coreos.com/
[CoreOS Overview]: https://coreos.com/using-coreos/
[CoreOS と Azure に関するページ]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park による CoreOS のチュートリアル]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon によるチュートリアル]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Azure 上の CoreOS で fleet を使ってみる]: virtual-machines-linux-coreos-fleet-get-started.md

<!---HONumber=August15_HO6-->