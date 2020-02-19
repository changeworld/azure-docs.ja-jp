---
title: コンテナー用 Azure Monitor のライブ データ (プレビュー) の設定 | Microsoft Docs
description: この記事では、コンテナー用 Azure Monitor で kubectl を使用せずに、コンテナーのログ (stdout/stderr) とイベントのリアルタイム ビューを設定する方法について説明します。
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: cf42eea99e437a76bb437b23f6eaffae1f1f3bc6
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063766"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>ライブ データ (プレビュー) 機能を設定する方法

Azure Kubernetes Service (AKS) クラスターから、コンテナー用 Azure Monitor でライブ データ (プレビュー) を表示するには、Kubernetes データにアクセスするためのアクセス許可を付与する認証を構成する必要があります。 このセキュリティ構成により、Azure portal で直接 Kubernetes API を介してデータにリアルタイムでアクセスできるようになります。

この機能では、ログ、イベント、メトリックへのアクセスを制御する次の 3 種類の方法がサポートされています。

- Kubernetes RBAC 認証なしの AKS が有効
- Kubernetes RBAC 認証を使って AKS が有効
- Azure Active Directory (AD) SAML ベースのシングル サインオンを使って AKS が有効

これらの手順では、Kubernetes クラスターへの管理アクセスと、Azure Active Directory (AD) への管理アクセス (ユーザー認証に Azure AD を使用するように構成する場合) が必要です。  

この記事では、次のクラスターからのライブ データ (プレビュー) 機能へのアクセスを制御する認証を構成する方法について説明します。

- ロールベースのアクセス制御 (RBAC) 対応の AKS クラスター
- Azure Active Directory と統合された AKS クラスター 

>[!NOTE]
>[プライベート クラスター](https://azure.microsoft.com/updates/aks-private-cluster/)として有効にされた AKS クラスターは、この機能でサポートされていません。 この機能では、ブラウザーからプロキシ サーバーを介した Kubernetes API への直接アクセスを利用します。 このプロキシから Kubernetes API をブロックするようにネットワーク セキュリティを有効にすると、このトラフィックはブロックされます。 

>[!NOTE]
>この機能は、Azure 中国を含む、すべての Azure リージョンで利用できます。 現在、Azure 米国政府機関では利用できません。

## <a name="authentication-model"></a>認証モデル

ライブ データ (プレビュー) 機能では Kubernetes API を利用します。これは、`kubectl` コマンド ライン ツールと同じです。 Kubernetes API エンドポイントは、ブラウザーが検証できない自己署名証明書を利用します。 この機能では内部プロキシを使用して AKS サービスで証明書を検証し、トラフィックが信頼できることを確認します。

Azure portal では、Azure Active Directory クラスターのログイン資格情報を検証し、クラスターの作成時にクライアント登録の設定 (この記事では再構成) にリダイレクトするよう求められます。 この動作は、`kubectl` で必要な認証プロセスに似ています。 

>[!NOTE]
>クラスターに対する承認は、Kubernetes および Kubernetes で構成されているセキュリティ モデルによって管理されます。 この機能にアクセスするユーザーには、`az aks get-credentials -n {your cluster name} -g {your resource group}` を実行する場合と同様に、Kubernetes 構成 (*kubeconfig*) をダウンロードするためのアクセス許可が必要です。 Azure RBAC 対応の AKS クラスターと RBAC 認証が有効になっていない AKS クラスターの場合、この構成ファイルには、**Azure Kubernetes Service クラスター ユーザー ロール**の承認および認証トークンが含まれます。 Azure Active Directory (AD) の SAML ベースのシングル サインオンで AKS が有効になっている場合は、Azure AD とクライアント登録の詳細情報が含まれます。

>[!IMPORTANT]
>この機能のユーザーには、`kubeconfig` をダウンロードしてこの機能を使用するために、クラスターに対する [Azure Kubernetes クラスター ユーザー ロール](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions)が必要です。 この機能を利用するユーザーには、クラスターに対する共同作成者アクセス権は**不要**です。 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>RBAC なしの Kubernetes クラスターが有効

お使いの Kubernetes クラスターが Kubernetes RBAC 認証で構成されていない場合、または Azure AD シングル サインオンと統合されていない場合は、これらの手順に従う必要はありません。 非 RBAC 構成では、管理アクセス許可が既定で付与されるためです。

## <a name="configure-kubernetes-rbac-authentication"></a>Kubernetes RBAC 認証の構成

Kubernetes RBAC 認証を有効にすると、Kubernetes API にアクセスするために **clusterUser** と **clusterAdmin** の 2 つのユーザーが使用されます。 これは、管理オプションなしで `az aks get-credentials -n {cluster_name} -g {rg_name}` 実行する場合と同様です。 つまり、**clusterUser** に Kubernetes API のエンドポイントへのアクセスを許可する必要があります。

次の例の手順は、この yaml 構成テンプレートからクラスター ロール バインディングを構成する方法を示しています。

1. yaml ファイルをコピーして貼り付け、LogReaderRBAC.yaml として保存します。  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. 構成を更新するには、`kubectl apply -f LogReaderRBAC.yaml` コマンドを実行します。

>[!NOTE] 
> 以前のバージョンの `LogReaderRBAC.yaml` ファイルをクラスターに適用した場合は、上記の手順 1. に示す新しいコードをコピーして貼り付けて更新してから、手順 2. に示すコマンドを実行してクラスターに適用します。

## <a name="configure-ad-integrated-authentication"></a>AD 統合認証の構成 

ユーザー認証に Azure Active Directory (AD) を使用するように構成された AKS クラスターでは、この機能にアクセスするユーザーのログイン資格情報を利用します。 この構成では、Azure AD 認証トークンを使って AKS クラスターにサインインできます。

Azure portal が信頼されたリダイレクト URL として承認ページをリダイレクトできるように、Azure AD クライアント登録を再構成する必要があります。 その後、Azure AD のユーザーは、**ClusterRoles** と **ClusterRoleBindings** を使用して、同じ Kubernetes API エンドポイントへの直接アクセスを許可されます。 

Kubernetes での高度なセキュリティ設定の詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)をご覧ください。 

