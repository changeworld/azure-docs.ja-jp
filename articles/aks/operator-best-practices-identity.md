---
title: ID 管理のベスト プラクティス
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) でクラスターの認証と認可を管理する方法に関するクラスター オペレーター ベスト プラクティスについて説明します
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: de84e3e2a8da3e1b5195978a8a2204fdfa2108d7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105104"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) の認証と認可のベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターをデプロイし、保守管理するとき、リソースやサービスへのアクセスを管理する方法を実装します。 このような制御がなければ:
* 必要としないリソースやサービスへのアクセス許可がアカウントに付与されることがあります。 
* 変更を行う際に使用された資格情報のセットを追跡することが難しくなる場合があります。

このベスト プラクティス記事では、クラスター オペレーターが AKS クラスターのアクセスと ID を管理する方法について取り上げます。 この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * Azure Active Directory を利用して AKS クラスター ユーザーを認証します。
> * Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を使用してリソースへのアクセスを制御します。
> * Azure RBAC を使用して、AKS リソース、大規模な Kubernetes API、および `kubeconfig` へのアクセスを細かく制御します。
> * マネージド ID を使用して、他のサービスでポッド自体を認証します。

## <a name="use-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用する

> **ベスト プラクティスのガイダンス** 
> 
> Azure AD 統合で AKS クラスターをデプロイします。 Azure AD を使用することで、ID 管理コンポーネントが一元化されます。 ユーザー アカウントまたはグループの状態が変わると、AKS クラスターにアクセスしたとき、その変更内容が自動的に更新されます。 [Roles、ClusterRoles、Bindings](#use-kubernetes-role-based-access-control-kubernetes-rbac) を使用して、ユーザーまたはグループに最低限のアクセス許可を付与します。

お使いの Kubernetes クラスターの開発者とアプリケーション所有者はさまざまなリソースへのアクセスを必要とします。 Kubernetes には、ユーザーがどのリソースを操作できるかを制御するための ID 管理ソリューションがありません。 代わりに、一般的には自分のクラスターと既存の ID ソリューションを統合します。 ここで、AKS クラスターと統合できる企業対応の ID 管理ソリューションである Azure AD の出番です。

Azure AD 統合クラスターを AKS で使用する際は、リソースへのアクセス許可を定義する *Roles* または *ClusterRoles* を作成します。 作成後、Azure AD のユーザーまたはグループにロールを *バインド* します。 これらの Kubernetes RBAC の詳細については、[次のセクション](#use-kubernetes-role-based-access-control-kubernetes-rbac)を参照してください。 Azure AD の統合とリソースへのアクセスの制御方法をまとめたのが次の図です。

![Azure Active Directory と AKS の統合のためのクラスターレベルの認証](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. 開発者が Azure AD を利用して認証します。
1. Azure AD トークン発行エンドポイントがアクセス トークンを発行します。
1. 開発者は、`kubectl create pod` などの Azure AD トークンを使用して操作を実行します。
1. Kubernetes では Azure AD を利用してトークンの有効性が検証され、開発者のグループ メンバーシップが取得されます。
1. Kubernetes RBAC とクラスター ポリシーが適用されます。
1. 前述の Azure AD グループ メンバーシップの検証、Kubernetes RBAC、ポリシーに基づき、開発者の要求が通過するかが決まります。

Azure AD を使用する AKS クラスターを作成する方法については、「[Azure Active Directory と Azure Kubernetes Service を統合する][aks-aad]」を参照してください。

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を使用する

> **ベスト プラクティスのガイダンス**
> 
> Kubernetes RBAC を使用して、ユーザーまたはグループに付与されるクラスター リソースへのアクセス許可を定義します。 必要最低限のアクセス許可を割り当てるロールとバインディングを作成します。 Azure AD と統合して、ユーザーの状態またはグループ メンバーシップの変更を自動的に更新し、クラスター リソースへのアクセス許可を最新の状態に保ちます。

Kubernetes では、クラスター リソースへのアクセスを細かく制御できます。 アクセス許可は、クラスター レベルで、または特定の名前空間に対して定義します。 管理できるリソースと、その際付与するアクセス許可の種類を決定します。 その後、これらのロールをバインディングが与えられているユーザーまたはグループに適用します。 *Roles*、*ClusterRoles*、*Bindings* に関する詳細については、「[Azure Kubernetes Service (AKS) でのアクセスと ID オプション][aks-concepts-identity]」を参照してください。

例として、*finance-app* という名前の名前空間にあるリソースに完全アクセスできるロールを作成します。次のサンプル YAML マニフェストをご覧ください。

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

次に、Azure AD ユーザー *developer1\@contoso.com* を RoleBinding にバインドする RoleBinding を作成します。次の YAML マニフェストをご覧ください。

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

*developer1\@contoso.com* が AKS クラスターに対して認証されると、*finance-app* 名前空間に対する完全アクセスが与えられます。 このように、リソースへのアクセスが論理的に分離されて制御されます。 Kubernetes RBAC は、Azure AD 統合と共に使用します。

Kubernetes RBAC と Azure AD グループを使用して Kubernetes クラスター リソースへのアクセスを制御するには、[AKS でロールベースのアクセス制御と Azure Active Directory の ID を使用してクラスター リソースへのアクセスを制御する][azure-ad-rbac]方法に関するページを参照してください。

## <a name="use-azure-rbac"></a>Azure RBAC を使用する 
> **ベスト プラクティスのガイダンス** 
> 
> Azure RBAC を使用して、ユーザーまたはグループが 1 つ以上のサブスクリプションの AKS リソースに対して持つ必要最小限のアクセス許可を定義します。

AKS クラスターを完全に運用するには、次の 2 つのレベルのアクセスが必要です。 
1. Azure サブスクリプションの AKS リソースへのアクセス。 

    このアクセス レベルでは、次のことができます。
      * AKS API を使用してクラスターのスケーリングまたはアップグレードを制御する
      * 設定した `kubeconfig` をプルする。

    AKS リソースと `kubeconfig` へのアクセスを制御する方法については、[クラスター構成ファイルへのアクセスの制限](control-kubeconfig-access.md)に関する記事を参照してください。

2. Kubernetes API へのアクセス。 
    
    このアクセス レベルは、次のいずれかによって制御されます。
    * [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (従来) 
    * Kubernetes の認可のための Azure RBAC と AKS の統合

    Azure RBAC を使用して Kubernetes API にアクセス許可を細かく付与する方法については、「[Kubernetes 認可に Azure RBAC を使用する](manage-azure-rbac.md)」を参照してください。

## <a name="use-pod-managed-identities"></a>ポッドマネージド ID を使用する

> **ベスト プラクティスのガイダンス** 
> 
> ポッドやコンテナー イメージ内で固定の資格情報を使用しないでください。開示や悪用の危険にさらされます。 代わりに、*ポッド ID* を使用し、中央の Azure AD ID ソリューションによるアクセスを自動要求してください。 

> [!NOTE]
> ポッド ID は、Linux のポッドおよびコンテナー イメージのみでの使用を目的としています。 Windows コンテナーに対するポッドマネージド ID のサポートは近日対応予定です。

ポッドから Cosmos DB、Key Vault、Blob Storage などの他の Azure サービスへアクセスするには、アクセス資格情報が付与されている必要があります。 アクセス資格情報は、コンテナー イメージを使用して定義したり、Kubernetes シークレットとして挿入したりできます。 どちらの方法でも、手動で作成して割り当てる必要があります。 通常、資格情報はポッド全体で再利用されます。定期的なローテーションはありません。

Azure リソース用ポッドマネージド ID を利用すると、Azure AD 経由でサービスにアクセスすることを自動要求できます。 ポッドマネージド ID は、AKS では現在プレビュー段階にあります。 使用を開始するには、「Azure Kubernetes Service で Azure Active Directory ポッドマネージド ID を使用する (プレビュー)」[(https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) のドキュメントを参照してください。 

ポッドの資格情報を手動で定義する代わりに、ポッドマネージド ID によってアクセス トークンがリアルタイムで要求され、それが割り当てられているサービスにのみアクセスするために使用されます。 AKS には、ポッドでマネージド ID を使用できるようにする操作を処理するコンポーネントが 2 つあります。

* **Node Management Identity (NMI) サーバー** は、AKS クラスターの各ノードで DaemonSet として実行されるポッドです。 NMI サーバーは、Azure サービスへのポッド要求を待ち受けます。
* **Azure リソース プロバイダー** は、Kubernetes API サーバーに対してクエリを行い、ポッドに対応する Azure ID マッピングを確認します。

ポッドから Azure サービスへのアクセスが要求されると、ネットワーク ルールによって NMI サーバーにトラフィックがリダイレクトされます。 
1. NMI サーバー:
    * Azure サービスへのアクセスを要求しているポッドを、そのリモート アドレスに基づいて識別します。
    * Azure リソース プロバイダーにクエリを実行します。 
1. Azure リソース プロバイダーによって、AKS クラスターに Azure ID マッピングが存在するかどうかが確認されます。
1. NMI サーバーから、ポッドの ID マッピングに基づいて Azure AD にアクセス トークンが要求されます。 
1. Azure AD が与える NMI サーバーへのアクセスがポッドに返されます。 
    * ポッドはこのアクセス トークンを利用し、Azure のサービスへのアクセスを要求できます。

次の例では、開発者はマネージド ID を利用して Azure SQL Database へのアクセスを要求するポッドを作成しています。

![ポッド ID によって、他のサービスへのアクセスをポッドは自動要求できます。](media/operator-best-practices-identity/pod-identities.png)

1. クラスター オペレーターによって、ポッドからサービスへのアクセスが要求される際に、ID をマッピングするためのサービス アカウントが作成されます。
1. NMI サーバーは、アクセス トークンのためのポッド要求を、Azure リソース プロバイダーと共に Azure AD に中継するためにデプロイされます。
1. 開発者は、NMI サーバー経由でアクセス トークンを要求するポッドをマネージド ID と共にデプロイします。
1. トークンがポッドに返され、Azure SQL Database にアクセスするために使用されます

> [!NOTE]
> ポッドマネージド ID は、現在プレビュー状態です。

ポッドマネージド ID を使用するには、「[Azure Kubernetes Service で Azure Active Directory ポッドマネージド ID を使用する (プレビュー)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)」を参照してください。

## <a name="next-steps"></a>次のステップ

このベスト プラクティスの記事では、クラスターとリソースの認証と認可について取り上げました。 これらのベスト プラクティスのいくつかを実装する場合は、次の記事を参照してください。

* [Azure Active Directory と AKS の統合][aks-aad]
* [Azure Kubernetes Service で Azure Active Directory ポッドマネージド ID を使用する (プレビュー)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [マルチ テナントとクラスター分離][aks-best-practices-cluster-isolation]
* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]
* [Kubernetes スケジューラの高度な機能][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
