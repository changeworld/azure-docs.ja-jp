<properties
   pageTitle="仮想マシンでの Docker および Compose | Microsoft Azure"
   description="Azure の Linux 仮想マシンでの Compose と Docker の操作を簡単に紹介します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="danlep"/>

# Docker と Compose を使用して Azure 仮想マシン上で複数コンテナー アプリケーションを定義して実行する

Docker と [Compose](http://github.com/docker/compose) を使用して、Azure の Linux 仮想マシンで複雑なアプリケーションを定義し、実行します。Compose (*Fig* の後継) では、単純なテキスト ファイルを使用して、複数の Docker コンテナーで構成されるアプリケーションを定義します。次に、VM 上でアプリケーションを実行するためのあらゆる操作を実行する単一のコマンドで、アプリケーションを起動します。

たとえば、この記事では、Ubuntu VM のバックエンド MariaDB SQL Database で WordPress ブログをすばやくセットアップする方法を示していますが、Compose を使用してさらに複雑なアプリケーションをセットアップすることもできます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager モデルを使用してこれらの手順を実行する](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql)方法について説明します。

Docker とコンテナーの知識がない場合は、「[Docker high level whiteboard (Docker の概要ホワイトボード)](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/)」をご覧ください。

## 手順 1: Docker ホストとしての Linux VM のセットアップ

Azure のさまざまな手順と Azure Markeplace で入手できるイメージまたは Resource Manager テンプレートを使用して、Linux VM を作成し、Docker ホストとしてセットアップできます。たとえば、[クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)を使用して Azure Docker VM 拡張機能で Ubuntu VM を作成する簡単な手順については、「[Docker VM 拡張機能を使用した環境のデプロイ](virtual-machines-linux-dockerextension.md)」をご覧ください。Docker VM 拡張機能を使用すると、VM が自動的に Docker ホストとしてセットアップされ、Compose が既にインストールされた状態になります。前述の記事の例では、Resource Manager モードで [Mac、Linux、Windows 用の Azure コマンド ライン インターフェイス](../xplat-cli-install.md) (Azure CLI) を使用して、VM を作成する方法を示しています。

## 手順 2: Compose がインストールされていることを確認する

Docker がインストールされた Linux VM を実行したら、SSH を使用してクライアント コンピューターから VM に接続します。

VM で Compose のインストールをテストするには、次のコマンドを実行します。

```
$ docker-compose --version
```

`docker-compose 1.6.2, build 4d72027` のような出力が表示されます。

>[AZURE.TIP] 別の方法で作成した Docker ホストに Compose を自分でインストールする必要がある場合は、[Compose のドキュメント](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)をご覧ください。


## 手順3: docker-compose.yml 構成ファイルの作成

次に、`docker-compose.yml` というテキスト構成ファイルを作成して、VM 上で実行される Docker コンテナーを定義します。このファイルでは、各コンテナーで実行するイメージ (イメージは Dockerfile からのビルドも使用できます)、必要な環境変数と依存関係、ポート、コンテナー間のリンクなどを指定します。yml ファイルの構文の詳細については、[Compose ファイルのリファレンス](http://docs.docker.com/compose/yml/)をご覧ください。

VM 上に作業ディレクトリを作成し、任意のテキスト エディターを使用して `docker-compose.yml` を作成します。概念実証の簡単な例を試すために、次のテキストをファイルにコピーしてください。この構成では、[DockerHub Registry](https://registry.hub.docker.com/_/wordpress/) から取得したイメージを使用して、WordPress (オープン ソースのブログ作成およびコンテンツ管理システム)、およびリンクされたバックエンド MariaDB SQL Database がインストールされます。

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## 手順 4: Compose によるコンテナーの起動

VM の作業ディレクトリで、次のコマンドを実行するだけです。(環境によっては、`sudo` を使用して `docker-compose` を実行しなければならない場合があります)

```
$ docker-compose up -d

```

これにより、`docker-compose.yml` で指定された Docker コンテナーが起動されます。次のような出力が表示されます。

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] コンテナーがバックグラウンドで継続的に実行されるように、必ず起動時に **-d** オプションを使用してください。

コンテナーが起動していることを確認するために、「`docker-compose ps`」と入力します。次のような結果が表示されます。

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

ポート80 で VM 上の WordPress に直接接続できます。Resource Manager テンプレートを使用して VM を作成した場合は、`http://<dnsname>.<region>.cloudapp.azure.com` への接続を試みます。クラシック デプロイメント モデルを使用して VM を作成した場合は、`http://<cloudservicename>.cloudapp.net` への接続を試みます。WordPress スタート画面が表示されます。この画面の手順に従ってインストールを完了し、アプリケーションを使用できます。

![WordPress のスタート画面][wordpress_start]


## 次のステップ

* Docker VM の Docker および Compose を構成するその他のオプションについては、[Docker VM 拡張機能のユーザー ガイド](https://github.com/Azure/azure-docker-extension/blob/master/README.md)をご覧ください。たとえば、Docker VM 拡張機能の構成に直接 Compose yml ファイル (JSON に変換) を配置する方法があります。
* 複数コンテナー アプリのビルドとデプロイに関するその他の例については、[Compose コマンド ラインのリファレンス](http://docs.docker.com/compose/reference/)および[ユーザー ガイド](http://docs.docker.com/compose/)をご覧ください。
* 自分で用意するか[コミュニティ](https://azure.microsoft.com/documentation/templates/)から取得した Azure リソース マネージャー テンプレートを利用して、Docker を搭載した Azure VM や Compose を搭載したアプリケーション セットアップをデプロイできます。たとえば、[Deploy a WordPress blog with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) テンプレートは、Docker と Compose を使用して、Ubuntu VM に WordPress と MySQL バックエンドを迅速にデプロイします。
* Docker Compose と [Docker Swarm](virtual-machines-linux-docker-swarm.md) クラスターを統合できます。シナリオについては、「[Using Compose with Swarm (Compose と Swarm の使用)](https://docs.docker.com/compose/swarm/)」をご覧ください。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0629_2016-->