---
title: OpenShift サーバーレスを使用して Azure Red Hat OpenShift にアプリケーションをデプロイする
description: OpenShift サーバーレスを使用して Azure Red Hat OpenShift にアプリケーションをデプロイする方法について説明します
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro、OpenShift、Red Hat、サーバーレス
ms.openlocfilehash: 0cd6ac8ae31c43bf1fe1be1d42ec27f183512442
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110063628"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>OpenShift サーバーレスを使用して Azure Red Hat OpenShift にアプリケーションをデプロイする

この記事では、[OpenShift サーバーレス](https://www.openshift.com/learn/topics/serverless)を使用して、Azure Red Hat OpenShift クラスターにアプリケーションをデプロイします。 OpenShift サーバーレスを使用すると、開発者は、オンデマンドでスケールアップまたはゼロにスケーリングされるアプリケーションをデプロイして実行できます。これにより、不使用時のリソースの消費を回避できます。

アプリケーション コードは適切なランタイムと共にコンテナーにパッケージ化できます。イベントによってアプリケーション コンテナーがトリガーされると、サーバーレス機能によってそれらが開始されます。 独自のアプリケーションからのイベント、複数のプロバイダーからのクラウド サービス、サービスとしてのソフトウェア (SaaS) システム、その他のサービスなど、さまざまなイベント ソースによってアプリケーションをトリガーできます。

サーバーレス方式でのコンテナーのデプロイのあらゆる側面を、OpenShift インターフェイスで直接管理できます。 開発者は、イベント パラメーターを変更する複数の方法を使用して、コンテナー化されたアプリケーションの起動をトリガーするイベントを視覚的に特定できます。 OpenShift サーバーレス アプリケーションは、OpenShift Pipelines、Service Mesh、Monitoring など、他の OpenShift サービスと統合できます。これにより、完全なサーバーレス アプリケーションの開発とデプロイのエクスペリエンスが提供されます。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Knative コマンドライン ツール (kn) をインストールする

<https://github.com/knative/client/releases/> から、お使いのマシンに適した CLI の最新リリースをダウンロードできます。

Azure Cloud Shell でコマンドを実行する場合は、Linux 用の最新の Knative CLI をダウンロードしてください。

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>Web コンソールを起動する

以下を実行して、クラスターの Web コンソールの URL を見つけます。

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

次のような URL が見つかります。

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

ブラウザーでコンソールの URL にアクセスし、`kubeadmin` 資格情報を使用してログインします。

:::image type="content" source="media/login.png" alt-text="Azure Red Hat OpenShift ログイン画面":::

## <a name="install-the-openshift-serverless-operator"></a>OpenShift サーバーレス オペレーターをインストールする

OpenShift Web コンソールにログインしたら、 *[管理者]* パースペクティブであることを確認します。 *[Operator Hub]\(オペレーター ハブ\)* を開き、 **[OpenShift Serverless]\(OpenShift サーバーレス\)** オペレーターを見つけ、それを選択します。

![OpenShift サーバーレス オペレーターを見つける](media/serverless/serverless-operatorhub.png)

次に、 **[インストール]** をクリックして、オペレーターのインストール ページを開きます。

![[インストール] をクリックしてオペレーターをインストールする](media/serverless/serverless-clickinstall.png)

Azure Red Hat OpenShift のクラスター バージョンに適した "*更新チャネル*" を選択し、`openshift-serverless` 名前空間にオペレーターをインストールします。 下にスクロールして、 **[インストール]** をクリックします。

![オペレーターのインストール ページ](media/serverless/serverless-installpage.png)

オペレーターがインストールされ、使用できる状態になっていることが数分で状態ページに反映されます。 **[オペレーターの表示]** ボタンをクリックして続行します。

![オペレーターがインストールされ、使用できる状態になる](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Knative サービスをインストールする

OpenShift サーバーレスで任意のコンテナーをサーバーレス方式で実行する機能は、アップストリーム Knative を使用することで可能になります。 Knative では、Kubernetes を拡張することにより、サーバーレス方式を使用して最新のアプリケーションをデプロイ、実行、管理するための一連のコンポーネントが提供されています。

### <a name="create-an-instance-of-the-knative-serving"></a>Knative サービスのインスタンスを作成する

左上にあるプロジェクトのドロップダウン リストをクリックして `knative-serving` 名前空間に切り替え、その後、 *[Provided APIs]\(提供されている API\)* の下の *[Knative Serving]\(Knative サービス\)* カードで **[Create Instance]\(インスタンスの作成\)** をクリックします。

![クリックして Knative サービス インスタンスを作成する](media/serverless/serverless-createknativeserving.png)

既定値はそのままにし、 *[Create Knative Serving]\(Knative サービスの作成\)* ページで下にスクロールし、 **[作成]** ボタンをクリックします。

![既定値はそのままにし、[作成] をクリックする](media/serverless/serverless-createknativeserving2.png)

*[状態]* 列に **[Ready]\(準備完了\)** と表示されるまで待機します。その後、OpenShift サーバーレスがインストールされ、OpenShift サーバーレス プロジェクトを作成する準備が整います。

![Knative サービスの準備が完了](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>サーバーレス プロジェクトを作成する

`demoserverless` という名前の新しいプロジェクトを作成するために、コマンドを実行します。

```azurecli-interactive
oc new-project demoserverless
```

次のような出力が表示されます。

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

左側のメニューで、 *[管理者]* パースペクティブではなく *[開発者]* パースペクティブに切り替え、プロジェクトの一覧から `demoserverless` を選択します。 これにより、プロジェクトの *[トポロジ]* ページが表示されます。

![Azure Red Hat OpenShift プロジェクトのトポロジ](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Web コンソールを使用してデプロイする

表示されたアプリケーションをデプロイするためのオプションから、 *[From Git]\(Git から\)* を選択します。 これにより、 *[Import from Git]\(Git からのインポート\)* ページが表示されます。 **[Git Repo URL]\(Git リポジトリ URL\)** として `https://github.com/sclorg/django-ex.git` を使用します。 サンプルの Web アプリケーションは、Python プログラミング言語を使用して実装されています。

![Git からの Azure Red Hat OpenShift プロジェクト](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift で、これが Python プロジェクトであることが検出され、適切なビルダー イメージが選択されます。

*[リソース]* まで下にスクロールし、生成するリソースの種類として **[Knative Service]\(Knative サービス\)** が選択されていることを確認します。 この操作により、Knative サービスが作成されます。これは、アイドル状態のときに OpenShift サーバーレスのゼロへのスケーリングを有効にするデプロイの種類です。

![Azure Red Hat OpenShift プロジェクト - Knative](media/serverless/serverless-knative.png)


準備が完了したら、ページの下部にある **[作成]** をクリックします。 これにより、アプリケーションのビルドとデプロイを管理するためのリソースが作成されます。 その後、プロジェクトのトポロジの概要にリダイレクトされます。

トポロジの概要では、デプロイしたアプリケーションの視覚的表現が表示されます。 このビューで、全体的なアプリケーション構造を確認できます。

ビルドが完了するまで待ちます。 これには数分かかることがあります。 ビルドが完了すると、サービスの左下隅に緑色のチェックマークが表示されます。

![Azure Red Hat OpenShift プロジェクトのトポロジ - ビルド成功](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>アプリケーションの規模を確認する

[トポロジ] ビューの上部にある *[表示オプション]* の一覧で、 *[Pod Count]\(ポッド数\)* をクリックします。 ポッド数がゼロにスケールダウンされるまで待機します。 スケールダウンには数分かかることがあります。

![Azure Red Hat OpenShift プロジェクトのトポロジ - ゼロにスケーリング](media/serverless/serverless-scaledtozero.png)

[Knative Service]\(Knative サービス\) パネルの右上隅にある *[URL を開く]* アイコンをクリックします。 アプリケーションが新しいタブで開きます。新しいブラウザー タブを閉じて、[トポロジ] ビューに戻ります。 [トポロジ] ビューで、要求に合わせてアプリケーションがポッド数 1 にスケールアップされたことを確認できます。 数分後、アプリケーションはポッド数ゼロに戻るようスケールダウンされます。

![Azure Red Hat OpenShift プロジェクトのトポロジ - 1 にスケーリング](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>新しいリビジョンを強制し、トラフィックの分散を設定する

サービスの構成が更新されるたびに、サービスの新しいリビジョンが作成されます。 サービス ルートでは、既定で最新の準備完了のリビジョンへのすべてのトラフィックが指されます。 この動作を変更するには、トラフィックの一部を取得するリビジョンを定義します。 Knative サービスでは、トラフィックをマッピングできます。つまり、サービスのリビジョンを、割り当てられた一部のトラフィックにマップできます。 トラフィック マッピングでは、特定のリビジョンに対して固有の URL を作成するオプションも利用できます。

*[トポロジ]* で、サービス内のリビジョンをクリックすると、その詳細が表示されます。 ポッド リングの下にあるバッジと詳細パネルの上部にあるバッジは `(REV)` となります。 サイド パネルの *[リソース]* タブ内で、下にスクロールし、サービスに関連付けられている構成をクリックします。

![Azure Red Hat OpenShift プロジェクトのトポロジ - リビジョン](media/serverless/serverless-revdetails.png)

*[YAML]* タブに切り替えて、下へスクロールし、`timeoutSeconds` の値を `301` に編集することで、構成の更新を強制します。 **[保存]** をクリックして、更新した構成を保存します。 実際のシナリオでは、このような構成の更新はコンテナー イメージ タグの更新によってもトリガーできます。

![構成を更新して新しいリビジョンを強制する](media/serverless/serverless-confupdate.png)

*[トポロジ]* ビューに戻ると、新しいリビジョンが展開されています。 バッジ `(KSVC)` で終わるサービスをクリックし、 **[Set Traffic Distribution]\(トラフィック分散の設定\)** ボタンをクリックすると、サービスの異なるリビジョン間でトラフィックを分割できるようになります。

![トラフィックの分散を設定する](media/serverless/serverless-trafficdist.png)

*[トポロジ]* ビューで、2 つのリビジョン間でトラフィックがどのように分散されているかが示されるようになります。

![トラフィックの分散を確認する](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Knative コマンドライン ツール (kn) を使用する

前の手順では、OpenShift Web コンソールを使用して、アプリケーションを作成し、OpenShift サーバーレスにデプロイしました。 OpenShift サーバーレスでは表面下で Knative が実行されているため、Knative コマンドライン ツール (kn) を使用して Knative サービスを作成することもできます。

> [!NOTE]
> `kn` CLI をまだインストールしていない場合は、この記事の「前提条件」セクションの手順に従ってください。 また、OpenShift コマンド ライン ツール `oc` を使用してログインしていることも確認してください。

`quay.io/rhdevelopers/knative-tutorial-greeter` で既にビルドされているコンテナー イメージを使用します。

### <a name="deploy-a-service"></a>サービスをデプロイする

サービスをデプロイするには、次のコマンドを実行します。

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

下のような出力が表示されます。

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

以下を実行することで、プロジェクトのルートの一覧を取得できます。

```azurecli-interactive
kn route list
```

名前空間内のルートの一覧が取得されます。 ブラウザーで URL を開いて、デプロイされたサービスを表示します。

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>サービスの新しいバージョンをデプロイする

下のコマンドを実行し、`:latest` イメージ タグと環境変数 `MESSAGE_PREFIX` を渡して、アプリケーションの新しいバージョンをデプロイします。

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

新しいリビジョン `greeter-v2` がデプロイされたことを示す確認メッセージが表示されます。

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

すべてのリビジョンとそのトラフィック分散の一覧を表示するには、次のコマンドを実行します。

```azurecli-interactive
kn revision list
```

下のような一覧が表示されます。 新しいリビジョンでは、トラフィックの 100% が取得されることに注意してください。

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>ブルー/グリーン デプロイとカナリア デプロイ

既定では、新しいリビジョンがデプロイされると、トラフィックの 100% が取得されます。 アプリケーションの以前のバージョンにすばやくロールバックできるブルー/グリーン デプロイ戦略を実装するとします。 Knative を使用すると、これを簡単に実現できます。

サービスを更新して 3 つのトラフィック タグを作成し、トラフィックの 100% を以下に送信します。

- **current**: 現在デプロイされているバージョンを指します
- **prev**: 以前のバージョンを指します
- **latest**: 常に最新バージョンを指します

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

下のような確認メッセージが表示されます。

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

下のコマンドを使用して、ルートの一覧を表示します。

```azurecli-interactive
kn route describe greeter
```

各タグの URL とトラフィック分散を示す出力が表示されます。

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

以前のバージョンにすばやくロールバックしたい場合は、トラフィック分散を更新して、以前のタグにトラフィックの 100% を送信します。

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

下のコマンドを使用してルートの一覧を表示し、もう一度確認します。

```azurecli-interactive
kn route describe greeter
```

トラフィック分散の 100% が以前のバージョンに送られていることを示す出力が表示されます。

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

ブラウザーでメイン ルート (この例では `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io`) を更新し、トラフィック分散を試します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションの操作が完了したら、次のコマンドを実行してプロジェクトを削除できます。

```azurecli-interactive
oc delete project demoserverless
```

「[チュートリアル: Azure Red Hat OpenShift 4 クラスターを削除する](./tutorial-delete-cluster.md)」の手順に従って、クラスターを削除することもできます。

## <a name="next-steps"></a>次のステップ

このガイドでは、以下の方法について学習しました。
> [!div class="checklist"]
>
> * OpenShift サーバーレス オペレーターと Knative サービスをインストールする
> * Web コンソールを使用してサーバーレス プロジェクトをデプロイする
> * Knative CLI (kn) を使用してサーバーレス プロジェクトをデプロイする
> * Knative CLI (kn) を使用してブルー/グリーン デプロイとカナリア デプロイを構成する

[OpenShift サーバーレス](https://www.openshift.com/learn/topics/serverless)を使用して、サーバーレスのイベント ドリブン アプリケーションを作成し、Azure Red Hat OpenShift にデプロイする方法の詳細については、[OpenShift サーバーレスの概要](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html)に関するドキュメント、および[サーバーレス アプリケーションの作成と管理](https://docs.openshift.com/container-platform/4.5/serverless/serving-creating-managing-apps.html)に関するドキュメントに従ってください。
