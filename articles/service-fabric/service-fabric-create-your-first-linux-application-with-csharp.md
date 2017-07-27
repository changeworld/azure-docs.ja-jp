---
title: "C# を使用して Linux で初めての Azure マイクロサービス アプリを作成する | Microsoft Docs"
description: "C# を使用して、Service Fabric アプリケーションを作成し、デプロイします"
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 4baf144cc28eeff0ab8f8b60e837f8a2bad903af
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017


---
<a id="create-your-first-azure-service-fabric-application" class="xliff"></a>

# 最初の Azure Service Fabric アプリケーションを作成する
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric では、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。 このチュートリアルでは Linux 用アプリケーションを作成し、C# (.NET Core) を使用してサービスを構築する方法について説明します。

<a id="prerequisites" class="xliff"></a>

## 前提条件
作業を開始する前に、 [Linux 開発環境がセットアップ](service-fabric-get-started-linux.md)されていることを確認してください。 Mac OS X を使用している場合は、 [Vagrant を使用して仮想マシンに Linux ワンボックス環境を設定](service-fabric-get-started-mac.md)します。

また、アプリケーションをデプロイするために、[Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (推奨) または [XPlat CLI](service-fabric-azure-cli.md) を構成してください。

<a id="create-the-application" class="xliff"></a>

## アプリケーションを作成する
Service Fabric のアプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスを 1 つ以上含めることができます。 Linux 用の Service Fabric SDK には、[Yeoman](http://yeoman.io/) ジェネレーターが含まれています。これを使用すると、初めてサービスを作成したり、後で追加したりする作業が簡単になります。 Yeoman を使用して、単一のサービスを持つアプリケーションを作成しましょう。

1. ターミナルで、コマンド「`yo azuresfcsharp`」を入力してスキャフォールディングの構築を開始します。
2. アプリケーションに名前を付けます。
3. 最初のサービスの種類を選択し、名前を付けます。 このチュートリアルでは、"Reliable Actor Service" を選択します。

   ![C# 用の Service Fabric Yeoman ジェネレーター][sf-yeoman]

> [!NOTE]
> オプションの詳細については、「 [Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。
>
>

<a id="build-the-application" class="xliff"></a>

## アプリケーションのビルド
Service Fabric Yeoman テンプレートには、ビルド スクリプトが含まれています。(アプリケーションのフォルダーへの移動後に) これを使用してターミナルからアプリをビルドできます。

  ```sh
 cd myapp
 ./build.sh
  ```

<a id="deploy-the-application" class="xliff"></a>

## アプリケーションのデプロイ

ビルドしたアプリケーションは、ローカル クラスターにデプロイできます。

<a id="using-xplat-cli" class="xliff"></a>

### XPlat CLI の使用

1. ローカルの Service Fabric クラスターに接続します。

    ```bash
    azure servicefabric cluster connect
    ```

2. テンプレートに用意されているインストール スクリプトを実行してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```

<a id="using-azure-cli-20" class="xliff"></a>

### Azure CLI 2.0 の使用

ビルドしたアプリケーションは、他のすべての Service Fabric アプリケーションと同じようにデプロイできます。 詳細な手順については、[Azure CLI を使用した Service Fabric アプリケーションの管理](service-fabric-application-lifecycle-azure-cli-2-0.md)についてのドキュメントを参照してください。

これらのコマンドのパラメーターは、アプリケーション パッケージ内の生成されたマニフェストで確認できます。

アプリケーションのデプロイ後、ブラウザーを開いて [http://localhost:19080/Explorer](http://localhost:19080/Explorer) の [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) に移動します。
次に、**Applications** ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認してください。

<a id="start-the-test-client-and-perform-a-failover" class="xliff"></a>

## テスト クライアントの起動と、フェールオーバーの実行
アクター プロジェクトは、それ自体では何も行いません。 これにメッセージを送信する別のサービスまたはクライアントが必要です。 アクター テンプレートには、アクター サービスとの対話に使用できる簡単なテスト スクリプトが含まれています。

1. ウォッチ ユーティリティを使用してスクリプトを実行し、アクター サービスの出力を確認します。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Service Fabric Explorer で、アクター サービスのプライマリ レプリカをホストしているノードを見つけます。 次のスクリーンショットでは、ノード 3 です。

    ![Finding the primary replica in Service Fabric Explorer][sfx-primary]
3. 前の手順で見つけたノードをクリックし、[アクション] メニューの **[非アクティブにする (再起動)]** を選択します。 このアクションによって、ローカル クラスターのノードのいずれかが再起動され、別のノードで実行されているセカンダリ レプリカに強制的にフェールオーバーされます。 このアクションを行うときは、テスト クライアントからの出力に注意してください。また、フェールオーバーにかかわらず、カウンターが増加していることに注意してください。

<a id="adding-more-services-to-an-existing-application" class="xliff"></a>

## 既存アプリケーションへのサービスの追加

`yo` を使用して作成したアプリケーションにサービスを追加するには、次の手順を実行します。 
1. ディレクトリを既存アプリケーションのルートに変更します。  たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfcsharp:AddService` を実行します。

<a id="migrating-from-projectjson-to-csproj" class="xliff"></a>

## project.json から .csproj への移行
1. 'dotnet migrate' をプロジェクトのルート ディレクトリで実行すると、すべての project.json が csproj 形式に移行します。
2. それに応じて、プロジェクト ファイル内の csproj ファイルのプロジェクト参照を更新します。
3. build.sh でプロジェクト ファイルの名前を csproj ファイルに更新します。

<a id="next-steps" class="xliff"></a>

## 次のステップ
* [Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)
* [Azure CLI を使用した Service Fabric クラスターの対話操作](service-fabric-azure-cli.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

<a id="related-articles" class="xliff"></a>

## 関連記事:

* [Service Fabric と Azure CLI 2.0 の概要](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI の概要](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png

