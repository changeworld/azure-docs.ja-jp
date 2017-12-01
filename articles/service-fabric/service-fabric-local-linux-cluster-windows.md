---
title: "Windows で Azure Service Fabric Linux クラスターを設定する | Microsoft Docs"
description: "この記事では、Windows 開発用マシンで実行される Service Fabric Linux クラスターを設定する方法について説明します。 これは、クロス プラットフォーム開発で特に便利です。"
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 57f9dae1b353b873fdc0ec5903018d160cfe384f
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Windows 開発用マシンで Linux Service Fabric クラスターを設定する

このドキュメントでは、Windows 開発用マシンでローカル Service Fabric を設定する方法について説明します。 ローカル Linux クラスターを設定すると、Linux クラスターを対象としているが、Windows マシンで開発されたアプリケーションをすばやくテストするときに便利です。

## <a name="prerequisites"></a>前提条件
Linux ベースの Service Fabric クラスターは、Windows ではネイティブに実行されません。 ローカル Service Fabric クラスターを実行するために、事前構成済みの Docker コンテナー イメージが用意されています。 作業を開始する前に、以下を行う必要があります。

* 少なくとも 4 GB の RAM
* 最新バージョンの [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Service Fabric One-box Docker コンテナー [イメージ](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/)へのアクセス

>[!TIP]
> * Docker の公式[ドキュメント](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)に記載されている手順に従って Windows に Docker をインストールしてください。 
> * インストールが完了したら、[ここ](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)に記載されている手順に従って、Docker が正しくインストールされているかどうかを確認します。


## <a name="create-a-local-container-and-setup-service-fabric"></a>ローカル コンテナーを作成し、Service Fabric をセットアップする
ローカル Docker コンテナーを設定し、そこでサービス ファブリック クラスターを実行するには、次の手順を実行します。

1. Docker ハブ リポジトリからイメージをプルします。

    ```powershell
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. ホスト上の Docker デーモン構成を以下を使用して更新し、Docker デーモンを再起動します。 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Docker アイコン、[設定]、[デーモン]、[詳細設定] の順に選択し、そこで更新することをお勧めします。 次に、Docker デーモンを再起動し、変更を反映させます。 

3. イメージを使用して Service Fabric One-box コンテナー インスタンスを起動します。

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * コンテナー インスタンスの名前を指定すると、読みやすい方法で処理することができます。 
    > * アプリケーションが特定のポートでリッスンしている場合は、追加の -p タグを使用して指定する必要があります。 たとえば、アプリケーションがポート 8080 でリッスンしている場合、docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox を実行します。

4. 対話型 ssh モードで Docker コンテナーにログインします。

    ```powershell
    docker exec -it sfonebox bash
    ```

5. セットアップ スクリプトを実行します。必要な依存関係がフェッチされ、その後、コンテナーのクラスターが起動されます。

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. 手順 5. が正常に完了したら、Windows から ``http://localhost:19080`` にアクセスします。Service Fabric Explorer が表示されます。 この時点で、Windows 開発者用マシンから任意のツールを使用してこのクラスターに接続し、Linux Service Fabric クラスターを対象としたアプリケーションを配置します。 

    > [!NOTE]
    > Eclipse プラグインは、現在、Windows でサポートされていません。 

## <a name="next-steps"></a>次のステップ
* [Eclipse](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-eclipse) の概要
* その他の [Java サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)を確認する


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png