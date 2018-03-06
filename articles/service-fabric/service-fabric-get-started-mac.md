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
ms.openlocfilehash: 4ac26c02e1893097c858380c07f520e6570fd3db
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Mac OS X で開発環境をセットアップする
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux クラスターで実行される Azure Service Fabric アプリケーションを Mac OS X を使用して構築できます。このドキュメントでは、開発用に Mac をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件
Azure Service Fabric は、Mac OS X ではネイティブに実行されません。ローカルの Service Fabric クラスターを実行できるように、事前に構成済みの Docker コンテナー イメージが用意されています。 作業を開始する前に、以下を行う必要があります。

* 少なくとも 4 GB の RAM。
* 最新バージョンの [Docker](https://www.docker.com/)。

>[!TIP]
>
>Mac に Docker をインストールには、[Docker のドキュメント](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install)の手順に従ってください。 インストールの完了後、[インストールを確認](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)します。
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>ローカル コンテナーを作成し、Service Fabric をセットアップする
ローカル Docker コンテナーをセットアップし、そこで Service Fabric クラスターを実行するには、次の手順を実行します。

1. Docker ハブ リポジトリから Service Fabric onebox コンテナー イメージをプルします。 既定では、最新バージョンの Service Fabric を含んだイメージがプルされます。 特定のリビジョンについては、[Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) のページをご覧ください。

    ```bash
    docker pull microsoft/service-fabric-onebox
    ```

2. 以下の設定を使用してホスト上の Docker デーモン構成を更新し、Docker デーモンを再起動します。 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    これらの設定は、Docker インストール パスの daemon.json ファイルで直接更新できます。
    
    >[!NOTE]
    >
    >daemon.json ファイルの場所は、マシンによって異なる場合があります。 例: ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json
    >
    >Docker でデーモン構成設定を直接変更することをお勧めします。 **Docker アイコン**を選択し、**[Preferences]\(環境設定\)** > **[Daemon]\(デーモン\)** > **[Advanced]\(詳細設定\)** の順に選択します。
    >

3. Service Fabric onebox コンテナー インスタンスを起動し、最初の手順でプルしたイメージを使用します。

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    >操作の際にわかりやすくするために、コンテナー インスタンスの名前を指定します。 
    >
    >アプリケーションが特定のポートでリッスンしている場合は、追加の `-p` タグを使用してポートを指定する必要があります。 たとえば、アプリケーションがポート 8080 でリッスンしている場合は、次の `-p` タグを追加します。
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

4. 対話型 SSH モードで Docker コンテナーにログオンします。

    ```bash
    docker exec -it sfonebox bash
    ```

5. セットアップ スクリプトを実行して、必要な依存関係をフェッチし、コンテナーでクラスターを起動します。

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. 手順 5. が完了したら、Mac から `http://localhost:19080` を参照します。 Service Fabric Explorer が表示されます。

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Mac に Service Fabric CLI (sfctl) をセットアップする

[Service Fabric CLI](service-fabric-cli.md#cli-mac) に関するページの手順に従って、Mac に Service Fabric CLI (`sfctl`) をインストールしてください。
この CLI コマンドは、クラスター、アプリケーション、サービスなどの Service Fabric エンティティの操作をサポートします。

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Mac 上で Yeoman を使ってアプリケーションを作成する

Service Fabric には、ターミナルから Yeoman テンプレート ジェネレーターを使って Service Fabric アプリケーションを作成するのに役立つスキャフォールディング ツールが用意されています。 以下の手順に従って、ご利用のマシンで Service Fabric Yeoman テンプレート ジェネレーターが実行されていることを確認してください。

1. Mac に Node.js とノード パッケージ マネージャー (NPM) がインストールされている必要があります。 このソフトウェアは、次のように [HomeBrew](https://brew.sh/) を使用してインストールできます。

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
4. Service Fabric Java アプリケーションを Mac で構築するには、JDK バージョン 1.8 と Gradle がホスト マシンにインストールされている必要があります。 このソフトウェアは、次のように [HomeBrew](https://brew.sh/) を使用してインストールできます。 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>ターミナルから Mac にアプリケーションをデプロイする

Service Fabric アプリケーションを作成して構築したら、[Service Fabric CLI](service-fabric-cli.md#cli-mac) を使用してアプリケーションをデプロイできます。

1. Mac のコンテナー インスタンス内で実行されている Service Fabric クラスターに接続します。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. プロジェクト ディレクトリ内で、インストール スクリプトを実行します。

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 開発環境をセットアップする

[.NET Core 2.0 SDK for Mac](https://www.microsoft.com/net/core#macos) をインストールして、[C# Service Fabric アプリケーションの作成](service-fabric-create-your-first-linux-application-with-csharp.md)を開始します。 .NET Core 2.0 Service Fabric アプリケーション用のパッケージは、現在プレビューの段階で NuGet.org でホストされています。

## <a name="install-the-service-fabric-plug-in-for-eclipse-neon-on-your-mac"></a>Eclipse Neon 用の Service Fabric プラグインを Mac にインストールする

Azure Service Fabric には、Eclipse Neon Java IDE 用のプラグインが用意されています。 このプラグインを使用すると、Java サービスの作成、ビルド、およびデプロイの手順を簡素化できます。 Eclipse 用の Service Fabric プラグインをインストールしたり、最新バージョンに更新したりするには、[これらの手順](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)に従います。 [Eclipse 用の Service Fabric のドキュメント](service-fabric-get-started-eclipse.md)に記載されている、アプリケーションの構築、アプリケーションへのサービスの追加、アプリケーションのアンインストールなどの他のすべての手順も適用できます。

最後の手順では、ホストと共有されているパスでコンテナーをインスタンス化します。 このプラグインでは、Mac 上の Docker コンテナーを操作するために、この種のインスタンス化が必要です。 例: 

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

属性は次のように定義されます。
* `/Users/sayantan/work/workspaces/mySFWorkspace` は、Mac 上のワークスペースの完全修飾パスです。
* `/tmp/mySFWorkspace` は、ワークスペースをマップするコンテナーの内部のパスです。

>[!NOTE]
> 
>ワークスペースの名前/パスが異なる場合は、`docker run` コマンドでこれらの値を更新してください。
> 
>`sfonebox` 以外の名前でコンテナーを開始する場合は、Service Fabric アクター Java アプリケーションの testclient.sh ファイルの名前値を更新してください。
>

## <a name="next-steps"></a>次の手順
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
