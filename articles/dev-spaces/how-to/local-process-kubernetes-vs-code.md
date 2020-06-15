---
title: Visual Studio Code を使用して Local Process with Kubernetes を利用する (プレビュー)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Local Process with Kubernetes を使用して開発用コンピューターを Kubernetes クラスターと Azure Dev Spaces に接続する方法について説明します
keywords: Local Process with Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316463"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Visual Studio Code を使用して Local Process with Kubernetes を利用する (プレビュー)

Local Process with Kubernetes を使用すると、開発用のコンピューター上でコードの実行とデバッグを行いながら、残りのアプリケーションやサービスが置かれている Kubernetes クラスターとの接続を保つことができます。 たとえば、多数のサービスやデータベースが相互に依存している大規模なマイクロサービス アーキテクチャがある場合、開発用コンピューター上のそれらの依存関係をレプリケートするのは困難な場合があります。 さらに、内部ループの開発中にコードを変更するたびにコードをビルドして Kubernetes クラスターにデプロイすると、遅くなり、時間がかかり、デバッガーで使用するのが難しくなることがあります。

Local Process with Kubernetes を使用すると、代わりに開発用コンピューターとクラスターの間に直接接続が作成されることにより、コードをビルドしてクラスターにデプロイする必要がなくなります。 デバッグ中に開発用コンピューターをクラスターに接続すると、Docker または Kubernetes の構成を作成することなく、完全なアプリケーションのコンテキストで、サービスを迅速にテストおよび開発できます。

Local Process with Kubernetes では、接続された Kubernetes クラスターと開発用コンピューターの間でトラフィックがリダイレクトされます。 このトラフィックのリダイレクトにより、開発用コンピューター上のコードと、Kubernetes クラスターで実行されているサービスは、同じ Kubernetes クラスター内に存在するかのように通信できます。 Local Process with Kubernetes には、開発用コンピューターの Kubernetes クラスター内のポッドに利用できる環境変数とマウントされたボリュームをレプリケートする方法も用意されています。 開発用コンピューター上の環境変数およびマウントされたボリュームにアクセスできると、それらの依存関係を手動でレプリケートすることなく、コードの作業をすばやく行うことができます。

このガイドでは、Local Process with Kubernetes を使用して、Kubernetes クラスターと、開発用コンピューターで実行されているコードの間で、トラフィックをリダイレクトする方法について説明します。 また、このガイドでは、Kubernetes クラスター上の複数のマイクロサービスを含む大規模なサンプル アプリケーションをデプロイするためのスクリプトも提供します。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件][preview-terms]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="before-you-begin"></a>開始する前に

このガイドでは、[Azure Dev Spaces 自転車共有サンプル アプリケーション][bike-sharing-github]を使用して、開発用コンピューターを Kubernetes クラスターに接続する方法を示します。 Kubernetes クラスター上で実行されている独自のアプリケーションが既にある場合でも、以下の手順に従い、独自のサービスの名前を使用できます。

### <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること][azure-cli]。
* [Visual Studio Code][vs-code] が macOS または Windows 10 で実行されていること。
* [Azure Dev Spaces][azds-vs-code] 拡張機能バージョン 2.0.220200601 以降が Visual Studio Code にインストールされていること。
* [Azure Dev Spaces CLI がインストールされていること][azds-cli]。

## <a name="create-a-kubernetes-cluster"></a>Kubernetes クラスターを作成する

[サポートされているリージョン][supported-regions]に AKS クラスターを作成します。 下記のコマンドを使用すると、*MyResourceGroup* というリソース グループと *MyAKS* という AKS クラスターが作成されます。

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>サンプル アプリケーションをインストールする

提供されているスクリプトを使用して、サンプル アプリケーションをクラスターにインストールします。 このスクリプトは、開発用コンピューター上で実行するか、[Azure Cloud Shell][azure-cloud-shell] を使用して実行することができます。

> [!IMPORTANT]
> スクリプトを実行するには、クラスターに対する "*所有者*" または "*共同作成者*" のアクセス許可が必要です。

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

パブリック URL を開いて、クラスターで実行されているサンプル アプリケーションに移動します。この URL は、インストール スクリプトの出力に表示されます。

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

上の例では、パブリック URL は `dev.bikesharingweb.EXTERNAL_IP.nip.io` です。

## <a name="connect-to-your-cluster-and-debug-a-service"></a>クラスターに接続してサービスをデバッグする

開発用コンピューターで、[az aks get-credentials][az-aks-get-credentials] を使用して、Kubernetes CLI をダウンロードし、Kubernetes クラスターに接続するように構成します。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Visual Studio Code で [Azure Dev Spaces 自転車共有サンプル アプリケーション][bike-sharing-github]から *dev-spaces/samples/BikeSharingApp/Bikes* を開きます。 Azure Kubernetes Service 拡張機能を開き、*MyAKS* クラスターで *dev* 名前空間を選択します。

![名前空間を選択する](../media/local-process-kubernetes-vs-code/select-namespace.png)

`npm install` コマンドを使用して、アプリケーションの依存関係をインストールします。

```console
npm install
```

左側にある *[デバッグ]* アイコンを選択し、上部にある *[Local Process with Kubernetes (Preview)]\(Local Process with Kubernetes (プレビュー)\)* を選択します。

![Local Process with Kubernetes を選択する](../media/local-process-kubernetes-vs-code/choose-local-process.png)

