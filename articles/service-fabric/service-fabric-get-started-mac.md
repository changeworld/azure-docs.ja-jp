---
title: "Azure Service Fabric を使用するための開発環境を Mac OS X にセットアップする | Microsoft Docs"
description: "ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。 このセットアップを完了すると、Mac OS X でアプリケーションを構築する準備が整います。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 309fcb901a1a3878edbfbe06e12122615b74664e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Mac OS X で開発環境をセットアップする
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux クラスターで実行される Service Fabric アプリケーションを Mac OS X を使用して構築できます。このドキュメントでは、開発用に Mac をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件
Service Fabric は、OS X ではネイティブに実行されません。ローカルの Service Fabric クラスターを実行できるように、事前に構成済みの Docker コンテナー イメージが用意されています。 作業を開始する前に、以下を行う必要があります。

* 少なくとも 4 GB の RAM
* 最新バージョンの [Docker](https://www.docker.com/)
* Service Fabric One-box Docker コンテナー [イメージ](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/)へのアクセス

>[!TIP]
> * Docker の公式[ドキュメント](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install)に記載されている手順に従って Mac に Docker をインストールしてください。 
> * インストールが完了したら、[ここ](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)に記載されている手順に従って、Docker が正しくインストールされているかどうかを確認します。


## <a name="create-a-local-container-and-setup-service-fabric"></a>ローカル コンテナーを作成し、Service Fabric をセットアップする
ローカル Docker コンテナーを設定し、そこでサービス ファブリック クラスターを実行するには、次の手順を実行します。

1. Docker ハブ リポジトリからイメージをプルします。

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. ホスト上の Docker デーモン構成を次のように更新し、Docker デーモンを再起動します。 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Docker インストール パスの daemon.json でこれを直接更新することができます (場所はマシンごとに異なる場合があります。たとえば、~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json)。 Docker アイコン、[Preferences]\(設定\)、[Daemon]\(デーモン\)、[Advanced]\(詳細設定\) の順に移動し、そこで更新することをお勧めします。

3. イメージを使用して Service Fabric One-box コンテナー インスタンスを起動します。

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * コンテナー インスタンスの名前を指定すると、読みやすい方法で処理することができます。 
    > * アプリケーションが所定のポートでリッスンしている場合は、追加の -p タグを使用して指定する必要があります。 たとえば、アプリケーションがポート 8080 でリッスンしている場合は、docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox を実行します

4. 対話型 ssh モードで Docker コンテナーにログインします。

    ```bash
    docker exec -it sfonebox bash
    ```

5. セットアップ スクリプトを実行します。必要な依存関係がフェッチされ、その後、コンテナーのクラスターが起動されます。

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. 手順 5. が正常に完了したら、Mac から ``http://localhost:19080`` にアクセスします。Service Fabric Explorer が表示されます。

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Mac に Service Fabric CLI (sfctl) をセットアップする

[Service Fabric CLI](service-fabric-cli.md#cli-mac) に関するページの手順に従って、Mac に Service Fabric CLI (`sfctl`) をインストールしてください。
この CLI には、クラスター、アプリケーション、サービスなどの Service Fabric エンティティを操作するコマンドが含まれています。

## <a name="create-application-on-your-mac-using-yeoman"></a>Mac 上で Yeoman を使ってアプリケーションを作成する

Service Fabric には、ターミナルから Yeoman テンプレート ジェネレーターを使って Service Fabric アプリケーションを作成するのに役立つスキャフォールディング ツールが用意されています。 以下の手順に従って、ご利用のマシンに Service Fabric Yeoman テンプレート ジェネレーターをセットアップしてください。

1. ご使用の Mac に Node.js と NPM がインストールされている必要があります。 インストールされていない場合は、次の手順に従って、Homebrew を使って Node.js と NPM をインストールしてください。

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. NPM からマシンに [Yeoman](http://yeoman.io/) テンプレート ジェネレーターをインストールします。

    ```bash
    npm install -g yo
    ```
3. 概要[ドキュメント](service-fabric-get-started-linux.md)の手順に従って、使用する Yeoman ジェネレーターをインストールします。 Yeoman を使って Service Fabric アプリケーションを作成するには、次の手順に従います。

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Service Fabric Java アプリケーションを Mac で構築するには、JDK 1.8 と Gradle がホスト マシンにインストールされている必要があります。 まだインストールしていない場合は、[Homebrew](https://brew.sh/) を使用してインストールできます。 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>ターミナルから Mac にアプリケーションをデプロイする

Service Fabric アプリケーションを作成して構築したら、次の手順に従って、[Service Fabric CLI](service-fabric-cli.md#cli-mac) を使用してアプリケーションをデプロイできます。

1. Mac のコンテナー インスタンス内で実行されている Service Fabric クラスターに接続します。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. プロジェクト ディレクトリ内に移動し、インストール スクリプトを実行します。

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 開発環境をセットアップする

[.NET Core 2.0 SDK for Mac](https://www.microsoft.com/net/core#macos) をインストールして、[C# Service Fabric アプリケーションの作成](service-fabric-create-your-first-linux-application-with-csharp.md)を開始します。 .NET Core 2.0 Service Fabric アプリケーション用のパッケージは、現在プレビューの段階で NuGet.org でホストされています。

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Eclipse Neon 用の Service Fabric プラグインを Mac にインストールする

Service Fabric には、Java サービスの作成、構築、デプロイのプロセスを簡略化できる、**Java IDE 用の Eclipse Neon** 向けのプラグインが用意されています。 Service Fabric Eclipse プラグインのインストールまたは最新バージョンへの更新については、概要の[ドキュメント](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)に記載されたインストール手順を利用できます。

[Service Fabric Eclipse のドキュメント](service-fabric-get-started-eclipse.md)に記載されている、アプリケーションの構築、アプリケーションへのサービスの追加、アプリケーションのインストール/アンインストールなどの他のすべての手順もここに適用できます。

上記の手順とは別に、Service Fabric Eclipse プラグインを Mac 上の Docker コンテナーで動作させるには、次のように、ホストとパスを共有するコンテナーをインスタンス化する必要があります。
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
ここで、``/Users/sayantan/work/workspaces/mySFWorkspace`` は Mac 上のワークスペースの完全修飾パスで、``/tmp/mySFWorkspace`` はマップ先のコンテナー内のパスです。

> [!NOTE]
>1. ワークスペースの名前/パスが異なる場合は、上記の ``docker run`` コマンドで適宜更新してください。
>2. ``sfonebox`` 以外の別の名前でコンテナーを開始する場合は、Service Fabric アクター Java アプリケーションの ``testclient.sh`` ファイルで適宜更新してください。

## <a name="next-steps"></a>次のステップ
<!-- Links -->
* [Yeoman を使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse 用の Service Fabric プラグインを使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-get-started-eclipse.md)
* [Azure Portal で Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-portal.md)
* [Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)
* [Service Fabric アプリケーション モデルを理解する](service-fabric-application-model.md)
* [Service Fabric CLI を使用してアプリケーションを管理する](service-fabric-application-lifecycle-sfctl.md)
* [Windows で Linux 開発環境を準備する](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship