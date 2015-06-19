<properties
   pageTitle="Azure 仮想マシンでの Docker および Compose の概要"
   description="Azure での Compose と Docker の操作の簡単な紹介"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# Azure 仮想マシンでの Docker および Compose の概要


[Compose](http://github.com/docker/compose) (*Fig* の後継品) を使用して、Azure 上の Linux 仮想マシンで Docker を使用した複雑なアプリケーションの定義と実行を簡単に始めることができます。Compose では、複数のコンテナーで構成されるアプリケーションを単一のファイルで定義し、アプリケーションを VM 上で実行するために必要なすべての操作を単一のコマンドで行います。




## Docker VM 拡張機能を搭載した Azure VM を作成し、Compose をインストールする

Azure Markeplace には、Azure VM を作成し、VM に Docker と Compose をインストールするための Azure 用の手順と利用可能なイメージが豊富に用意されています。たとえば、Mac、Linux、および Windows 向けの Azure コマンド ライン インターフェイス (Azure CLI) を使用して、Docker VM 拡張機能がインストールされた Ubuntu VM を作成する簡単な手順については、「[Azure コマンド ライン インターフェイスでの Docker VM 拡張機能の使用](virtual-machines-docker-with-xplat-cli)」を参照してください。この記事内の例では、CLI をサービス管理 (**asm**) モードで使用します。


Docker がインストールされた Ubuntu VM を起動した後、SSH を使用して VM に接続し、次の 2 つのコマンドを実行して [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) をインストールします。

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]"アクセスが拒否されました" という内容のエラーが発生した場合は、/usr/local/bin ディレクトリが書き込み可能でない可能性があるため、Compose をスーパーユーザーとしてインストールする必要があります。`sudo -i` を実行してから、上記の 2 つのコマンドを実行し、`exit` を実行します。

Compose のインストールをテストするために、次のコマンドを実行します。

```
docker-compose --version
```

```
docker-compose 1.2.0
``` のような出力が表示されます。


## docker-compose.yml 構成ファイルを作成する

Compose では、`docker-compose.yml` というテキスト構成ファイルを使用して、VM 上で実行されるコンテナーを定義します。このファイルでは、各コンテナーで実行するイメージ (イメージは Dockerfile からのビルドも使用できます)、必要な環境変数と依存関係、ポート、コンテナー間のリンクなどを指定します。yml ファイルの構文の詳細については、[docker-compose.yml のリファレンス](http://docs.docker.com/compose/yml/)のページを参照してください。

VM 上に作業ディレクトリを作成し、任意のテキスト エディターを使用して `docker-compose.yml` を作成します。簡単な例を試すために、次のテキストをファイルにコピーしてください。この構成では、[DockerHub Registry](https://registry.hub.docker.com/_/wordpress/) から取得したイメージを使用して、WordPress (オープン ソースのブログ作成およびコンテンツ管理システム)、およびリンクされたバックエンド MariaDB SQL Database がインストールされます。

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1...Creating wordpress_wordpress_1... ```

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

`http://localhost:8080` を参照すると、VM 上の WordPress に直接接続できます。インターネット経由で VM に接続する場合は、まず、パブリック ポート 80 をプライベート ポート 8080 にマッピングする HTTP エンドポイントを VM 上に構成します。たとえば、次の Azure CLI コマンドを実行します。

```
azure vm endpoint create <machine-name> 80 8080

```

WordPress スタート画面が表示され、手順に従ってインストールを完了できます。

![WordPress のスタート画面][wordpress_start]




## 次のステップ

* 複数コンテナー アプリのビルドとデプロイに関するその他の例については、[Compose コマンドのリファレンス](http://docs.docker.com/compose/cli/)および[ユーザー ガイド](http://docs.docker.com/compose/)を参照してください。
* Docker Compose と [Docker Swarm](virtual-machines-docker-swarm.md) クラスターを統合できます。このシナリオについては、[Docker Compose/Swarm の統合](https://github.com/docker/compose/blob/master/SWARM.md)に関するページを参照してください。
* 自分で用意するか[コミュニティ](http://azure.microsoft.com/documentation/templates/)から取得した Azure リソース マネージャー テンプレートを利用して、Docker を搭載した Azure VM や Compose を搭載したアプリケーション セットアップをデプロイできます。たとえば、[Docker がインストールされた Ubuntu VM および 3 つのコンテナー用のテンプレート](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/)を使用すると、[DockerHub Registry](https://registry.hub.docker.com/) で提供されているイメージから 3 つのサービスを迅速にデプロイできます。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=58--> 