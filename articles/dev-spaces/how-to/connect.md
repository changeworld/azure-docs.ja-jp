---
title: 開発用コンピューターを AKS クラスターに接続する (プレビュー)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Azure Dev Spaces を使用して AKS クラスターに開発用コンピューターを接続する方法について説明します
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235014"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>開発用コンピューターを AKS クラスターに接続する (プレビュー)

Azure Dev Spaces を使用すると、開発用コンピューター上のコンテナーの有無にかかわらずコードを実行およびデバッグできるだけでなく、残りのアプリケーションやサービスを使用して Kubernetes クラスターに接続できます。 開発用コンピューターをクラスターに接続すると、Docker または Kubernetes の構成を作成しなくても、アプリケーションを短期間で開発し、エンドツーエンドのテストを実行できます。 同じクラスターを使用している他のワークロードやユーザーに影響を与えずに、AKS クラスターに接続することもできます。

Azure Dev Spaces では、接続された AKS クラスターと開発用コンピューターの間でトラフィックがリダイレクトされます。 このトラフィックのリダイレクトにより、AKS クラスターで実行されている開発用コンピューターとサービスのコードが、同じ AKS クラスター内にあるかのように通信できます。 開発用コンピューターでコードを実行しているため、そのコードの実行とデバッグに使用している開発ツールにも柔軟性が与えられます。 Azure Dev Spaces には、開発用コンピューターの AKS クラスター内のポッドに利用できる環境変数とマウントされたファイルをレプリケートする方法も用意されています。

このガイドでは、以下の方法について説明します。

* Azure のマネージド Kubernetes クラスターで Azure Dev Spaces をセットアップする。
* 複数のマイクロサービスを伴う大規模アプリケーションを開発空間にデプロイする。
* Azure Dev Spaces を使用し、AKS クラスターと開発用コンピューターで実行されているコードの間でトラフィックをリダイレクトする。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="before-you-begin"></a>開始する前に

このガイドでは、[Azure Dev Spaces 自転車共有サンプル アプリケーション](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)を使用し、開発用コンピューターを AKS クラスターに接続する方法を示します。 サンプル アプリケーションを実行するには、[Azure Dev Spaces 自転車共有サンプル アプリケーション README](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) に記載されている手順に従ってください。 あるいは、AKS クラスターに独自のアプリケーションがある場合、次の手順で独自のサービスとポッドの名前を使用できます。

### <a name="limitations"></a>制限事項

* 現時点では、UDP はサポートされていません。

### <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること][azure-cli]。
* [Azure Dev Spaces][azds-vs-code] 拡張機能がインストールされ、MacOS または Windows 10 で実行されている [Visual Studio Code][vs-code]。
* [Azure Dev Spaces 自転車共有サンプル アプリケーション](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)または AKS クラスターで実行している独自のアプリケーション。

## <a name="connect-your-development-computer"></a>開発用コンピューターを接続する

Visual Studio Code で *dev-spaces/samples/BikeSharingApp/Bikes* を開き、Azure Dev Spaces 拡張機能を使用して、開発用コンピューターを AKS クラスターに接続します。

Azure Dev Spaces 拡張機能を使用するには、 *[表示]* 、 *[コマンド パレット]* の順にクリックし、Visual Studio Code でコマンド パレットを開きます。 「`Azure Dev Spaces: Redirect`」の入力を開始して、`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`、`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`、`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]` をクリックします。

