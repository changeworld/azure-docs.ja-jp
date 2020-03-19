---
title: AKS で Azure Dev Spaces を有効にしてクライアント側ツールをインストールする
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: AKS クラスターで Azure Dev Spaces を有効にして、クライアント側ツールをインストールする方法について説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898944"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>AKS クラスターで Azure Dev Spaces を有効にして、クライアント側ツールをインストールする

この記事では、AKS クラスターで Azure Dev Spaces を有効にする方法と、クライアント側ツールをインストールする方法をいくつかを紹介します。

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>CLI を使用して Azure Dev Spaces を有効化または削除する

CLI を使用して Dev Spaces を有効にする前に、以下が必要です。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント][az-portal-create-account]を作成できます。
* [Azure CLI がインストールされていること][install-cli]。
* [サポートされているリージョン][supported-regions]の [AKS クラスター][create-aks-cli]。

`use-dev-spaces` コマンドを使用して AKS クラスターで Dev Spaces を有効にし、プロンプトに従います。

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

上のコマンドを使用すると、*myResourceGroup* グループ内の *myAKSCluster* クラスターで Dev Spaces が有効になり、*default* 開発空間が作成されます。

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

`use-dev-spaces` コマンドでは、Azure Dev Spaces CLI もインストールされます。

AKS クラスターから Azure Dev Spaces を削除するには、`azds remove` コマンドを使用します。 次に例を示します。

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

上記のコマンドでは *MyResourceGroup* 内の *MyAKS* クラスターから Azure Dev Spaces を削除します。 Azure Dev Spaces で作成した名前空間はすべて、ワークロードと共に残りますが、それらの名前空間にある新しいワークロードは Azure Dev Spaces ではインストルメント化されません。 また、Azure Dev Spaces によってインストルメント化された既存のポッドを再起動すると、エラーが表示される場合があります。 Azure Dev Spaces ツールを使用せずに、それらのポッドを再デプロイする必要があります。 クラスターから Azure Dev Spaces を完全に削除するには、Azure Dev Spaces が有効化されていたすべての名前空間にあるすべてのポッドを削除します。

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Azure portal を使用して Azure Dev Spaces を有効化または削除する

Azure portal を使用して Dev Spaces を有効にする前に、以下が必要です。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント][az-portal-create-account]を作成できます。
* [サポートされているリージョン][supported-regions]の [AKS クラスター][create-aks-portal]。

Azure portal を使用して Azure Dev Spaces を有効にするには:
1. [Azure portal][az-portal] にサインインします。
1. AKS クラスターに移動します。
1. *[Dev Spaces]* メニュー項目を選択します。
1. *[Dev Spaces を有効にする]* を *[はい]* に変更して *[保存]* をクリックします。

![Azure portal での Dev Spaces の有効化](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Azure portal を使用して Azure Dev Spaces を有効にしても、Azure Dev Spaces 用のクライアント側ツールはインストール**されません**。

AKS クラスターから Azure Dev Spaces を削除するには、 *[Dev Spaces を有効にする]* を *[いいえ]* に変更して、 *[保存]* をクリックします。 Azure Dev Spaces で作成した名前空間はすべて、ワークロードと共に残りますが、それらの名前空間にある新しいワークロードは Azure Dev Spaces ではインストルメント化されません。 また、Azure Dev Spaces によってインストルメント化された既存のポッドを再起動すると、エラーが表示される場合があります。 Azure Dev Spaces ツールを使用せずに、それらのポッドを再デプロイする必要があります。 クラスターから Azure Dev Spaces を完全に削除するには、Azure Dev Spaces が有効化されていたすべての名前空間にあるすべてのポッドを削除します。

## <a name="install-the-client-side-tools"></a>クライアント側ツールをインストールする

Azure Dev Spaces クライアント側ツールを使用すると、ローカル コンピューターから AKS クラスター上の dev 領域と対話できます。 クライアント側ツールをインストールする方法はいくつかあります。

* [Visual Studio Code][vscode] で [Azure Dev Spaces 拡張機能][vscode-extension]をインストールします。
* [Visual Studio 2019][visual-studio] で、Azure Development ワークロードをインストールします。
* Visual Studio 2017 で、Web 開発ワークロードと [Visual Studio Tools for Kubernetes][visual-studio-k8s-tools] をインストールします。
* [Windows][cli-win]、[Mac][cli-mac]、または [Linux][cli-linux] CLI をダウンロードしてインストールします。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces を使用して複数のコンテナーにまたがるより複雑なアプリケーションを開発する方法と、別の空間で別のバージョンまたは分岐を使用して作業することによって共同開発を簡略化する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces でのチーム開発][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
