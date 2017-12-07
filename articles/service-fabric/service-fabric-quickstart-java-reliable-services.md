---
title: "Azure Service Fabric Java アプリケーションを作成する | Microsoft Docs"
description: "Service Fabric のクイック スタート サンプルを使用して Azure 向けの Java アプリケーションを作成します。"
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
ms.date: 10/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: c4966f3ddc95a7e7c97d09cd45abdb8443601b74
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2017
---
# <a name="create-a-java-application"></a>Java アプリケーションの作成
Azure Service Fabric は、マイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

このクイック スタートでは、Linux 開発者コンピューターで Eclipse IDE を使用して、Service Fabric に初めての Java アプリケーションをデプロイする方法を示します。 最後まで読み進めていけば、Java Web フロントエンドからクラスター内のステートフルなバックエンド サービスに投票結果を保存するアプリケーションが完成します。

![アプリケーションのスクリーンショット](./media/service-fabric-quickstart-java/votingapp.png)

このクイックスタートでは、次の方法について説明します。

> [!div class="checklist"]
> * Service Fabric Java アプリケーションのツールとして Eclipse を使用する
> * アプリケーションをローカル クラスターにデプロイする 
> * アプリケーションを Azure のクラスターにデプロイする
> * 複数のノードにアプリケーションをスケールアウトする

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、以下が必要です。
1. [Service Fabric SDK および Service Fabric コマンド ライン インターフェイス (CLI) をインストールする](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Git をインストールする](https://git-scm.com/)
3. [Eclipse をインストールする](https://www.eclipse.org/downloads/)
4. [Java 環境をセットアップ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)し、オプションの手順に従って Eclipse プラグインのインストールを確実に行う 

## <a name="download-the-sample"></a>サンプルのダウンロード
コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。
```
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する
1. 次のコマンドを実行して、ローカル クラスターを開始します。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    ローカル クラスターの起動には、一定の時間がかかります。 クラスターが完全に起動されたことを確認するには、**http://localhost:19080** で Service Fabric Explorer にアクセスします。 5 つの正常なノードは、ローカル クラスターが起動され、実行されていることを示します。 
    
    ![正常なローカル クラスター](./media/service-fabric-quickstart-java/localclusterup.png)

2. Eclipse を開きます。
3. [ファイル] -> [Open Projects from File System]\(ファイル システムからプロジェクトを開く\) の順にクリックします。 
4. [ディレクトリ] をクリックし、Github から複製した `service-fabric-java-quickstart` フォルダーから `Voting` ディレクトリを選択します。 [完了] をクリックします。 

    ![Eclipse の [インポート] ダイアログ](./media/service-fabric-quickstart-java/eclipseimport.png)
    
5. これで、Eclipse のパッケージ エクスプローラーに `Voting` プロジェクトが配置されました。 
6. プロジェクトを右クリックして、**[Service Fabric]** ドロップダウンの **[Publish Application...]\(アプリケーションの発行\)** を選択します。 ターゲット プロファイルとして **[PublishProfiles/Local.json]** を選択し、[発行] をクリックします。 

    ![ローカルの [発行] ダイアログ](./media/service-fabric-quickstart-java/localjson.png)
    
7. 使い慣れた Web ブラウザーを開き、**http://localhost:8080** に接続してアプリケーションにアクセスします。 

    ![ローカルのアプリケーション フロントエンド](./media/service-fabric-quickstart-java/runninglocally.png)
    
これで一連の投票の選択肢を追加して投票を開始できます。 アプリケーションが実行され、データはすべて Service Fabric クラスターに保存されます。別途データベースを用意する必要はありません。

## <a name="deploy-the-application-to-azure"></a>Azure にアプリケーションをデプロイする

### <a name="set-up-your-azure-service-fabric-cluster"></a>Azure Service Fabric クラスターの設定
Azure 内のクラスターにアプリケーションをデプロイする場合、独自のクラスターかパーティー クラスターを作成します。

パーティー クラスターは、Azure でホストされている期間限定の無料 Service Fabric クラスターです。 Service Fabric チームによって実行され、誰でもアプリケーションをデプロイして、プラットフォームについて学ぶことができます。 パーティ クラスターにアクセスするには、[こちらの手順を実行します](http://aka.ms/tryservicefabric)。 

独自のクラスターの作成については、[Azure での Service Fabric クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)に関するページをご覧ください。

> [!Note]
> Web フロントエンド サービスは、ポート 8080 で受信トラフィックをリッスンする構成になっています。 このポートがクラスターで開放されていることを確認してください。 パーティ クラスターを使用している場合、このポートは開放されています。
>

### <a name="deploy-the-application-using-eclipse"></a>Eclipse を使用したアプリケーションのデプロイ
アプリケーションとクラスターの準備ができたので、Eclipse から直接クラスターにデプロイできます。

1. **PublishProfiles** ディレクトリにある **Cloud.json** ファイルを開き、`ConnectionIPOrURL` および `ConnectionPort` フィールドに適切に入力します。 以下に例を示します。 

    ```bash
    {
         "ClusterConnectionParameters": 
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "",
            "ClientCert": ""
         }
    }
    ```

2. プロジェクトを右クリックして、**[Service Fabric]** ドロップダウンの **[Publish Application...]\(アプリケーションの発行\)** を選択します。 ターゲット プロファイルとして **[PublishProfiles/Cloud.json]** を選択し、[発行] をクリックします。 

    ![クラウドの [発行] ダイアログ](./media/service-fabric-quickstart-java/cloudjson.png)

3. 使い慣れた Web ブラウザーを開き、**http://\<ConnectionIPOrURL>:8080** に接続してアプリケーションにアクセスします。 

    ![クラウドのアプリケーション フロントエンド](./media/service-fabric-quickstart-java/runningcloud.png)
    
## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする
サービスは、その負荷の変化に対応するために、クラスターで簡単にスケールすることができます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。 サービスをスケールする方法は複数あり、Service Fabric CLI (sfctl) のスクリプトやコマンドを使用できます。 この例では、Service Fabric Explorer を使用します。

Service Fabric Explorer は、あらゆる Service Fabric クラスターで動作し、ブラウザーからクラスターの HTTP 管理ポート (19080) にアクセスして利用することができます (例: `http://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`)。

Web フロントエンド サービスをスケールするには、次の手順に従います。

1. クラスターで Service Fabric Explorer を開きます (例: `http://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`)。
2. ツリー ビューで **fabric:/Voting/VotingWeb** ノードの横にある省略記号 (3 つの点) をクリックし、**[Scale Service]\(サービスのスケール\)** を選択します。

    ![Service Fabric Explorer スケール サービス](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    これで Web フロントエンド サービスのインスタンス数をスケールできる状態になりました。

3. この数値を **2** に変更し、**[Scale Service]\(サービスのスケール\)** をクリックします。
4. ツリー ビューの **fabric:/Voting/VotingWeb** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。

    ![Service Fabric Explorer スケール サービスの完了](./media/service-fabric-quickstart-java/servicescaled.png)

    このサービスには 2 つのインスタンスがあることがわかります。また、ツリー ビューを見れば、それらのインスタンスがどのノードで実行されているかが確認できます。

たったこれだけの管理タスクにより、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="next-steps"></a>次のステップ
このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * Service Fabric Java アプリケーションのツールとして Eclipse を使用する
> * Java アプリケーションをローカル クラスターにデプロイする 
> * Java アプリケーションを Azure のクラスターにデプロイする
> * 複数のノードにアプリケーションをスケールアウトする

* [Eclipse を使用した Java のサービスのデバッグ](service-fabric-debugging-your-application-java.md)について確認する
* [Jenkins を使用したシームレスな統合とデプロイの設定](service-fabric-cicd-your-linux-applications-with-jenkins.md)について確認する
* その他の[Java サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)を確認する