![コマンド](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>リダイレクト オプションを選択する

`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` を実行する場合、既存の Kubernetes サービスを選択するように求められます。

![サービスを選択する](../media/how-to-connect/connect-choose-service.png)

このオプションにより、このサービスの AKS クラスターにおけるあらゆるトラフィックが開発用コンピューターで実行されているアプリケーションのバージョンにリダイレクトされます。 このサービスにおいて、複数のポッドが AKS クラスターで実行されている場合、このサービスのトラフィックはすべて、開発用コンピューターにのみルーティングされます。 Azure Dev Spaces ではまた、外向きのトラフィックがすべて、アプリケーションから AKS クラスターにルーティングされます。

`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]` を実行する場合、特定のポッドを選択するように求められます。

![ポッドを選択する](../media/how-to-connect/connect-choose-pod.png)

このオプションでは、特定のポッドに接続されます。 このオプションは、トラフィックを送受信しないポッドとのやりとりや、終了したポッドのレプリケートに便利です。 ポッドでトラフィックが送受信されない場合、このオプションは `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` と同じように動作し、選択したポッドのサービスに関連するすべてのポッドについて、AKS クラスターのすべてのトラフィックがリダイレクトされます。

`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]` を実行する場合、既存のポッドまたはサービスを選択するように求められません。 このオプションでは、開発用コンピューター上で実行されているアプリケーションから AKS クラスターに送信トラフィックがすべてリダイレクトされます。

たとえば、`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` を選択し、*自転車*サービスを選択します。

### <a name="select-a-connection-mode"></a>接続モードを選択する

リダイレクト オプションを選択すると、接続モードとして *[置換]* または *[複製]* を選択するように求められます。

![置換または複製](../media/how-to-connect/connect-replace-clone.png)

*[置換]* オプションでは、AKS クラスターの現在のポッドまたはサービスが置換され、そのサービスのトラフィックがすべて、開発用コンピューターにリダイレクトされます。 このオプションにより、AKS クラスター内のサービスのうち、リダイレクト対象のサービスとやりとりする他のサービスが悪影響を受ける可能性があり、開発用コンピューターでアプリケーションが起動されるまで機能しないことがあります。 *[複製]* オプションでは、開発用コンピューターにポッドまたはサービスのトラフィックをリダイレクトする目的で既存の子開発スペースを選択するか、新しい子開発スペースを作成できます。 このオプションを使用すると、その子開発スペースへのトラフィックのみが開発用コンピューターにリダイレクトされるため、隔離状態で作業でき、他のサービスを妨害しません。 *[複製]* オプションを使用するには、AKS クラスターで Azure Dev Spaces を有効にする必要があります。

この例では、 *[置換]* を選択します。

> [!NOTE]
> 既存のサービスのポッドに複数のコンテナーが含まれる場合、アプリケーションのコンテナーを選択するようにも求められます。

### <a name="select-a-port-for-your-application"></a>アプリケーションのポートを選択する

接続モードを選択した場合、ローカル アプリケーションの TCP ポートを入力するように求められます。 アプリケーションで複数のポートを開く場合、「*80,81*」のように、コンマで分割します。 アプリケーションでネットワーク要求を受け取らない場合、「*0*」を入力します。 この例では、「*3000*」と入力します。

![接続用のポートを選択する](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>接続されていることを確認する

アプリケーションの TCP ポートを選択すると、Azure Dev Spaces により、AKS クラスターへの接続が確立されます。 Azure Dev Spaces によりエージェントが AKS クラスターに挿入され、AKS クラスターと開発用コンピューターの間でトラフィックがリダイレクトされます。 この接続の確立には数分かかることがあります。 また、開発用コンピューターの *hosts* ファイルを変更するために Azure Dev Spaces から管理者アクセスが要求されます。

> [!IMPORTANT]
> Azure Dev Spaces で AKS クラスターへの接続が確立されると、 *[置換]* 接続モードを選択した場合、AKS クラスターの他のサービスは、開発用コンピューターでユーザーがそのサービスを開始するまで正しく機能しない可能性があります。 代わりに *[複製]* 接続モードを選択することで、リダイレクト用に子 Dev Space を作成し、親 Space の混乱を回避できます。 また、開発用コンピューターで利用できない依存関係がサービスに含まれているとき、場合によっては、アプリケーションを変更したり、[追加で構成](#additional-configuration)したりする必要があります。

AKS クラスターへの接続が確立されると、Azure Dev Spaces により、「*AZDS Connect - Bikes*」というタイトルのターミナル ウィンドウが開きます。 このターミナル ウィンドウには、AKS クラスターから構成されたあらゆる環境変数と DNS エントリが与えられます。 このターミナル ウィンドウで、あるいは Visual Studio Code デバッガーを使用することで実行するあらゆるコードが AKS クラスターに接続されます。

![ターミナル](../media/how-to-connect/connect-terminal.png)

また、Azure Dev Spaces により「*Dev Spaces Connect*」というタイトルのウィンドウとそのすべての出力が作成されます。

![出力](../media/how-to-connect/connect-output.png)

Azure Dev Spaces には、接続状態を示すステータス バー項目もあります。

![Status](../media/how-to-connect/connect-status.png)

ステータス バーに *Dev Spaces:Connected to dev/bikes on local port 3000* と表示されていることを確認します。

### <a name="configure-your-application-on-your-development-computer"></a>開発用コンピューターでアプリケーションを構成する

*AZDS Connect - Bikes* ターミナル ウィンドウを開き、`npm install` を実行します。

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

*[デバッグ]* をクリックし、 *[構成を開く]* をクリックします。 環境を選択するように求められたら、 *[Node.js]* を選択します。これで `.vscode/launch.json` ファイルが作成されます。 そのファイルの内容を次で置き換えます。

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

[package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) を開き、デバッグ スクリプトを追加します。

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>開発用コンピューターでアプリケーションを開始する

左にある *[デバッグ]* アイコンをクリックし、上にある *[NPM による起動]* の横にある [開始] ボタンをクリックします。

![NPM による起動](../media/how-to-connect/launch-npm.png)

アプリケーションが起動し、Azure Dev Spaces により、AKS クラスターと開発用コンピューターの間でトラフィックがリダイレクトされます。 *デバッグ コンソール*に下のようなメッセージが表示されます。

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Azure Dev Spaces ステータス バーをクリックし、アプリケーションのパブリック URL を選択することで *bikesharingweb* サービスに移動します。 パブリック URL は、先に実行した `azds list-uris` コマンドから見つけることもできます。 クラスターで Azure Dev Spaces を使用していない場合、使用している名前空間の IP または URL をアプリケーションに使用します。 上記の例では、*bikesharingweb* サービスのパブリック URL は `http://dev.bikesharingweb.fedcab0987.eus.azds.io/` です。 ユーザーとして *Aurelia Briggs (顧客)* を選択してから、借りる自転車を選択します。

### <a name="set-a-break-point"></a>ブレーク ポイントを設定する

[server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) を開き、233 行目のどこかをクリックして、カーソルをそこに置きます。 *F9* キーを押すか、 *[デバッグ]* 、 *[ブレークポイントの設定/解除]* の順にクリックし、ブレークポイントを設定します。

パブリック URL を開いて、*bikesharingweb* サービスに移動します。 ユーザーとして *Aurelia Briggs (顧客)* を選択してから、借りる自転車を選択します。 自転車の画像が読み込まれないことにご注意ください。 Visual Studio Code に戻って、233 行目が強調表示されていることを確認します。 設定したブレークポイントによって、233 行目でサービスが一時停止されました。 サービスを再開するには、*F5* キーを押すか、 *[デバッグ]* 、 *[続行]* の順にクリックします。 ブラウザーに戻り、自転車のプレースホルダー画像が表示されていることを確認します。

`server.js` の 233 行目にカーソルを置いて *F9* キーを押すことで、ブレークポイントを削除します。

### <a name="update-your-application"></a>アプリケーションの更新

`server.js` を編集し、行 232 および 233 を削除します。

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

変更を保存し、 *[デバッグ]* をクリックし、 *[デバッグの再起動]* をクリックします。 ブラウザーを更新し、自転車のプレースホルダー画像が消えたことを確認します。

*[デバッグ]* 、 *[デバッグの停止]* の順にクリックして、デバッガーを停止します。 Azure Dev Spaces ステータス バーをクリックし、AKS クラスターとの接続を断ちます。

## <a name="additional-configuration"></a>追加構成

Azure Dev Spaces では、ルーティング トラフィックを処理し、追加の構成なしで環境変数をレプリケートできます。 ConfigMap ファイルなど、AKS クラスターのコンテナーにマウントされているファイルをダウンロードする必要がある場合、`azds-local.env` を作成し、開発用コンピューターにそれらのファイルをダウンロードできます。

例 `azds-local.env` を以下に示します。

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>ログ記録と診断の使用

開発用コンピューターを AKS クラスターに接続した後、*Dev Spaces Connect* ウィンドウにログ出力が書き込まれます。

![出力](../media/how-to-connect/connect-output.png)

Azure Dev Spaces のステータス バーをクリックし、 *[診断情報の表示]* を選択します。 このコマンドを実行すると、ログ出力に現在の環境変数と DNS エントリが出力されます。

![診断付きの出力](../media/how-to-connect/connect-output-diagnostics.png)

また、この診断ログは、ご利用の[開発用コンピューターの *TEMP* ディレクトリ][azds-tmp-dir]にある `Azure Dev Spaces` ディレクトリにあります。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces と GitHub Actions を使用し、プル要求をリポジトリのメイン ブランチに結合する前に、AKS で直接、プル要求から変更をテストする方法を学習します。

> [!div class="nextstepaction"]
> [GitHub Actions と Azure Kubernetes Service][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download