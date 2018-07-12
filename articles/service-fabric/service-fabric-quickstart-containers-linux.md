---
title: Azure で Service Fabric 上に Linux コンテナー アプリを作成する | Microsoft Docs
description: このクイック スタートでは、アプリケーションの Docker イメージをビルドし、そのイメージをコンテナー レジストリにプッシュした後、Service Fabric クラスターにコンテナーをデプロイします。
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b0ded0fb274f6b64935ddaba75abf23a94063120
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452653"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>クイック スタート: Service Fabric に Linux コンテナーをデプロイする

Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。

このクイックスタートでは、Linux コンテナーを Service Fabric クラスターにデプロイする方法を説明します。 完了すると、Service Fabric クラスターで実行される Python Web フロントエンドと Redis バックエンドで構成される投票アプリケーションが作成されます。 さらに、アプリケーションのフェールオーバー方法と、クラスター内のアプリケーションのスケーリング方法も説明します。

![投票アプリの Web ページ][quickstartpic]

このクイック スタートでは、Azure Cloud Shell の Bash 環境を使用して、Service Fabric CLI コマンドを実行します。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Cloud Shell を実行するのが初めての場合、`clouddrive` ファイル共有を設定するよう求められます。 既定値をそのまま使用できます。または、既存のファイル共有をアタッチできます。 詳細については、「[`clouddrive` ファイル共有を設定する](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share)」を参照してください。

## <a name="get-the-application-package"></a>アプリケーション パッケージの取得

コンテナーを Service Fabric にデプロイするには、個別のコンテナーとアプリケーションを説明する一連のマニフェスト ファイル (アプリケーション定義) が必要です。

Cloud Shell で git を使用して、アプリケーション定義のコピーを複製します。次に、ディレクトリを複製の `Voting` ディレクトリに変更します。

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric クラスターの作成

Azure にアプリケーションをデプロイするには、アプリケーションを実行する Service Fabric クラスターが必要です。 パーティー クラスターには、Service Fabric クラスターをすばやく作成する簡単な方法が用意されています。 パーティー クラスターは、Azure でホストされ、Service Fabric チームによって実行される期間限定の無料 Service Fabric クラスターです。 パーティ クラスターは、アプリケーションをデプロイしてプラットフォームについて学習するために使用できます。 このクラスターでは、ノード間のセキュリティおよびクライアントとノードの間のセキュリティに単一の自己署名証明書が使用されます。

