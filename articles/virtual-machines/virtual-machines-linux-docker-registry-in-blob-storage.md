<properties 
  pageTitle="Azure への独自のプライベート Docker Registry のデプロイ | Microsoft Azure"
  description="Docker Registry を使用して Azure BLOB ストレージ サービスでコンテナー イメージをホストする方法について説明します。"
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="" 
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="02/01/2016" 
  ms.author="ahmetb" />

# Azure への独自のプライベート Docker Registry のデプロイ

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



このドキュメントでは、Docker プライベート レジストリの概要、および Azure BLOB ストレージを使用して Microsoft Azure の Docker プライベート レジストリに Docker Registry 2.0 コンテナー イメージをデプロイする方法について説明します。

このドキュメントでは以下のことが想定されています。

1. 読者は Docker の使用方法を理解しており、格納する Docker イメージを持っている。(そうでない場合は、 [Docker について学習](https://www.docker.com)してください)
2. Docker エンジンがインストールされているサーバーがある。(そうでない場合は、 [Azure ですぐに行って](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/)ください。)


## プライベート Docker レジストリとは

コンテナー化されたアプリケーションをクラウドに配布するには、Docker コンテナー イメージを作成し、自分または他のユーザーが使用できるようにどこかに格納します。

コンテナー イメージの作成およびクラウドへの配布は簡単ですが、生成されたイメージを確実に格納することは簡単ではありません。そのため、Docker では、クラウドにコンテナー イメージを格納するための [Docker Hub][docker-hub] と呼ばれる集中サービスが提供されており、これらのイメージを使用していつでもコンテナーを作成できます。

[Docker Hub][docker-hub] はプライベート アプリケーション コンテナー イメージを格納するための有料サービスですが、開発者のニーズを尊重し、パブリック インターネットに接続することなくファイアウォールまたはオンプレミスの背後の独自のプライベート Docker Registry にイメージを格納するためのオープンソース ツールセットが提供されています。Azure BLOB ストレージは簡単にセキュリティで保護できるので、それを使用して自分で管理するプライベート Docker Registry を Azure に簡単に作成して使用できます。

## Azure で Docker Registry をホストする必要がある理由

Docker Registry インスタンスを Microsoft Azure でホストし、イメージを Azure BLOB ストレージに格納すると、いくつかの利点があります。

**セキュリティ:** Docker イメージは Azure データセンターを離れないので、Docker Hub を使用する場合とは異なり、パブリック インターネットを移動しません。
  
**パフォーマンス:** Docker イメージは、アプリケーションと同じデータセンターまたはリージョンに格納されます。これは、Docker Hub と比較して、速く信頼性が高いことを意味します。

**信頼性:** Microsoft Azure BLOB ストレージを使用することにより、高可用性、冗長性、Premium Storage (SSD) などの多くのストレージ プロパティを使用できます。

## Azure BLOB ストレージを使用するための Docker Registry の構成

(先に進む前に、[Docker Registry 2.0 のドキュメント][registry-docs]を読むことをお勧めします。)

Docker Registry を[構成][registry-config]するには 2 つの方法があります。そのための方法は次のとおりです。

1. `config.yml` ファイルを使用します。この場合は、`registry` イメージの上に別の Docker イメージを作成する必要があります。
2. 環境変数によって既定の構成ファイルを上書きします。Docker イメージを別に作成して保持する必要はありません。

わかりやすくするため、このトピックでは環境変数を使用する 2 番目のオプションを使用します。

次に該当する Docker Registry インスタンスを実行するには:
* イメージを保存するために Azure ストレージ アカウントを使用する、
* 仮想マシンのポート 5000 をリッスンする、
* 認証が構成されていない (推奨しません。下記のメモを参照してください)

bash 端末で次のような Docker コマンドを実行する必要があります (`<storage-account>` および `<storage-key>` を実際の資格情報に置き換えます)。

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

コマンドが終了した後は、ホストで `docker ps` コマンドを実行することにより、プライベート Docker Registry インスタンスをホストしているコンテナーを確認できます。

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] Docker Registry へのセキュリティの構成については、このドキュメントでは説明しません。前記のデプロイ コマンドを使用した場合、仮想マシン エンドポイントまたはロード バランサーでレジストリ ポートへのポートを開くと、誰でも既定で認証することなくレジストリにアクセスできます。
>
> レジストリ インスタンスとイメージを保護する方法については、[Docker Registry の構成][registry-config]に関するドキュメントを参照してください。

## 次のステップ

レジストリを設定したら、使用できます。Docker [registry-docs] で始めてください。

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

<!---HONumber=AcomDC_0323_2016-->