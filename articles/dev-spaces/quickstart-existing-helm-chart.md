---
title: Kubernetes で既存の Helm チャートを使用してアプリケーションを開発する
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: このクイックスタートでは、Azure Dev Spaces とコマンド ラインを使用し、Azure Kubernetes Service 上で既存の Helm チャートを使用するアプリケーションを開発する方法について説明します
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
manager: gwallace
ms.openlocfilehash: c37ea0b04e99cf1bba555e098bdf33b8a8558cfa
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996679"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>クイック スタート:Kubernetes で既存の Helm チャートを使用するアプリケーションを開発する - Azure Dev Spaces
このガイドでは、以下の方法について説明します。

- Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
- コマンド ラインで Azure Dev Spaces を使用して、AKS 内の既存の Helm チャートを使用するアプリケーションを実行する。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
- [Azure CLI がインストールされていること](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターを作成する

[サポートされているリージョン][supported-regions]で AKS クラスターを作成する必要があります。 下記のコマンドを使用すると、*MyResourceGroup* というリソース グループと *MyAKS* という AKS クラスターが作成されます。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS クラスターで Azure Dev Spaces を有効にする

`use-dev-spaces` コマンドを使用して AKS クラスターで Dev Spaces を有効にし、プロンプトに従います。 下記のコマンドを使用すると、*MyResourceGroup* グループ内の *MyAKS* クラスターで Dev Spaces が有効になり、*dev* という開発空間が作成されます。

> [!NOTE]
> この `use-dev-spaces` コマンドでは、Azure Dev Spaces CLI がまだインストールされていない場合にはこれもインストールされます。 Azure Dev Spaces CLI を Azure Cloud Shell にインストールすることはできません。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>サンプル アプリケーション コードを取得する

この記事では、[Azure Dev Spaces サンプル アプリケーション](https://github.com/Azure/dev-spaces)を使用して、Azure Dev Spaces の使い方のデモを行います。

GitHub からアプリケーションを複製して、*dev-spaces/samples/python/getting-started/webfrontend* ディレクトリに移動します。

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>アプリケーションを準備する

Azure Dev Spaces でアプリケーションを実行するには、Dockerfile と Helm チャートが必要です。 [Java][java-quickstart]、[.NET core][netcore-quickstart]、[Node.js][nodejs-quickstart] などの一部の言語では、必要なすべての資産を Azure Dev Spaces クライアント ツールで生成できます。 Go、PHP、Python など、他の多くの言語では、有効な Dockerfile を提供できる限り、クライアント ツールで Helm チャートを生成できます。 この場合、サンプル アプリケーションには既存の Dockerfile と Helm チャートがあります

`azds prep` コマンドを使用して、Azure Dev Spaces で既存の Helm チャートと Dockerfile を使用するアプリケーションを実行するための構成を生成します。

```cmd
azds prep --enable-ingress --chart webfrontend/
```

`prep`dev-spaces/samples/python/getting-started/webfrontend* ディレクトリから * コマンドを実行し、`--chart` を使用して Helm チャートの場所を指定する必要があります。

> [!NOTE]
> 次のような警告が表示されることがあります: *警告: Dockerfile could not be generated due to unsupported language (サポートされていない言語が原因で Dockerfile を生成できませんでした)* " `azds prep` の実行時。 `azds prep` コマンドを実行すると、対象のプロジェクトの [Dockerfile と Helm チャート](how-dev-spaces-works-prep.md#prepare-your-code)の生成が試行されますが、既存の Dockerfile または Helm チャートは上書きされません。

## <a name="build-and-run-code-in-kubernetes"></a>Kubernetes でコードをビルドして実行する

`azds up` コマンドを使用して、AKS でコードをビルドして実行します。

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

`azds up` コマンドの出力に表示されるパブリック URL を開いて、サービスが稼働していることを確認できます。 この例のパブリック URL は `http://dev.service.1234567890abcdef1234.eus.azds.io/` です。

> [!NOTE]
> `azds up` の実行中にサービスに移動すると、`azds up` コマンドの出力に HTTP 要求のトレースも表示されます。 それらのトレースを、サービスのトラブルシューティングやデバッグに活かすことができます。 トレースは、`azds up` の実行時に `--disable-http-traces` を使用して無効にできます。

*Ctrl + C* キーを押して `azds up` コマンドを停止すると、サービスは引き続き AKS で稼働し、パブリック URL は使用可能な状態のままになります。

## <a name="update-code"></a>コードの更新

サービスの更新バージョンをデプロイするには、ご自分のプロジェクトにある任意のファイルを更新して、`azds up` コマンドを再実行します。 次に例を示します。

1. `azds up` がまだ実行されている場合、*Ctrl + C* キーを押します。
1. [`webfrontend.py` の 13 行目](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13)を以下に更新します。
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. 変更を保存します。
1. `azds up` コマンドを再実行します。

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. 稼働中のご自分のサービスに移動して、変更を確認します。
1. *Ctrl + C* キーを押して、`azds up` コマンドを停止します。

## <a name="clean-up-your-azure-resources"></a>Azure リソースをクリーンアップする

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces を使用して複数のコンテナーにまたがるより複雑なアプリケーションを開発する方法と、別の空間で別のバージョンまたは分岐を使用して作業することによって共同開発を簡略化する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces でのチーム開発][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service