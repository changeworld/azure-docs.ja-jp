---
title: "Linux 上で Azure Service Fabric Reliable Actors Java アプリケーションを作成する | Microsoft Docs"
description: "Java Service Fabric Reliable Actors アプリケーションを 5 分で作成してデプロイする方法について説明します。"
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 254f38a600ea4026120bc411368eeb01310e56b2
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Linux で初めての Java Service Fabric Reliable Actors アプリケーションを作成する

このクイックスタートを参考にすると、Linux 開発環境で初めての Azure Service Fabric Java アプリケーションをほんの数分で作成することができます。  作業が終了すると、単純な Java 単一サービス アプリケーションがローカル開発クラスターで実行された状態になります。  

## <a name="prerequisites"></a>前提条件
作業を開始する前に、[Linux 開発環境](service-fabric-get-started-linux.md)に Service Fabric SDK と Azure CLI をインストールし、開発クラスターをセットアップします。 Mac OS X を使用している場合は、[Vagrant を使用して仮想マシンに Linux 開発環境をセットアップする](service-fabric-get-started-mac.md)ことができます。

また、アプリケーションをデプロイするために、[Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (推奨) または [XPlat CLI](service-fabric-azure-cli.md) を構成してください。

## <a name="create-the-application"></a>アプリケーションを作成する
Service Fabric アプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスが 1 つ以上含まれます。 Linux 用の Service Fabric SDK には、[Yeoman](http://yeoman.io/) ジェネレーターが含まれています。これを使用すると、初めてサービスを作成したり、後で追加したりする作業が簡単になります。  Eclipse 用のプラグインを使用して、Service Fabric Java アプリケーションを作成、ビルド、およびデプロイすることもできます。 [Eclipse を使用した初めての Java アプリケーションの作成とデプロイ](service-fabric-get-started-eclipse.md)に関するページを参照してください。 このクイック スタートでは、Yeoman を使用して、カウンター値の格納と取得という単一のサービスを行うアプリケーションを作成します。

1. ターミナルで、「``yo azuresfjava``」と入力します。
2. アプリケーションに名前を付けます。 
3. 最初のサービスの種類を選択し、名前を付けます。 このチュートリアルでは、[Reliable Actor サービス] を選択します。 他の種類のサービスの詳細については、「[Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。
   ![Java 用 Service Fabric Yeoman ジェネレーター][sf-yeoman]

## <a name="build-the-application"></a>アプリケーションのビルド
Service Fabric Yeoman テンプレートには、[Gradle](https://gradle.org/) のビルド スクリプトが含まれています。このスクリプトを使用して、端末からアプリケーションをビルドすることができます。 アプリケーションをビルドしてパッケージ化するには、次のコマンドを実行します。

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>アプリケーションのデプロイ
ビルドしたアプリケーションは、ローカル クラスターにデプロイできます。

### <a name="using-xplat-cli"></a>XPlat CLI の使用

1. ローカルの Service Fabric クラスターに接続します。

    ```bash
    azure servicefabric cluster connect
    ```

2. テンプレートに用意されているインストール スクリプトを実行してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```

### <a name="using-azure-cli-20"></a>Azure CLI 2.0 の使用

ビルドしたアプリケーションは、他のすべての Service Fabric アプリケーションと同じようにデプロイできます。 詳細な手順については、[Azure CLI を使用した Service Fabric アプリケーションの管理](service-fabric-application-lifecycle-azure-cli-2-0.md)についてのドキュメントを参照してください。

これらのコマンドのパラメーターは、アプリケーション パッケージ内の生成されたマニフェストで確認できます。

アプリケーションのデプロイ後、ブラウザーを開いて [http://localhost:19080/Explorer](http://localhost:19080/Explorer) の [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) に移動します。
次に、**Applications** ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認してください。

## <a name="start-the-test-client-and-perform-a-failover"></a>テスト クライアントの起動と、フェールオーバーの実行
アクターは単独では何も実行しません。メッセージを送信するには、別のサービスまたはクライアントが必要です。 アクター テンプレートには、アクター サービスとの対話に使用できる簡単なテスト スクリプトが含まれています。

1. ウォッチ ユーティリティを使用してスクリプトを実行し、アクター サービスの出力を確認します。  テスト スクリプトは、アクターに対して `setCountAsync()` メソッドを呼び出してカウンターを増分させ、`getCountAsync()` メソッドを呼び出して新しいカウンター値を取得し、その値をコンソールに表示します。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Service Fabric Explorer で、アクター サービスのプライマリ レプリカをホストしているノードを見つけます。 次のスクリーンショットでは、ノード 3 です。 プライマリ サービス レプリカは、読み取り操作と書き込み操作を処理します。  サービスの状態の変更がセカンダリ レプリカにレプリケートされます (次のスクリーンショットでは、ノード 0 のノード 1 で実行されています)。

    ![Finding the primary replica in Service Fabric Explorer][sfx-primary]

3. **[ノード]** で、前の手順で見つけたノードをクリックし、[アクション] メニューの **[非アクティブにする (再起動)]** を選択します。 この操作によって、プライマリ サービス レプリカを実行しているノードが再起動され、別のノードで実行されているセカンダリ レプリカのいずれかに強制的にフェールオーバーされます。  フェールオーバーされたセカンダリ レプリカがプライマリに昇格し、別のノードに別のセカンダリ レプリカが作成され、プライマリ レプリカによる読み取り/書き込み操作が開始されます。 ノードが再起動したら、テスト クライアントからの出力を監視して、フェールオーバーにかかわらず、カウンターが増加し続けることを確認してください。

## <a name="remove-the-application"></a>アプリケーションを削除する
テンプレートに用意されているアンインストール スクリプトを使用してアプリケーション インスタンスを削除し、アプリケーション パッケージの登録を解除して、クラスターのイメージ ストアからアプリケーション パッケージを削除します。

```bash
./uninstall.sh
```

Service Fabric explorer で、アプリケーションとアプリケーションの種類が **[アプリケーション]** ノードに表示されていないことを確認します。

## <a name="next-steps"></a>次のステップ
* [Linux で初めての Service Fabric Java アプリケーションを作成する](service-fabric-get-started-eclipse.md)
* [Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)
* [Azure CLI を使用した Service Fabric クラスターの対話操作](service-fabric-azure-cli.md)
* [デプロイのトラブルシューティング](service-fabric-azure-cli.md#troubleshooting)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

## <a name="related-articles"></a>関連記事:

* [Service Fabric と Azure CLI 2.0 の概要](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI の概要](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