*[Local Process with Kubernetes (Preview)]\(Local Process with Kubernetes (プレビュー)\)* の横にある開始ボタンをクリックします。 この起動構成を初めて実行すると、置き換えるサービス、開発用コンピューターから転送するためのポート、使用する起動タスクを構成するように求められます。

*bikes* サービスを選択します。

![サービスを選択する](../media/local-process-kubernetes-vs-code/choose-service.png)

*bikes* サービスに対する Kubernetes クラスター内のすべてのトラフィックが、開発用コンピューターで実行されているアプリケーションのバージョンにリダイレクトされます。 Local Process with Kubernetes ではまた、アプリケーションからのすべての送信トラフィックが、Kubernetes クラスターにルーティングされます。

> [!IMPORTANT]
> リダイレクトできるのは、ポッドが 1 つのサービスだけです。

サービスを選択した後は、ローカル アプリケーション用の TCP ポートの入力を求められます。 この例では、「*3000*」と入力します。

![接続用のポートを選択する](../media/local-process-kubernetes-vs-code/choose-port.png)

起動タスクとして *[NPM による起動]* を選択します。

![起動タスクを選択する](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> *KubernetesDNSManager* が管理者特権で実行して hosts ファイルを変更することを許可するように求められます。

ステータス バーがオレンジに変わり、Dev Spaces 拡張機能に接続されたことが示されたら、開発用コンピューターは接続されています。

![接続された開発用コンピューター](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> 以降の起動では、サービス名、ポート、または起動タスクの指定を求められることはありません。 これらの値は、 *.vscode/tasks.json* に保存されています。

開発用コンピューターが接続されると、置き換えているサービスのトラフィックの、開発用コンピューターに対するリダイレクトが開始されます。

## <a name="set-a-break-point"></a>ブレーク ポイントを設定する

[server.js][server-js-breakpoint] を開き、233 行目のどこかをクリックして、カーソルをそこに置きます。 *F9* キーを押すか、 *[実行]* 、 *[ブレークポイントの設定/解除]* の順にクリックして、ブレークポイントを設定します。

パブリック URL を開いて、サンプル アプリケーションに移動します。 ユーザーとして *Aurelia Briggs (顧客)* を選択してから、借りる自転車を選択します。 自転車の画像が読み込まれないことにご注意ください。 Visual Studio Code に戻って、233 行目が強調表示されていることを確認します。 設定したブレークポイントによって、233 行目でサービスが一時停止されました。 サービスを再開するには、*F5* キーを押すか、 *[実行]* 、 *[続行]* の順にクリックします。 ブラウザーに戻り、自転車のプレースホルダー画像が表示されていることを確認します。

`server.js` の 233 行目にカーソルを置いて *F9* キーを押すことで、ブレークポイントを削除します。

### <a name="update-your-application"></a>アプリケーションの更新

`server.js` を編集し、行 234 および 235 を削除します。

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

これでセクションは、次のようになります。

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

変更を保存し、 *[実行]* をクリックし、 *[デバッグの再起動]* をクリックします。 再接続された後、ブラウザーを更新し、自転車のプレースホルダー画像が消えたことを確認します。

*[実行]* 、 *[デバッグの停止]* の順にクリックして、デバッガーを停止します。

> [!NOTE]
> 既定では、デバッグ タスクを停止すると、開発用コンピューターも Kubernetes クラスターから切断されます。 この動作を変更できます。そのためには、 *[Local Process With Kubernetes: Disconnect After Debugging]\(Local Process With Kubernetes: デバッグ後の切断\)* を Visual Studio Code の設定で探し、 *[Disconnect automatically when Debugging ends]\(デバッグが終了したら自動的に切断する\)* のチェック ボックスをオフにします。 この設定を更新した後は、デバッグを停止して開始するとき、開発用コンピューターは接続されたままになります。 開発用コンピューターをクラスターから切断するには、ステータスバーの Azure Dev Spaces 拡張機能をクリックし、 *[Disconnect current session]\(現在のセッションを切断する\)* を選択します。
>
> Visual Studio Code がクラスターへの接続を突然終了した場合、または Visual Studio Code が終了した場合は、Local Process with Kubernetes に接続する前に、リダイレクトしているサービスが元の状態に戻らない可能性があります。 この問題を解決するには、[トラブルシューティング ガイド][troubleshooting]のページを参照してください。

## <a name="using-logging-and-diagnostics"></a>ログ記録と診断の使用

開発用コンピューターが Kubernetes クラスターに接続された後、*Dev Spaces* ウィンドウにログ出力が書き込まれます。

![出力](../media/local-process-kubernetes-vs-code/output.png)

Azure Dev Spaces のステータス バーをクリックし、 *[Show connection diagnostics information]\(接続診断情報を表示する\)* を選択します。 このコマンドを実行すると、ログ出力に現在の環境変数と DNS エントリが出力されます。

![診断付きの出力](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

また、この診断ログは、ご利用の[開発用コンピューターの *TEMP* ディレクトリ][azds-tmp-dir]にある `Azure Dev Spaces` ディレクトリにあります。

## <a name="remove-the-sample-application-from-your-cluster"></a>クラスターからサンプル アプリケーションを削除する

クラスターからサンプル アプリケーションを削除するには、提供されているスクリプトを使用します。

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces と GitHub Actions を使用し、プル要求をリポジトリのメイン ブランチに結合する前に、AKS で直接、プル要求から変更をテストする方法を学習します。

> [!div class="nextstepaction"]
> [GitHub Actions と Azure Kubernetes Service][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download