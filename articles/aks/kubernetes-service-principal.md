---
title: "Azure Kubernetes クラスターのサービス プリンシパル | Microsoft Docs"
description: "AKS で Kubernetes クラスターの Azure Active Directory サービス プリンシパルを作成および管理します"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: af27d01108cbfb3bd71023ffbce85f348abb0cfe
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Azure Container Service (AKS) でのサービス プリンシパル

AKS クラスターには、Azure API と対話するための [Azure Active Directory サービス プリンシパル](../active-directory/develop/active-directory-application-objects.md)が必要です。 サービス プリンシパルは、[ユーザー定義のルート](../virtual-network/virtual-networks-udr-overview.md)や[レイヤー 4 の Azure Load Balancer](../load-balancer/load-balancer-overview.md) などのリソースを動的に管理するために必要です。

この記事では、AKS で Kubernetes クラスターのサービス プリンシパルを設定するためのさまざまなオプションを紹介します。

## <a name="before-you-begin"></a>開始する前に

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの kubectl 接続が確立されていることを想定しています。 これらの項目が必要な場合は、[AKS のクイック スタート](./kubernetes-walkthrough.md)を参照してください。

Azure AD サービス プリンシパルを作成するには、アプリケーションを Azure AD テナントに登録し、そのアプリケーションをサブスクリプション内のロールに割り当てるためのアクセス許可が必要です。 必要なアクセス許可がない場合は、必要なアクセス許可の割り当てを Azure AD またはサブスクリプションの管理者に依頼するか、Kubernetes クラスターのサービス プリンシパルを事前に作成することが必要になる可能性があります。

また、Azure CLI バージョン 2.0.21 以降がインストールされ、構成されていることも必要です。 バージョンを確認するには、az --version を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-sp-with-aks-cluster"></a>AKS クラスターで SP を作成する

`az aks create` コマンドを使用して AKS クラスターをデプロイするときに、サービス プリンシパルを自動的に生成することもできます。

次の例では、AKS クラスターが作成されます。また、既存のサービス プリンシパルが指定されていないため、そのクラスター用にサービス プリンシパルが作成されます。 この操作を完了するために、アカウントには、サービス プリンシパルを作成するための適切な権限が必要です。

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>既存の SP を使用する

既存の Azure AD サービス プリンシパルは、AKS クラスターで使用することも、AKS クラスターで使用するために事前に作成しておくことも可能です。 これは、サービス プリンシパルの情報を指定する必要がある Azure Portal からクラスターをデプロイする際に役立ちます。

既存のサービス プリンシパルを使用する場合は、次の要件を満たしている必要があります。

- スコープ: クラスターをデプロイするために使用されるサブスクリプション
- ロール: 共同作成者
- クライアント シークレット: パスワードである必要があります

## <a name="pre-create-a-new-sp"></a>新しい SP を事前に作成する

Azure CLI を使用してサービス プリンシパルを作成するには、[az ad sp create-for-rbac]() コマンドを使用します。

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

出力は次のようになります。 `appId` と `password` を書き留めておきます。 これらの値は、AKS クラスターの作成時に使用します。

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>既存の SP を使用する

事前に作成したサービス プリンシパルを使用するときに、`appId` と `password` を引数の値として `az aks create` コマンドに指定します。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-principal <appId> ----client-secret <password>
```

Azure Portal から AKS クラスターをデプロイする場合は、AKS クラスターの構成フォームにこれらの値を入力します。

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>追加の考慮事項

AKS と Azure AD サービス プリンシパルを使用する場合は、次の点に注意してください。

* Kubernetes のサービス プリンシパルは、クラスター構成の一部です。 ただし、クラスターのデプロイに ID を使用しないでください。
* すべてのサービス プリンシパルは、Azure AD アプリケーションに関連付けられています。 Kubernetes クラスターのサービス プリンシパルは、有効な任意の Azure AD アプリケーション名 (たとえば `https://www.contoso.org/example`) に関連付けることができます。 アプリケーションの URL は、実際のエンドポイントである必要はありません。
* サービス プリンシパルの**クライアント ID** を指定する場合、この記事で示したように `appId` の値を使用するか、対応するサービス プリンシパルの `name` (例: `https://www.contoso.org/example`) を使用することができます。
* Kubernetes クラスター内のマスター VM とノード VM では、サービス プリンシパルの資格情報が /etc/kubernetes/azure.json ファイルに格納されます。
* `az aks create` コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルの資格情報は、コマンドの実行に使用されたコンピューター上の ~/.azure/acsServicePrincipal.json ファイルに書き込まれます。
* `az aks create` コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルは同じサブスクリプション内に作成された [Azure Container Registry](../container-registry/container-registry-intro.md) でも認証を行うことができます。

## <a name="next-steps"></a>次のステップ

Azure Active Directory サービス プリンシパルの詳細については、Azure AD アプリケーションのドキュメントを参照してください。

> [!div class="nextstepaction"]
> [アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/active-directory-application-objects.md)
