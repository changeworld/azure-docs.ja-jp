---
title: "Service Fabric と Linux におけるコンテナーのデプロイ | Microsoft Docs"
description: "Service Fabric と、Docker コンテナーを使用してマイクロサービス アプリケーションをデプロイする方法。 この記事では、Service Fabric が提供するコンテナー用の機能と、クラスターに Docker コンテナー イメージをデプロイする方法について説明しています。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: f31c8ab3d2b4fd871c92ac0e7d04bc8d5ab86830
ms.openlocfilehash: 963ca79f83d9ae4b3c5a0e5da6c5fb7985e9ca77


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Service Fabric への Docker コンテナーのデプロイ
> [!div class="op_single_selector"]
> * [Windows コンテナーのデプロイ](service-fabric-deploy-container.md)
> * [Docker コンテナーのデプロイ](service-fabric-deploy-container-linux.md)
>
>

この記事では、コンテナー化されたサービスを Linux 上の Docker コンテナーに構築する手順を紹介しています。

Service Fabric には、コンテナー化されたマイクロサービスで構成されたアプリケーションの構築に役立つ、いくつかのコンテナー機能が用意されています。 これのサービスは、コンテナー化されたサービスと呼ばれます。

機能は次のとおりです。

* コンテナー イメージのデプロイとアクティブ化
* リソース ガバナンス
* リポジトリの認証
* コンテナー ポートからホスト ポートへのマッピング
* コンテナー間での検出と通信
* 環境変数の構成と設定の機能

## <a name="packaging-a-docker-container-with-yeoman"></a>yeoman での Docker コンテナーのパッケージ化
Linux 上でコンテナーをパッケージ化する際は、yeoman テンプレートを使用するか、それとも[アプリケーション パッケージを手動で作成](service-fabric-deploy-container.md#manually)するかを選択できます。

Service Fabric のアプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすコンテナーを 1 つ以上含めることができます。 Linux 用の Service Fabric SDK には、[Yeoman](http://yeoman.io/) ジェネレーターが含まれています。これを使用すると、アプリケーションを作成したり、コンテナー イメージを追加したりする作業が簡単になります。 Yeoman を使用し、Docker コンテナーを 1 つだけ持った新しいアプリケーション *SimpleContainerApp* を作成してみましょう。 生成されたマニフェスト ファイルを編集することで、サービスは後から追加できます。

## <a name="create-the-application"></a>アプリケーションを作成する
1. ターミナルで、「**yo azuresfguest**」と入力します。
2. フレームワークには **[コンテナー]** を選択します。
3. アプリケーションに名前を付けます (例: SimpleContainerApp)。
4. DockerHub リポジトリにあるコンテナー イメージの URL を指定します。 イメージ パラメーターは、<リポジトリ>/<イメージ名> の形式で指定してください。

![コンテナー用 Service Fabric Yeoman ジェネレーター][sf-yeoman]

## <a name="deploy-the-application"></a>アプリケーションのデプロイ
アプリケーションがビルドされたら、Azure CLI を使用してローカル クラスターにデプロイできます。

1. ローカルの Service Fabric クラスターに接続します。

    ```bash
    azure servicefabric cluster connect
    ```
2. テンプレートに用意されているインストール スクリプトを使用してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```
3. ブラウザーを開き、http://localhost:19080/Explorer の Service Fabric Explorer に移動します (Mac OS X で Vagrant を使用している場合は、localhost を VM のプライベート IP に置き換えます)。
4. Applications ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認します。
5. アプリケーション インスタンスを削除し、その種類のアプリケーションの登録を解除するには、テンプレートに指定されているアンインストール スクリプトを使用します。

    ```bash
    ./uninstall.sh
    ```

## <a name="adding-more-services-to-an-existing-application"></a>既存アプリケーションへのサービスの追加

`yo` を使用して作成したアプリケーションに別のコンテナー サービスを追加するには、次の手順を実行します。 
1. ディレクトリを既存アプリケーションのルートに変更します。  たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfguest:AddService` を実行します。



## <a name="next-steps"></a>次のステップ
* [Service Fabric とコンテナーの概要](service-fabric-containers-overview.md)
* [Azure CLI を使用した Service Fabric クラスターの対話操作](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Dec16_HO1-->


