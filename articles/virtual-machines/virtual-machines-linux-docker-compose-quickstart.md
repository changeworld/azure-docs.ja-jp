---
title: "Azure の Linux VM で Docker Compose を使用する | Microsoft Docs"
description: "Azure の Linux 仮想マシンで Docker と Compose を使用する方法"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 3295120664e409440641818b13dd1abab6f2f72f
ms.openlocfilehash: 06ad7f9267f24ee1f2fe417ad4aa0bf1096832d6


---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Docker と Compose を使用して Azure 仮想マシン上で複数コンテナー アプリケーションを定義して実行する
[Compose](http://github.com/docker/compose) では、単純なテキスト ファイルを使用して、複数の Docker コンテナーで構成されるアプリケーションを定義します。 次に、定義された環境をデプロイするためのあらゆる操作を実行する単一のコマンドで、アプリケーションを起動します。 たとえば、この記事では、Ubuntu VM のバックエンド MariaDB SQL Database で WordPress ブログをすばやくセットアップする方法を示します。 Compose を使用してさらに複雑なアプリケーションをセットアップすることもできます。

## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>手順 1: Docker ホストとしての Linux VM のセットアップ
Azure のさまざまな手順と Azure Marketplace で入手できるイメージまたは Resource Manager テンプレートを使用して、Linux VM を作成し、Docker ホストとしてセットアップできます。 たとえば、[クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)を使って Azure Docker VM 拡張機能で Ubuntu VM を簡単に作成する方法については、「[Docker VM 拡張機能を使用した環境のデプロイ](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

Docker VM 拡張機能を使用すると、VM が自動的に Docker ホストとしてセットアップされ、Compose が既にインストールされた状態になります。 前述の記事の例では、Resource Manager モードで [Azure CLI 1.0](../xplat-cli-install.md) を使用して VM を作成する方法を示しています。

上記のドキュメントの基本的なコマンドは、`myResourceGroup` という名前のリソース グループを場所 `West US` に作成し、Azure Docker VM 拡張機能がインストールされた VM をデプロイします。

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Azure CLI は数秒でプロンプトに戻りますが、Docker ホストの作成と構成はまだ行われています。 デプロイが完了するには数分かかります。 `azure vm show` コマンドを使うと、Docker ホストの詳細な状態を確認できます。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myDockerVM` という名前 (テンプレートによる既定の名前 - この名前は変更しないでください) の VM の状態を確認します。 前の手順で作成したリソース グループの名前を入力します。

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

出力の先頭付近に VM の `ProvisioningState` が表示されます。 ここで " `Succeeded`" と表示されている場合デプロイは完了しており、VM に SSH で接続できます。

## <a name="step-2-verify-that-compose-is-installed"></a>手順 2: Compose がインストールされていることを確認する
デプロイが完了したら、デプロイ時に指定した DNS 名を使用して、SSH で新しい Docker ホストに接続します。 `azure vm show -g myDockerResourceGroup -n myDockerVM` を使うと、DNS 名など、VM の詳細を見ることができます。

VM に Compose がインストールされていることを確認するには、次のコマンドを実行します。

```bash
docker-compose --version
```

すると、 `docker-compose 1.6.2, build 4d72027`のような結果が出力されます。

> [!TIP]
> 別の方法で作成した Docker ホストに Compose を自分でインストールする必要がある場合は、 [Compose のドキュメント](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)をご覧ください。


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>手順3: docker-compose.yml 構成ファイルの作成
次に、 `docker-compose.yml` というテキスト構成ファイルを作成して、VM 上で実行される Docker コンテナーを定義します。 このファイルでは、各コンテナーで実行するイメージ (イメージは Dockerfile からのビルドも使用できます)、必要な環境変数と依存関係、ポート、コンテナー間のリンクを指定します。 yml ファイルの構文の詳細については、 [Compose ファイルのリファレンス](http://docs.docker.com/compose/yml/)をご覧ください。

次のようにして `docker-compose.yml` ファイルを作成します。

```bash
touch docker-compose.yml
```

任意のテキスト エディターを使って何らかのデータをファイルに追加します。 次の例では、`vi` エディターを使います。

```bash
vi docker-compose.yml
```

次の例をテキスト ファイルに貼り付けます。 この構成では、 [DockerHub Registry](https://registry.hub.docker.com/_/wordpress/) から取得したイメージを使用して、WordPress (オープン ソースのブログ作成およびコンテンツ管理システム)、およびリンクされたバックエンド MariaDB SQL Database がインストールされます。 実際の `MYSQL_ROOT_PASSWORD` を次のように入力します。

```sh
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

## <a name="step-4-start-the-containers-with-compose"></a>手順 4: Compose によるコンテナーの起動
`docker-compose.yml` ファイルと同じディレクトリで、次のコマンドを実行します (環境によっては、`docker-compose` を実行するのに `sudo` が必要な場合があります)。

```bash
docker-compose up -d
```

このコマンドにより、 `docker-compose.yml`で指定された Docker コンテナーが起動されます。 この手順は、完了までに 1 ～ 2 分かかります。 次の例のような出力が表示されます。

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> コンテナーがバックグラウンドで継続的に実行されるように、必ず起動時に **-d** オプションを使用してください。


コンテナーが起動していることを確認するために、「 `docker-compose ps`」と入力します。 次のような結果が表示されます。

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

ポート80 で VM 上の WordPress に直接接続できます。 Web ブラウザーを開き、VM の DNS 名を入力します (例: `http://myresourcegroup.westus.cloudapp.azure.com`)。 WordPress スタート画面が表示されます。この画面の手順に従ってインストールを完了し、アプリケーションを使用できます。

![WordPress のスタート画面][wordpress_start]

## <a name="next-steps"></a>次のステップ
* Docker VM の Docker および Compose を構成するその他のオプションについては、 [Docker VM 拡張機能のユーザー ガイド](https://github.com/Azure/azure-docker-extension/blob/master/README.md) をご覧ください。 たとえば、Docker VM 拡張機能の構成に直接 Compose yml ファイル (JSON に変換) を配置する方法があります。
* 複数コンテナー アプリのビルドとデプロイに関するその他の例については、[Compose コマンド ラインのリファレンス](http://docs.docker.com/compose/reference/)および[ユーザー ガイド](http://docs.docker.com/compose/)をご覧ください。
* 自分で用意するか [コミュニティ](https://azure.microsoft.com/documentation/templates/)から取得した Azure リソース マネージャー テンプレートを利用して、Docker を搭載した Azure VM や Compose を搭載したアプリケーション セットアップをデプロイできます。 たとえば、 [Deploy a WordPress blog with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) テンプレートは、Docker と Compose を使用して、Ubuntu VM に WordPress と MySQL バックエンドを迅速にデプロイします。
* Docker Compose と [Docker Swarm](virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) クラスターを統合できます。 シナリオについては、「 [Using Compose with Swarm (Compose と Swarm の使用)](https://docs.docker.com/compose/swarm/) 」をご覧ください。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png



<!--HONumber=Dec16_HO3-->


