---
title: Azure Kubernetes Service で Azure AD を使用する
description: Azure Kubernetes Service (AKS) における Azure AD の使用方法
services: container-service
manager: gwallace
author: TomGeske
ms.topic: article
ms.date: 07/08/2020
ms.author: thomasge
ms.openlocfilehash: 9cacd2454dc987f7d507bb4b677e742f0be0d391
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86166503"
---
# <a name="aks-managed-azure-active-directory-integration-preview"></a>AKS マネージド Azure Active Directory の統合 (プレビュー)

> [!NOTE]
> Azure Active Directory (Azure AD) と統合された既存の AKS (Azure Kubernetes Service) クラスターは、新しい AKS マネージド Azure AD エクスペリエンスの影響を受けません。

AKS マネージド Azure AD 統合は、Azure AD の統合エクスペリエンスを簡素化するように設計されています。これまでユーザーは、クライアント アプリとサーバー アプリを作成し、Azure AD テナントでディレクトリの読み取りアクセス許可を付与する必要がありました。 新しいバージョンでは、クライアント アプリとサーバー アプリは AKS リソース プロバイダーで管理されます。

## <a name="azure-ad-authentication-overview"></a>Azure AD 認証の概要

クラスター管理者は、ユーザーの ID またはディレクトリ グループのメンバーシップに基づいて、Kubernetes のロールベースのアクセス制御 (RBAC) を構成できます。 Azure AD 認証は、OpenID Connect によって AKS クラスターに提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。 OpenID Connect の詳細については、[OpenID Connect のドキュメント][open-id-connect]を参照してください。

[Azure Active Directory 統合の概念に関するドキュメント](concepts-identity.md#azure-active-directory-integration)で AAD 統合フローの詳細を確認してください。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトイン単位で利用できます。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、ベストエフォート ベースでカスタマー サポートによって部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポート記事を参照してください。
>
> - [AKS のサポート ポリシー](support-policies.md)
> - [Azure サポートに関する FAQ](faq.md)

## <a name="before-you-begin"></a>開始する前に

* Azure アカウント テナント ID を確認します。そのためには、Azure portal に移動し、[Azure Active Directory] > [プロパティ] > [ディレクトリ ID] の順に選択します

> [!Important]
> 最小バージョンが 1.18 の Kubectl を使用する必要があります。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.5.1 以降
- aks-preview 0.4.38 拡張機能
- 最小バージョンが [1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180) の Kubectl

aks-preview 以降の拡張機能をインストールまたは更新するには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Kubectl をインストールするには、次のコマンドを使用します。

```azurecli
sudo az aks install-cli
kubectl version --client
```

他のオペレーティング システムでは、[これらの手順](https://kubernetes.io/docs/tasks/tools/install-kubectl/)を使用します。

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD が有効になっている AKS クラスターを作成する

次の CLI コマンドを使用して、AKS クラスターを作成します。

Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

既存の Azure AD グループを使用することも、新しい Azure AD グループを作成することもできます。 Azure AD グループのオブジェクト ID が必要です。

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

クラスター管理者用の新しい Azure AD グループを作成するには、次のコマンドを使用します。

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

AKS クラスターを作成し、Azure AD グループの管理アクセスを有効にします

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

正常に作成された AKS マネージド Azure AD クラスターの応答本文には、次のセクションが含まれます
```
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

クラスターは数分以内に作成されます。

## <a name="access-an-azure-ad-enabled-cluster"></a>Azure AD が有効なクラスターへのアクセス

次の手順を実行するには、[Azure Kubernetes Service クラスター ユーザー](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role)組み込みロールが必要です。

クラスターにアクセスするためのユーザー資格情報を取得します。
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
手順に従ってサインインします。

kubectl get nodes コマンドを使用して、クラスターのノードを表示します。

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
[ロールベースのアクセスの制御 (RBAC)](https://docs.microsoft.com/azure/aks/azure-ad-rbac) を構成して、クラスターの追加のセキュリティ グループを構成します。

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Azure AD でのアクセスに関する問題のトラブルシューティング

> [!Important]
> 次に示す手順では、通常の Azure AD グループ認証をバイパスしています。 これは緊急時にのみ使用してください。

クラスターへのアクセス権を持つ有効な Azure AD グループへのアクセス権がないために永続的にブロックされている場合でも、クラスターに直接アクセスするための管理者資格情報を取得することができます。

これらの手順を実行するには、[Azure Kubernetes Service クラスター管理者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role)組み込みロールにアクセスできる必要があります。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-sign-in-with-kubelogin"></a>kubelogin を使用した非対話型サインイン

継続的インテグレーション パイプラインなど、現在 kubectl で使用できない非対話型シナリオがいくつかあります。 [`kubelogin`](https://github.com/Azure/kubelogin) を使用して、非対話型サービス プリンシパル サインインでクラスターにアクセスできます。

## <a name="next-steps"></a>次のステップ

* [Kubernetes 承認用の Azure RBAC 統合][azure-rbac-integration]について学習する。
* [Azure AD と Kubernetes RBAC の統合][azure-ad-rbac]について学習する。
* [kubelogin](https://github.com/Azure/kubelogin) を使用して、kubectl では利用できない Azure 認証の機能にアクセスする。
* [AKS と Kubernetes ID の概念][aks-concepts-identity]について学習する。
* [Azure Resource Manager (ARM) テンプレート][aks-arm-template]を使用して、AKS マネージド Azure AD が有効なクラスターを作成する。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

