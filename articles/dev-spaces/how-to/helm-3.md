---
title: Helm 3 を使用するように Azure Dev Spaces クラスターを構成する (プレビュー)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Helm 3 を使用するように Dev Spaces クラスターを構成する方法について説明します
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454297"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Helm 3 を使用するように Azure Dev Spaces クラスターを構成する (プレビュー)

Azure Dev Spaces の既定では、Helm 2 を使用して、AKS クラスターの開発スペースにユーザー サービスをインストールします。 Azure Dev Spaces を使うと、開発スペースにユーザー サービスをインストールする Helm 2 ではなく Helm 3 を使用できるようになります。 ユーザー サービスのインストールに使用する Helm Azure Dev Spaces のバージョンに関係なく、Helm 2 または 3 クライアントを引き続き使用して、同じクラスター上で独自のリリースを管理できます。

Helm 3 を有効にすると、次の方法で開発者スペースにユーザー サービスをインストールした場合、Azure Dev Spaces は動作が異なります。

* Tiller は、*azds* 名前空間のクラスターにデプロイされなくなります。
* Helm によって、*azds* 名前空間ではなく、サービスがインストールされている名前空間にリリース情報が格納されます。
* コントローラーが削除された後、Helm 3 のリリース情報はサービスがインストールされる名前空間に残ります。
* Helm 3 クライアントを使用して、クラスター上の Azure Dev Spaces によって管理されるリリースと直接対話できます。

このガイドでは、Azure Dev Spaces の Helm 3 を使用して開発スペースにユーザー サービスをインストールする方法について説明します。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="before-you-begin"></a>開始する前に

### <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること][azure-cli]。

### <a name="register-the-helm3preview-preview-feature"></a>Helm3Preview プレビュー機能を登録する

Azure Dev Spaces で Helm 3 を使用して開発スペースにユーザー サービスをインストールできるようにするには、まず *az feature register* コマンドを使用してサブスクリプションで *Helm3Preview* 機能フラグを有効にします。

> [!WARNING]
> *Helm3Preview* 機能フラグを使用して Azure Dev Spaces を有効にした AKS クラスターには、このプレビュー エクスペリエンスが使用されます。 AKS クラスターで完全にサポートされている Azure Dev Spaces を引き続き有効にするには、運用サブスクリプションでプレビュー機能を有効にしないでください。 プレビュー機能のテストには、別のテストまたは開発用 Azure サブスクリプションを使用します。

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

登録が完了するまでに数分かかります。 *az feature show* コマンドを使用して、登録状態を確認します。

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

*state* が *Registered* の場合、*az provider register* を使用して *Microsoft.DevSpaces* の登録を更新します。

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>制限事項

この機能がプレビュー段階の間は、次の制限事項が適用されます。

* 既存のワークロードがある AKS クラスターでは、この機能を使用できません。 新しい AKS クラスターを作成する必要があります。

## <a name="create-your-cluster"></a>クラスターの作成

このプレビュー機能があるリージョンに新しい AKS クラスターを作成します。 以下のコマンドを実行すると、*MyResourceGroup* という名前のリソース グループと *MyAKS* という名前の新しい AKS クラスターが作成されます。

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Azure Dev Spaces の有効化

*use-dev-spaces* コマンドを使用して AKS クラスターで Dev Spaces を有効にし、プロンプトに従います。 次のコマンドを使用すると、*MyResourceGroup* グループ内の *MyAKS* クラスターで Dev Spaces が有効になり、既定の開発スペースが作成されます。

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Dev Spaces で Helm 3 が実行されていることを確認する

*azds* 名前空間のデプロイを一覧表示して、tiller が実行されていないことを確認します。

```cmd
kubectl get deployment -n azds
```

azds 名前空間で *tiller-deploy* が実行されていないことを確認します。 次に例を示します。

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces を使用して複数のコンテナーにまたがるより複雑なアプリケーションを開発する方法と、別の空間で別のバージョンまたは分岐を使用して作業することによって共同開発を簡略化する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces でのチーム開発][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md