>[!NOTE]
>新しい RBAC 対応クラスターを作成する場合は、「[Azure Active Directory と Azure Kubernetes Service を統合する](../../aks/azure-ad-integration.md)」を参照し、手順に従って Azure AD 認証を構成してください。 クライアント アプリケーションを作成する手順の該当するセクションの注で、コンテナー用 Azure Monitor 用に作成する必要がある、以下の手順 3 で指定するものと一致する 2 つのリダイレクト URL が強調されています。

### <a name="client-registration-reconfiguration"></a>クライアント登録の再構成

1. Azure portal の **[Azure Active Directory] > [アプリの登録]** で、Azure AD の Kubernetes クラスターのクライアント登録を見つけます。

2. 左側のウィンドウで **[認証]** を選択します。 

3. **Web** アプリケーションの種類として、この一覧に 2 つのリダイレクト URL を追加します。 最初のベース URL の値は `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`、2番目のベース URL の値は `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` にする必要があります。

    >[!NOTE]
    >この機能を Azure 中国で使用する場合は、最初のベース URL の値を `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` にし、2 番目のベース URL の値を `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` にする必要があります。 
    
4. リダイレクト URL を登録したら、 **[暗黙的な許可]** で **[アクセス トークン]** と **[ID トークン]** の各オプションを選択し、ご自分の変更を保存します。

>[!NOTE]
>シングル サインオン用の Azure Active Directory での認証の構成は、新しい AKS クラスターの最初のデプロイ中にのみ実行できます。 既にデプロイされている AKS クラスターに対して、シングル サインオンを構成することはできません。
  
>[!IMPORTANT]
>更新された URI を使用したユーザー認証のために Azure AD を再構成した場合は、ブラウザーのキャッシュをクリアして、更新された認証トークンが確実にダウンロードおよび適用されるようにします。

## <a name="grant-permission"></a>アクセス許可を付与する

ライブ データ (プレビュー) 機能にアクセスするには、各 Azure AD アカウントに Kubernetes の適切な API へのアクセス許可を付与する必要があります。 Azure Active Directory アカウントに付与する手順は、[Kubernetes RBAC 認証](#configure-kubernetes-rbac-authentication)のセクションで説明する手順と似ています。 yaml 構成テンプレートをクラスターに適用する前に、**ClusterRoleBinding** の **clusterUser** を目的のユーザーに置き換えます。 

>[!IMPORTANT]
>RBAC のバインドを付与するユーザーが同じ Azure AD テナントに存在する場合は、userPrincipalName に基づいてアクセス許可を割り当てます。 ユーザーが別の Azure AD テナントに存在する場合は、objectId プロパティを照会して使用します。

AKS クラスターの **ClusterRoleBinding** の構成に関する追加のヘルプについては、「[RBAC のバインドを作成する](../../aks/azure-ad-integration-cli.md#create-rbac-binding)」をご覧ください。

## <a name="next-steps"></a>次のステップ

認証を設定したら、[メトリック](container-insights-livedata-metrics.md)、[デプロイ](container-insights-livedata-deployments.md)、[イベントとログ](container-insights-livedata-overview.md)をクラスターからリアルタイムで表示できます。
