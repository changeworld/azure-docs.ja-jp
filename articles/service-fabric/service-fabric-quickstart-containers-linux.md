---
title: "Linux 上で Azure Service Fabric コンテナー アプリケーションを作成する | Microsoft Docs"
description: "Azure Service Fabric で初めての Linux コンテナー アプリケーションを作成します。  アプリケーションの Docker イメージをビルドして、そのイメージをコンテナー レジストリにプッシュし、Service Fabric コンテナー アプリケーションをビルドおよびデプロイします。"
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 6aec2146d83c18a1e1714843cd49890f178e4fb3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Azure Service Fabric Linux コンテナー アプリケーションを Azure にデプロイする
Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

このクイックスタートでは、Linux コンテナーを Service Fabric クラスターにデプロイする方法を説明します。 完了すると、Service Fabric クラスターで実行される Python Web フロントエンドと Redis バックエンドで構成される投票アプリケーションが作成されます。 

![quickstartpic][quickstartpic]

このクイックスタートでは、次の方法について説明します。
> [!div class="checklist"]
> * Azure Linux Service Fabric クラスターへのコンテナーのデプロイ
> * Service Fabric でのコンテナーのスケールとフェールオーバー

## <a name="prerequisite"></a>前提条件
1. Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

2. コマンド ライン インターフェイス (CLI) をローカルにインストールして使用する場合、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには、az --version を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

## <a name="get-application-package"></a>アプリケーション パッケージを取得する
コンテナーを Service Fabric にデプロイするには、個別のコンテナーとアプリケーションを説明する一連のマニフェスト ファイル (アプリケーション定義) が必要です。

Cloud Shell で git を使用して、アプリケーション定義のコピーを複製します。

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```
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
> Web フロントエンド サービスは、ポート 80 で受信トラフィックをリッスンする構成になっています。 このポートがクラスターで開放されていることを確認してください。 パーティ クラスターを使用している場合、このポートは開放されています。
>

### <a name="install-service-fabric-command-line-interface-and-connect-to-your-cluster"></a>Service Fabric コマンド ライン インターフェイスをインストールしてクラスターに接続する

Azure CLI を使用して Azure の Service Fabric クラスターに接続します。 エンドポイントはクラスターの管理エンドポイントです (`https://linh1x87d1d.westus.cloudapp.azure.com:19080` など)。

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

### <a name="deploy-the-service-fabric-application"></a>Service Fabric アプリケーションをデプロイする 
Service Fabric コンテナー アプリケーションは、記載されている Service Fabric アプリケーション パッケージまたは Docker Compose を使用してデプロイできます。 

#### <a name="deploy-using-service-fabric-application-package"></a>Service Fabric アプリケーション パッケージを使用してデプロイする
用意されているインストール スクリプトを使用してクラスターに投票アプリケーション定義をコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

```bash
./install.sh
```

#### <a name="deploy-the-application-using-docker-compose"></a>Docker Compose を使用してアプリケーションをデプロイする
Docker Compose と次のコマンドを使用して、Service Fabric クラスターにアプリケーションをデプロイし、インストールします。
```bash
sfctl compose create --deployment-name TestApp --file-path docker-compose.yml
```

ブラウザーを開いて、http://\<my-azure-service-fabric-cluster-url>:19080/Explorer (`http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer` など) で Service Fabric Explorer に移動します。 アプリケーション ノードを展開し、投票アプリケーションの種類と作成したインスタンスのエントリがあることを確認します。

![Service Fabric Explorer][sfx]

実行中のコンテナーに接続します。  クラスターの URL を指す Web ブラウザーを開きます (`http://linh1x87d1d.westus.cloudapp.azure.com:80` など)。 ブラウザーに Voting アプリケーションが表示されます。

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>クラスター内のコンテナーをフェールオーバーする
障害が発生すると、Service Fabric はクラスター内の他のノードにコンテナー インスタンスを自動的に移動します。 また、コンテナーのノードを手動でドレインして、クラスター内の他のノードに適切に移動することもできます。 サービスをスケーリングするには複数の方法があります。この例では、Service Fabric Explorer を使います。

フロントエンド コンテナーのフェールオーバーは、次の手順で行います。

1. クラスターで Service Fabric Explorer を開きます (例: `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`)。
2. ツリー ビューの **fabric:/Voting/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。 ツリー ビューのノード名に注意してください。この名前は、コンテナーが現在実行しているノードを示します (例: `_nodetype_4`)。
3. ツリー ビューの **Nodes** ノードを展開します。 コンテナーを実行しているノードの横にある省略記号 (3 つのドット) をクリックします。
4. **[再起動]** を選んでそのノードを再起動し、再起動操作を確認します。 再起動により、コンテナーはクラスター内の別のノードにフェールオーバーします。

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする
Service Fabric サービスは、その負荷に対応するように、クラスターで簡単にスケールすることができます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。

Web フロントエンド サービスをスケールするには、次の手順に従います。

1. クラスターで Service Fabric Explorer を開きます (例: `http://linh1x87d1d.westus.cloudapp.azure.com:19080`)。
2. ツリー ビューで **fabric:/Voting/azurevotefront** ノードの横にある省略記号 (3 つの点) をクリックし、**[Scale Service]\(サービスのスケール\)** を選択します。

    ![containersquickstartscale][containersquickstartscale]

  これで Web フロントエンド サービスのインスタンス数をスケールできる状態になりました。

3. この数値を **2** に変更し、**[Scale Service]\(サービスのスケール\)** をクリックします。
4. ツリー ビューの **fabric:/Voting/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。

    ![containersquickstartscaledone][containersquickstartscaledone]

    サービスに 2 つのインスタンスがあることがわかります。 ツリー ビューでは、インスタンスが実行されるノードを確認できます。

たったこれだけの管理タスクにより、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="clean-up"></a>クリーンアップ
クラスターからアプリケーション インスタンスを削除し、その種類のアプリケーションの登録を解除するには、テンプレートに指定されているアンインストール スクリプトを使用します。 このコマンドは、インスタンスをクリーンアップするのに時間がかかるため、このスクリプトの直後に install.sh コマンドを実行することはお勧めしません。 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>次の手順
このクイック スタートでは、次の方法について説明しました。
> [!div class="checklist"]
> * Linux コンテナー アプリケーションの Azure へのデプロイ
> * Service Fabric クラスターでのコンテナーのフェールオーバー
> * Service Fabric クラスターでのコンテナーのスケール

* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
* GitHub で [Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-containers)を確認します。

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
