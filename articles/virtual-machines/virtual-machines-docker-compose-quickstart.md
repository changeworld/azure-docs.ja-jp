<properties
   pageTitle="仮想マシンでの Docker および Compose | Microsoft Azure"
   description="Azure Virtual Machines での Compose と Docker の操作を簡単に紹介します。"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/07/2015"
   ms.author="danlep"/>

# Azure 仮想マシンでの Docker および Compose の概要

この記事では、Docker と [Compose](http://github.com/docker/compose) を使用して、Azure の Linux 仮想マシンで複雑なアプリケーションを定義し、実行する方法について説明します。Compose (*Fig* の後継) では、単純なテキスト ファイルを使用して、複数の Docker コンテナーで構成されるアプリケーションを定義します。次に、VM 上でアプリケーションを実行するためのあらゆる操作を実行する単一のコマンドで、アプリケーションを起動します。たとえば、この記事では、バックエンド MariaDB SQL Database で WordPress ブログをすばやくセットアップする方法を示していますが、Compose を使用してさらに複雑なアプリケーションをセットアップすることもできます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事は、リソース マネージャーとクラシック デプロイメント モデルの両方を使用して仮想マシンを作成する場合に適用されます。

Docker とコンテナーの知識がない場合は、「[Docker high level whiteboard (Docker の概要ホワイトボード)](http://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/)」をご覧ください。

## 手順 1: Docker ホストとしての Linux VM のセットアップ

Azure のさまざまな手順と Azure Markeplace で入手できるイメージを使用して、Linux VM を作成し、Docker ホストとしてセットアップできます。たとえば、Docker VM 拡張機能を使用して Ubuntu VM を作成する簡単な手順については、「[Using the Docker VM Extension from the Azure Command-Line Interface (Azure コマンド ライン インターフェイスでの Docker VM 拡張機能の使用)](virtual-machines-docker-with-xplat-cli-install.md)」をご覧ください。Docker VM 拡張機能を使用すると、VM が自動的に Docker ホストとしてセットアップされます。前述の記事の例では、サービス管理モードで [Mac、Linux、Windows 用の Azure コマンド ライン インターフェイス](../xplat-cli-install.md) (Azure CLI) を使用して、VM を作成する方法を示しています。

## 手順 2: Compose のインストール

Docker がインストールされた Linux VM を実行したら、SSH を使用してクライアント コンピューターから VM に接続します。必要に応じて、次の 2 つのコマンドを実行して [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) をインストールします。

>[AZURE.TIP]Docker VM 拡張機能を使用して VM を作成した場合、Compose は既にインストールされています。これらのコマンドを省略して、手順 3. に進んでください。VM に Docker を自分でインストールした場合は、Compose をインストールするだけでかまいません。

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]"アクセスが拒否されました" という内容のエラーが発生した場合は、VM の /usr/local/bin ディレクトリが書き込み可能でない可能性があるため、Compose をスーパーユーザーとしてインストールする必要があります。`sudo -i` を実行してから、上記の 2 つのコマンドを実行し、`exit` を実行します。

Compose のインストールをテストするには、次のコマンドを実行します。

```
$ docker-compose --version
```

```
docker-compose 1.3.2
``` のような出力が表示されます。


## 手順3: docker-compose.yml 構成ファイルの作成

次に、`docker-compose.yml` というテキスト構成ファイルを作成して、VM 上で実行される Docker コンテナーを定義します。このファイルでは、各コンテナーで実行するイメージ (イメージは Dockerfile からのビルドも使用できます)、必要な環境変数と依存関係、ポート、コンテナー間のリンクなどを指定します。yml ファイルの構文の詳細については、[docker-compose.yml のリファレンス](http://docs.docker.com/compose/yml/)のページを参照してください。

VM 上に作業ディレクトリを作成し、任意のテキスト エディターを使用して `docker-compose.yml` を作成します。簡単な例を試すために、次のテキストをファイルにコピーしてください。この構成では、[DockerHub Registry](https://registry.hub.docker.com/_/wordpress/) から取得したイメージを使用して、WordPress (オープン ソースのブログ作成およびコンテンツ管理システム)、およびリンクされたバックエンド MariaDB SQL Database がインストールされます。

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## Step 4: Start the containers with Compose

In the working directory on your VM, simply run the following command.

```
$ docker-compose up -d

```

This starts the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress\_db\_1...
Creating wordpress\_wordpress\_1...
```

>[AZURE.NOTE]コンテナーがバックグラウンドで継続的に実行されるように、必ず起動時に **-d** オプションを使用してください。

コンテナーが起動していることを確認するために、「`docker-compose ps`」と入力します。次のような結果が表示されます。

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

`http://localhost:8080` を参照すると、VM 上の WordPress に直接接続できます。インターネット経由で VM に接続する場合は、まず、パブリック ポート 80 をプライベート ポート 8080 にマッピングする HTTP エンドポイントを VM 上に構成します。たとえば、Azure サービス管理デプロイメントで、次の Azure CLI コマンドを実行します。

```
$ azure vm endpoint create <machine-name> 80 8080

```

WordPress スタート画面が表示されます。この画面の手順に従ってインストールを完了し、アプリケーションを使用できます。

![WordPress のスタート画面][wordpress_start]


## 次のステップ

* 複数コンテナー アプリのビルドとデプロイのその他の例については、[Compose CLI リファレンス](http://docs.docker.com/compose/cli/)と[ユーザー ガイド](http://docs.docker.com/compose/)を参照してください。
* 自分で用意するか[コミュニティ](http://azure.microsoft.com/documentation/templates/)から取得した Azure リソース マネージャー テンプレートを利用して、Docker を搭載した Azure VM や Compose を搭載したアプリケーション セットアップをデプロイできます。たとえば、[Deploy a WordPress blog with Docker](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/) テンプレートは、Docker と Compose を使用して、Ubuntu VM に WordPress と MySQL バックエンドを迅速にデプロイします。
* Docker Compose と [Docker Swarm](virtual-machines-docker-swarm.md) クラスターを統合できます。このシナリオについては、
[Docker Compose/Swarm の統合](https://github.com/docker/compose/blob/master/SWARM.md)に関するページを参照してください。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=Oct15_HO2-->