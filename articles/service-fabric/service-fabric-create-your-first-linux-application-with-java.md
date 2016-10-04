<properties
   pageTitle="Java を使用して Linux 上で最初の Service Fabric アプリケーションを作成する | Microsoft Azure"
   description="Java を使用して Service Fabric アプリケーションを作成およびデプロイします"
   services="service-fabric"
   documentationCenter="java"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="seanmck"/>


# 最初の Azure Service Fabric アプリケーションを作成する

> [AZURE.SELECTOR]
- [C シャープ](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java](service-fabric-create-your-first-linux-application-with-java.md)

Service Fabric では、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。このチュートリアルでは Linux 用アプリケーションを作成し、Java を使用するサービスを構築する方法について説明します。

## 前提条件

作業を開始する前に、[Linux 開発環境がセットアップ](service-fabric-get-started-linux.md)されていることを確認してください。Mac OS X を使用している場合は、[Vagrant を使用して仮想マシンに Linux ワンボックス環境を設定](service-fabric-get-started-mac.md)します。

## アプリケーションを作成する

Service Fabric のアプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスを 1 つ以上含めることができます。Linux 用の Service Fabric SDK には、[Yeoman](http://yeoman.io/) ジェネレーターが含まれています。これを使用すると、初めてサービスを作成したり、後で追加したりする作業が簡単になります。Yeoman を使用して、単一のサービスを持つ新しいアプリケーションを作成しましょう。

1. ターミナルで、「**yo azuresfjava**」と入力します。

2. アプリケーションに名前を付けます。

3. 最初のサービスの種類を選択し、名前を付けます。このチュートリアルでは、"Reliable Actor Service" を選択します。

  ![Service Fabric Yeoman generator for Java][sf-yeoman]

>[AZURE.NOTE] オプションの詳細については、「[Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。

## アプリケーションのビルド

Service Fabric Yeoman テンプレートには、[Gradle](https://gradle.org/) のビルド スクリプトが含まれています。端末からアプリをビルドするために、これを使用することができます。

  ```bash
  gradle
  ```

## アプリケーションのデプロイ

アプリケーションがビルドされたら、Azure CLI を使用してローカル クラスターにデプロイできます。

1. ローカルの Service Fabric クラスターに接続します。

    ```bash
    azuresfcli servicefabric cluster connect
    ```

2. テンプレートに用意されているインストール スクリプトを使用してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    cd myapp
    ./install.sh
    ```

3. ブラウザーを開き、http://localhost:19080/Explorer の Service Fabric Explorer に移動します (Mac OS X で Vagrant を使用している場合は、localhost を VM のプライベート IP に置き換えます)。

4. Applications ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認します。

## テスト クライアントの起動と、フェールオーバーの実行

アクター プロジェクトは、それ自体では何も行いません。これにメッセージを送信する別のサービスまたはクライアントが必要です。アクター テンプレートには、アクター サービスとの対話に使用できる簡単なテスト スクリプトが含まれています。

1. ウォッチ ユーティリティを使用してスクリプトを実行し、アクター サービスの出力を確認します。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Service Fabric Explorer で、アクター サービスのプライマリ レプリカをホストしているノードを見つけます。次のスクリーンショットでは、ノード 3 です。

    ![Finding the primary replica in Service Fabric Explorer][sfx-primary]

3. 前の手順で見つけたノードをクリックし、[アクション] メニューの **[非アクティブにする (再起動)]** を選択します。ローカル クラスターの 5 つのノードのいずれかが再起動され、別のノードで実行されているセカンダリ レプリカのいずれかに強制的にフェールオーバーされます。この操作を行うとき、テスト クライアントからの出力に注意してください。また、フェールオーバーにかかわらず、カウンターが増加していることに注意してください。

## Eclipse Neon プラグインによるアプリケーションのビルドとデプロイ

Eclipse Neon のサービス プラグインをインストールした場合は、それを使用して、Java でビルドした Service Fabric アプリケーションを作成、ビルド、およびデプロイすることができます。

### アプリケーションを作成する

Service Fabric プラグインは、Eclipse 拡張機能を通じて使用できます。

1. Eclipse で、**[File (ファイル)]、[Other (その他)]、[Service Fabric]** の順に選択します。アクターとコンテナーを含む、オプションのセットが表示されます。

    ![Service Fabric templates in Eclipse][sf-eclipse-templates]

2. この場合は、ステートレス サービスを選択します。

3. Service Fabric の観点を使用することの確認を求めるメッセージが表示されます。この観点を使用すると、Service Fabric プロジェクトと共に使用される Eclipse が最適化されます。[Yes (はい)] を選択します。

### アプリケーションのデプロイ

Service Fabric テンプレートには、アプリケーションをビルドおよびデプロイするための、一連の Gradle タスクが含まれています。これらのタスクは、Eclipse を通じてトリガーできます。

1. **[Run (実行)]、[Run Configurations (構成の実行)]** の順に選択します。

2. **[Gradle Project (Gradle プロジェクト)]** を展開し、**[ServiceFabricDeployer]** を選択します。

3. **[実行]** をクリックします。

アプリは数分以内でビルド、デプロイされます。Service Fabric Explorer で、その状況を監視できます。

## 次のステップ

- [Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

<!---HONumber=AcomDC_0928_2016-->