---
title: "Spring Boot アプリケーションを Azure Service Fabric にデプロイする | Microsoft Docs"
description: "Spring Boot の Getting Started サンプルを使用して Azure Service Fabric に Spring Boot アプリケーションをデプロイします。"
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 8e57ed29560baeea6279a45e8769f4005c123b56
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-a-spring-boot-application"></a>Spring Boot アプリケーションのデプロイ
Azure Service Fabric は、マイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

このクイックスタートでは、Spring Boot アプリケーションを Service Fabric にデプロイする方法を紹介します。 このクイックスタートでは、Spring の Web サイトからの [Getting Started](https://spring.io/guides/gs/spring-boot/) サンプルを使用します。 このクイックスタートでは、使い慣れたコマンド ライン ツールを使用し、Azure Service Fabric アプリケーションとして Spring Boot サンプルをデプロイする方法を説明します。 クイックスタートを完了すると、Spring Boot の Getting Started サンプルが Azure Service Fabric 上で動作します。 

![アプリケーションのスクリーンショット](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

このクイックスタートでは、次の方法について説明します。

> [!div class="checklist"]
> * Spring Boot アプリケーションを Service Fabric にデプロイする
> * アプリケーションをローカル クラスターにデプロイする 
> * アプリケーションを Azure のクラスターにデプロイする
> * 複数のノードにアプリケーションをスケールアウトする
> * 可用性に影響を与えることなくサービスのフェールオーバーを実行する

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、以下が必要です。
1. [Service Fabric SDK および Service Fabric コマンド ライン インターフェイス (CLI) をインストールする](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Git をインストールする](https://git-scm.com/)
3. [Yeoman をインストールする](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Java 環境を設定する](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>サンプルのダウンロード
コマンド ウィンドウで次のコマンドを実行して、Spring Boot の Getting Started サンプル アプリをローカル コンピューターに複製します。
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Spring Boot アプリケーションのパッケージ作成 
1. 複製した `gs-spring-boot` ディレクトリ内で `yo azuresfguest` コマンドを実行します。 

2. 各プロンプトで次の情報を入力します。 

    ![Yeoman エントリ](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` フォルダーで、`entryPoint.sh` という名前のファイルを作成します。 このファイルに次のコードを追加します。 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

この段階では、Azure Service Fabric にデプロイできる Spring Boot の Getting Started サンプルのための Azure Service Fabric アプリケーションを作成しました。

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する
1. 次のコマンドを実行して、ローカル クラスターを開始します。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    ローカル クラスターの起動には、一定の時間がかかります。 クラスターが完全に起動されたことを確認するには、**http://localhost:19080** で Service Fabric Explorer にアクセスします。 5 つの正常なノードは、ローカル クラスターが起動され、実行されていることを示します。 
    
    ![正常なローカル クラスター](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. `gs-spring-boot/SpringServiceFabric` フォルダーに移動します。
3. 次のコマンドを実行して、ローカル クラスターに接続します。 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. `install.sh` スクリプトを実行します。 

    ```bash
    ./install.sh
    ```

5. 使い慣れた Web ブラウザーを開き、**http://localhost:8080** に接続してアプリケーションにアクセスします。 

    ![ローカルのアプリケーション フロントエンド](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
これで、Azure Service Fabric クラスターに展開された Spring Boot アプリケーションにアクセスできるようになりました。  

## <a name="deploy-the-application-to-azure"></a>Azure にアプリケーションを展開する

### <a name="set-up-your-azure-service-fabric-cluster"></a>Azure Service Fabric クラスターの設定
Azure 内のクラスターにアプリケーションをデプロイするには、独自のクラスターを作成します。

パーティー クラスターは、Azure でホストされている期間限定の無料 Service Fabric クラスターです。 Service Fabric チームによって実行され、誰でもアプリケーションをデプロイして、プラットフォームについて学ぶことができます。 パーティ クラスターにアクセスするには、[こちらの手順を実行します](http://aka.ms/tryservicefabric)。 

セキュリティで保護されたパーティ クラスターに対する管理操作は、Service Fabric Explorer、CLI、Powershell のいずれかを使用して実行できます。 Service Fabric Explorer を使用するには、パーティ クラスターの Web サイトから PFX ファイルをダウンロードし、ご使用の証明書ストア (Windows または Mac) またはブラウザー本体 (Ubuntu) に証明書をインポートする必要があります。 パーティ クラスターの自己署名証明書についてはパスワードは不要です。 

PowerShell または CLI で管理操作を実行するには、PFX (PowerShell) または PEM (CLI) が必要となります。 PFX を PEM ファイルに変換するには、次のコマンドを実行してください。  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

独自のクラスターの作成については、[Azure での Service Fabric クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)に関するページをご覧ください。

> [!Note]
> Spring Boot サービスは、ポート 8080 で受信トラフィックをリッスンする構成になっています。 このポートがクラスターで開放されていることを確認してください。 パーティ クラスターを使用している場合、このポートは開放されています。
>

### <a name="deploy-the-application-using-cli"></a>CLI を使用したアプリケーションのデプロイ
アプリケーションとクラスターの準備ができましたので、コマンド ラインから直接クラスターにデプロイできます。

1. `gs-spring-boot/SpringServiceFabric` フォルダーに移動します。
2. 次のコマンドを実行して、Azure クラスターに接続します。 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    クラスターが自己署名証明書を使用してセキュリティ保護されている場合は、次のコマンドを実行します。 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. `install.sh` スクリプトを実行します。 

    ```bash
    ./install.sh
    ```

4. 任意の Web ブラウザーを開き、**http://\<ConnectionIPOrURL>:8080** に接続してアプリケーションにアクセスします。 

    ![ローカルのアプリケーション フロントエンド](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
これで、Azure Service Fabric クラスターに展開された Spring Boot アプリケーションにアクセスできるようになりました。  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする
サービスは、その負荷の変化に対応するために、クラスターで簡単にスケールすることができます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。 サービスをスケールする方法は複数あり、Service Fabric CLI (sfctl) のスクリプトやコマンドを使用できます。 この例では、Service Fabric Explorer を使用します。

Service Fabric Explorer は、あらゆる Service Fabric クラスターで動作し、ブラウザーからクラスターの HTTP 管理ポート (19080) にアクセスして利用することができます (例: `http://localhost:19080`)。

Web フロントエンド サービスをスケールするには、次の手順に従います。

1. クラスターで Service Fabric Explorer を開きます (例: `http://localhost:19080`)。
2. ツリー ビューで **fabric:/SpringServiceFabric/SpringGettingStarted** ノードの横にある省略記号 (3 つの点) をクリックし、**[Scale Service]\(サービスのスケール\)** を選択します。

    ![Service Fabric Explorer スケール サービス](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    次に、スケーリングするサービスのインスタンス数を選択します。

3. 数値を **3** に変更し、**[Scale Service]\(サービスのスケール\)** をクリックします。

    または、次のコマンドラインを使用してサービスを拡張することもできます。

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. ツリー ビューの **fabric:/SpringServiceFabric/SpringGettingStarted** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。

    ![Service Fabric Explorer スケール サービスの完了](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    サービスには 3 つのインスタンスがあり、各インスタンスを実行しているノードがツリー ビューに表示されます。

このように簡単な管理タスクを実行するだけで、Spring サービスでユーザー負荷を処理するためのリソースが追加されます。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="fail-over-services-in-a-cluster"></a>クラスターのフェールオーバー サービス 
サービスのフェールオーバーを示すために、Service Fabric Explorer を使用して、ノードの再起動をシミュレートします。 サービス インスタンスが 1 つのみ実行されていることを確認してください。 

1. クラスターで Service Fabric Explorer を開きます (例: `http://localhost:19080`)。
2. サービスのインスタンスを実行しているノードの横にある省略記号 (3 つのドット) をクリックし、ノードを再起動します。 

    ![Service Fabric Explorer でのノードの再起動](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. サービスのインスタンスは別のノードに移動され、アプリケーションにはダウンタイムは発生しません。 

    ![Service Fabric Explorer でのノード再起動成功](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>次の手順
このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * Spring Boot アプリケーションを Service Fabric にデプロイする
> * アプリケーションをローカル クラスターにデプロイする 
> * アプリケーションを Azure のクラスターにデプロイする
> * 複数のノードにアプリケーションをスケールアウトする
> * 可用性に影響を与えることなくサービスのフェールオーバーを実行する

* [Azure Service Fabric プログラミング モデルを使用した Java microservices の構築](service-fabric-quickstart-java-reliable-services.md)の詳細
* [Jenkins を使用した継続的インテグレーションとデプロイの設定](service-fabric-cicd-your-linux-applications-with-jenkins.md)について確認する
* その他の[Java サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)を確認する