サインインして、[Linux クラスター](http://aka.ms/tryservicefabric)に参加します。 **[PFX]** リンクをクリックして、PFX 証明書をコンピューターにダウンロードします。 **[ReadMe]** リンクをクリックして、証明書パスワードと、証明書を使用するさまざまな環境を構成する方法についての手順を探します。 **ウェルカム** ページと **ReadMe** ページをどちらも開いたままにして、次の手順にある指示のいくつかを使用します。

> [!Note]
> 1 時間あたりに使用可能なパーティ クラスターの数には制限があります。 パーティ クラスターへのサインアップ時にエラーが発生する場合は、少し待ってからやり直してください。または、[Azure での Service Fabric クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)に関するページの手順に従って、サブスクリプションにクラスターを作成することもできます。
>
>独自のクラスターを作成する場合、Web フロントエンド サービスが、ポート 80 で受信トラフィックをリッスンするよう構成されていることに注意します。 このポートがクラスターで開放されていることを確認してください。 (パーティ クラスターを使用した場合、このポートは開放されています。)
>

## <a name="configure-your-environment"></a>環境の構成

Service Fabric には、クラスターとそのアプリケーションを管理するために使用できるツールがいくつか用意されています。

- ブラウザーベースのツールである Service Fabric Explorer。
- Azure CLI 2.0 上で実行される Service Fabric コマンド ライン インターフェイス (CLI)。
- PowerShell コマンド。

このクイック スタートでは、Cloud Shell の Service Fabric CLI と Service Fabric Explorer を使用します。 次のセクションでは、これらのツールを使用してセキュリティで保護されたクラスターに接続するために必要な証明書をインストールする方法について説明します。

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Service Fabric CLI のために証明書を構成する

Cloud Shell で CLI を使用するには、証明書 PFX ファイルを Cloud Shell にアップロードし、それを使用して PEM ファイルを作成する必要があります。

1. Cloud Shell で現在の作業ディレクトリに証明書をアップロードするには、ダウンロード先のコンピューター上のフォルダーから証明書 PFX ファイルをドラッグして、Cloud Shell ウィンドウにドロップします。

2. PFX ファイルを PEM ファイルに変換するには、次のコマンドを使用します  (パーティー クラスターでは、**ReadMe** ページにある手順から PFX ファイルとパスワードに固有のコマンドをコピーできます)。

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ```

### <a name="configure-certificate-for-service-fabric-explorer"></a>Service Fabric Explorer のために証明書を構成する

Service Fabric Explorer を使用するには、パーティ クラスターの Web サイトからダウンロードした証明書 PFX ファイルを、ご使用の証明書ストア (Windows または Mac) あるいはブラウザー本体 (Ubuntu) にインポートする必要があります。 **ReadMe** ページから取得できる PFX 秘密キー パスワードが必要です。

お使いのシステムに証明書をインポートするのに最適な任意の方法を使用します。 例: 

- Windows の場合: PFX ファイルをダブルクリックし、プロンプトに従って個人用ストア `Certificates - Current User\Personal\Certificates` に証明書をインストールします。 または、**ReadMe** 手順の PowerShell コマンドを使用できます。
- Mac の場合: PFX ファイルをダブルクリックし、プロンプトに従ってキーチェーンに証明書をインストールします。
- Ubuntu の場合: Mozilla Firefox は、Ubuntu 16.04 の既定のブラウザーです。 証明書を Firefox にインポートするには、ブラウザーの右上隅にあるメニュー ボタンをクリックし、**[オプション]** をクリックします。 **環境設定**ページで、検索ボックスを使用して "証明書" を検索します。 **[証明書を表示]** をクリックし、**[あなたの証明書]** タブを選択します。次に、**[インポート]** をクリックし、プロンプトに従って証明書をインポートします。

   ![Firefox での証明書のインストール](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Service Fabric アプリケーションをデプロイする

1. Cloud Shell で、CLI を使用して Azure の Service Fabric クラスターに接続します。 エンドポイントは、クラスターの管理エンドポイントです。 前のセクションで PEM ファイルを作成しました  (パーティー クラスターでは、**ReadMe** ページにある手順から PEM ファイルと管理エンドポイントに固有のコマンドをコピーできます)。

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. インストール スクリプトを使用して投票アプリケーション定義をクラスターにコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```

3. Web ブラウザーを開き、クラスターの Service Fabric Explorer エンドポイントに移動します。 エンドポイントの形式は、**https://\<my-azure-service-fabric-cluster-url>:19080/Explorer** です (例: `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`)。 </br>(パーティー クラスターでは、**ウェルカム** ページでクラスターの Service Fabric Explorer エンドポイントを見つけることができます。)

4. **Applications** ノードを展開し、投票アプリケーションの種類と作成したインスタンスのエントリがあることを確認します。

    ![Service Fabric Explorer][sfx]

5. 実行中のコンテナーに接続するには、Web ブラウザーを開いてクラスターの URL に移動します (例: `http://linh1x87d1d.westus.cloudapp.azure.com:80`)。 ブラウザーに Voting アプリケーションが表示されます。

    ![投票アプリの Web ページ][quickstartpic]

> [!NOTE]
> Service Fabric アプリケーションは、Docker Compose でデプロイすることもできます。 たとえば、次のコマンドを使用すれば、Docker Compose を使ってクラスターにアプリケーションをデプロイしてインストールできます。
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>クラスター内のコンテナーをフェールオーバーする

障害が発生すると、Service Fabric は自動的にコンテナー インスタンスをクラスター内の他のノードに移動します。 また、コンテナーのノードを手動でドレインして、クラスター内の他のノードに適切に移動することもできます。 Service Fabric では、いくつかの方法でサービスをスケーリングできます。 次の手順では、Service Fabric Explorer を使用します。

フロントエンド コンテナーのフェールオーバーは、次の手順で行います。

1. クラスターで Service Fabric Explorer を開きます (例: `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`)。
2. ツリー ビューの **fabric:/Voting/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。 ツリー ビューのノード名に注意してください。この名前は、コンテナーが現在実行しているノードを示します (例: `_nodetype_4`)。
3. ツリー ビューの **Nodes** ノードを展開します。 コンテナーを実行しているノードの横にある省略記号 (...) をクリックします。
4. **[再起動]** を選んでそのノードを再起動し、再起動操作を確認します。 再起動により、コンテナーはクラスター内の別のノードにフェールオーバーします。

    ![Service Fabric Explorer のノード ビュー][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする

Service Fabric サービスは、その負荷に対応するように、クラスターで簡単にスケールすることができます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。

Web フロントエンド サービスをスケールするには、次の手順に従います。

1. クラスターで Service Fabric Explorer を開きます (例: `https://linh1x87d1d.westus.cloudapp.azure.com:19080`)。
2. ツリー ビューで **fabric:/Voting/azurevotefront** ノードの横にある省略記号 (3 つの点) をクリックし、**[Scale Service]\(サービスのスケーリング\)** を選択します。

    ![Service Fabric Explorer スケーリング サービスの開始][containersquickstartscale]

    これで Web フロントエンド サービスのインスタンス数をスケールできる状態になりました。

3. この数値を **2** に変更し、**[Scale Service]\(サービスのスケール\)** をクリックします。
4. ツリー ビューの **fabric:/Voting/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。

    ![Service Fabric Explorer スケーリング サービスの完了][containersquickstartscaledone]

    サービスに 2 つのインスタンスがあることがわかります。 ツリー ビューでは、インスタンスが実行されるノードを確認できます。

この簡単な管理タスクを通じて、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

1. クラスターからアプリケーション インスタンスを削除し、アプリケーションの種類の登録を解除するには、テンプレートに指定されているアンインストール スクリプト (uninstall.sh) を使用します。 このスクリプトによってインスタンスがクリーンアップされるまで、しばらく時間がかかります。そのため、このスクリプトの直後にインストール スクリプトを実行しないようにしてください。 Service Fabric Explorer を使用して、インスタンスが削除され、アプリケーションの種類が登録解除された時間を確認できます。

    ```bash
    ./uninstall.sh
    ```

2. クラスターの操作が完了したら、証明書ストアから証明書を削除できます。 例: 
   - Windows の場合: [証明書 MMC スナップイン](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)を使用します。 スナップインの追加時に **[ユーザー アカウント]** を選択してください。 `Certificates - Current User\Personal\Certificates` に移動して、証明書を削除します。
   - Mac の場合: キーチェーン アプリを使用します。
   - Ubuntu の場合: 証明書の表示に使用される手順に従ってから、証明書を削除します。

3. Cloud Shell を引き続き使用しない場合、課金されないようにするために、関連付けられているストレージ アカウントを削除できます。 Cloud Shell セッションを閉じます。 Azure Portal で、Cloud Shell に関連付けられているストレージ アカウントをクリックします。次に、ページの上部にある **[削除]** をクリックしてプロンプトに応答します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Linux コンテナー アプリケーションを Azure の Service Fabric クラスターにデプロイし、アプリケーションのフェールオーバーを実行して、クラスター内のアプリケーションをスケーリングしました。 Service Fabric で Linux コンテナーを操作する方法の詳細については、Linux コンテナー アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Linux コンテナー アプリの作成](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
