---
title: ソースから Azure Red Hat OpenShift にアプリケーションをデプロイする
description: Source-to-Image (S2I) ビルド戦略を使用して、ソース コードから Azure Red Hat OpenShift にアプリケーションをデプロイする方法について説明します
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro、OpenShift、Red Hat、s2i、Source to Image
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558701"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>ソースから Azure Red Hat OpenShift にアプリケーションをデプロイする

この記事では、source-to-image (S2I) ビルドを使用して、ソース コードから Azure Red Hat OpenShift クラスターにアプリケーションをデプロイします。 [Source-to-Image (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) は、ソース コードから再現可能なコンテナー イメージを作成するためのビルド プロセスです。 S2I では、コンテナー　イメージにソース コードを挿入し、コンテナーでそのソース コードの実行を準備できるようにすることで、実行の準備が完了したイメージが作成されます。 OpenShift でソースからアプリケーションをビルドしてデプロイできます。そのため、変更のたびにコンテナーを手動で作成する必要はありません。 ソース コードの変更が通知された場合、OpenShift によって新しいバージョンが自動的にビルドされ、デプロイできます。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>プロジェクトを作成する

`demoproject` という名前の新しいプロジェクトを作成するために、コマンドを実行します。

```azurecli-interactive
oc new-project demoproject
```

次のような出力が表示されます。

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>Web コンソールを起動する

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

左側のメニューで、 *[管理者]* パースペクティブではなく *[開発者]* パースペクティブに切り替え、プロジェクトの一覧から `demoproject` を選択します。 これにより、プロジェクトの *[トポロジ]* ページが表示されます。

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift プロジェクトのトポロジ":::

プロジェクトは空であるため、ワークロードは存在せず、アプリケーションのデプロイ方法に関するさまざまなオプションが表示されます。

## <a name="deploying-using-the-web-console"></a>Web コンソールを使用してデプロイする

表示されたアプリケーションをデプロイするためのオプションから、 *[From Git]\(Git から\)* を選択します。 これにより、 *[Import from Git]\(Git からのインポート\)* ページが表示されます。 **[Git Repo URL]\(Git リポジトリ URL\)** として `https://github.com/sclorg/django-ex.git` を使用します。 サンプルの Web アプリケーションは、Python プログラミング言語を使用して実装されています。

:::image type="content" source="media/s2i/from-git.png" alt-text="Git からの Azure Red Hat OpenShift プロジェクト":::

> [!NOTE]
> OpenShift では、これが Python プロジェクトであることが検出され、適切なビルダー イメージが選択されます。

下にスクロールして *[詳細オプション]* を表示し、 **[Create a route to the application]\(アプリケーションへのルートを作成する\)** がオンになっていることを確認します。 この操作によって、OpenShift ルートが作成されます。これは、外部から到達可能なホスト名を付与することによってサービスを公開する方法です。

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Git からの Azure Red Hat OpenShift プロジェクト - ルートの設定":::

準備が完了したら、ページの下部にある **[作成]** をクリックします。 これにより、アプリケーションのビルドとデプロイを管理するためのリソースが作成されます。 その後、プロジェクトのトポロジの概要にリダイレクトされます。

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Git からの Azure Red Hat OpenShift プロジェクト - トポロジ":::

トポロジの概要では、デプロイしたアプリケーションが視覚的表現が表示されます。 このビューで、全体的なアプリケーション構造を確認できます。

Git アイコンをクリックすると、アプリケーションのソース コードのビルド元である Git リポジトリに移動できます。 左下に表示されるアイコンは、アプリケーションのビルドの状態を示しています。 このアイコンをクリックすると、ビルドの詳細セクションに移動します。 アプリケーションがルートを公開している場合、右上のアイコンをクリックすると、作成されたアプリケーション ルートの URL を開くことができます。

アプリケーションのスケールアップまたはスケールダウン、ロールアウトの開始、ポッドの再作成が行われると、トポロジ ビューのアプリケーション表現がアニメーション化され、実行されている処理がリアルタイムに表示されます。

アプリケーション アイコンをクリックすると、下のような詳細が表示されます。

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Git からの Azure Red Hat OpenShift プロジェクト - 詳細":::

## <a name="viewing-the-builder-logs"></a>ビルダーのログを表示する

ビルドが開始されたら、 *[リソース]* パネルに表示されている *[ログの表示]* リンクをクリックします。

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Git からの Azure Red Hat OpenShift プロジェクト - ビルド ログ":::

これにより、ビルドの実行中にその進行状況を監視できます。 ビルダー イメージ (この例では Python) によって、アプリケーション ソース コードが最終イメージに挿入された後、それが OpenShift 内部イメージ レジストリにプッシュされます。 "Push successful (プッシュが成功しました)" という最終メッセージが表示されたら、ビルドは正常に完了したことになります。

## <a name="accessing-the-application"></a>アプリケーションにアクセスする

アプリケーション イメージのビルドが完了すると、それがデプロイされます。

左側のメニュー バーで *[トポロジ]* をクリックして、プロジェクトのトポロジ ビューに戻ります。 Web コンソールを使用してアプリケーションを作成した場合、アプリケーションの *ルート* が自動的に作成され、クラスターの外部に公開されます。 Web ブラウザーからアプリケーションにアクセスするために使用できる URL は、前に表示した、アプリケーションの *[リソース]* タブで確認できます。

トポロジ ビューで、リングの右上のアイコンをクリックすることで、デプロイされたアプリケーションの URL を取得できます。 デプロイが完了し、アイコンをクリックすると、デプロイしたアプリケーションが表示されます。

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Git からの Azure Red Hat OpenShift プロジェクト - アプリの参照":::

## <a name="deploying-using-the-command-line"></a>コマンドラインを使用してデプロイする

Web コンソールを使用してアプリケーションをデプロイする方法を学習しました。次も同じ Web アプリケーションをデプロイしますが、今回は `oc` コマンドライン ツールを使用します。

次のコマンドを実行してプロジェクトを削除し、最初からやり直します。

```azurecli-interactive
oc delete project demoproject
```

`demoproject` が削除されたことを示す確認メッセージが表示されます。

```output
project.project.openshift.io "demoproject" deleted
```

以下を実行して、`demoproject` を再度作成します。

```azurecli-interactive
oc new-project demoproject
```

プロジェクト内で、提供されている最新バージョンの Python の S2I ビルダーを指定して、GitHub のソースから新しいアプリケーションを作成します。

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

これにより、次のような出力が表示されます。

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift では、アプリケーションの名前として Git リポジトリの名前が使用されます。 以下を実行して、ビルドとデプロイの状態を確認します。

```azurecli-interactive
oc status
```

ビルドとデプロイが完了すると、次のような出力が表示されます。

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

OpenShift クラスターの外部でアプリケーションを公開するために、以下を実行してルートを作成する必要があります。

```azurecli-interactive
oc expose service/django-ex
```

確認メッセージが表示されます。

```output
route.route.openshift.io/django-ex exposed
```

以下を実行して URL を取得します。

```azurecli-interactive
oc get route django-ex
```

デプロイされたアプリケーションを参照するために使用できる、ルートに割り当てられたホスト名を取得する必要があります。

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>新しいバイナリ ビルドをトリガーする

アプリケーションで作業するときには、変更を行い、それらの変更がデプロイされたことを確認する必要があります。 コードがコミットされるたびに新しいビルドとデプロイをトリガーする Webhook を簡単に設定できます。 ただし、コードのすべての変更をリポジトリにプッシュせずに変更を確認したい場合もあるため、これが望ましくない場合もあります。

変更を頻繁に行う場合、いわゆるバイナリ ビルドを使用できます。 このシナリオを例示するために、以下を実行して、アプリケーションの Git リポジトリをローカルで複製します。

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

これにより、アプリケーションのソース コードを含むサブディレクトリ `django-ex` が作成されます。

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

このサブディレクトリに移動します。

```azurecli-interactive
cd django-ex
```

統合 Azure Cloud Shell エディターを開きます。

```azurecli-interactive
code welcome/templates/welcome/index.html
```

下にスクロールし、`Welcome to your Django application on OpenShift` という行を `Welcome to Azure Red Hat OpenShift` に変更します。 ファイルを保存し、右上の `...` メニューからエディターを閉じます。

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Git からの Azure Red Hat OpenShift プロジェクト - Azure Cloud Shell エディターでのアプリケーションの編集":::

コマンドを実行して、新しいビルドを開始します。

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

`--from-dir=.` フラグを渡すと、OpenShift コマンドラインで、指定されたディレクトリからソース コードがアップロードされ、ビルドとデプロイのプロセスが開始されます。 下のような出力が表示され、数分後にビルドが完了します。

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

アプリケーションを使用してブラウザーを更新すると、更新されたタイトルが表示されます。

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Git からの Azure Red Hat OpenShift プロジェクト - 更新されたアプリの参照":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションの操作が完了したら、次のコマンドを実行してプロジェクトを削除できます。

```azurecli-interactive
oc delete project demoproject
```

「[チュートリアル: Azure Red Hat OpenShift 4 クラスターを削除する](./tutorial-delete-cluster.md)」の手順に従って、クラスターを削除することもできます。

## <a name="next-steps"></a>次のステップ

このガイドでは、以下の方法について学習しました。
> [!div class="checklist"]
>
> * プロジェクトを作成する
> * Web コンソールを使用してソース コードからアプリケーションをデプロイする
> * OpenShift コマンド ラインを使用してソース コードからアプリケーションをデプロイする
> * OpenShift コマンド ラインを使用してバイナリ ビルドをトリガーする

Source-to-Image 戦略と[その他のビルド戦略](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)を使用してアプリケーションをビルドおよびデプロイする方法について詳しく確認します。
