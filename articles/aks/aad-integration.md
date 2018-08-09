---
title: Azure Active Directory と Azure Kubernetes Service を統合する
description: Azure Active Directory が有効な Azure Kubernetes Service クラスターを作成する方法について説明します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2c4e0f8c31299644c912a70fc91bbdfa6da6795b
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579029"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Azure Active Directory と AKS を統合する - プレビュー

Azure Kubernetes Service (AKS) は、ユーザー認証に Azure Active Directory を使うように構成することができます。 この構成では、Azure Active Directory 認証トークンを使って Azure Kubernetes Service クラスターにログインできます。 さらに、クラスター管理者は、ユーザー ID またはディレクトリ グループのメンバーシップを基にして Kubernetes のロールベースのアクセス制御を構成できます。

このドキュメントでは、AKS と Azure AD に必要なすべての前提条件の作成、Azure AD が有効なクラスターの展開、および AKS クラスターでの簡単な RBAC ロールの作成について説明します。 既存の RBAC 非対応 AKS クラスターは現在、RBAC 用途のために更新できないことに注意してください。

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) の RBAC と Azure AD の統合は、現在は**プレビュー段階**です。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。
>

## <a name="authentication-details"></a>認証の詳細

Azure AD 認証は、OpenID Connect によって Azure Kubernetes クラスターに提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。 OpenID Connect について詳しくは、[Open ID Connect のドキュメント][open-id-connect]をご覧ください。

Kubernetes クラスターの内部からは、webhook トークン認証を使って認証トークンが確認されます。 webhook トークン認証は、AKS クラスターの一部として構成および管理されます。 webhook トークン認証について詳しくは、[webhook 認証のドキュメント][kubernetes-webhook]をご覧ください。

> [!NOTE]
> AKS 認証用に Azure AD を構成すると、2 つの Azure AD アプリケーションが構成されます。 この操作は、Azure テナント管理者が行う必要があります。

## <a name="create-server-application"></a>サーバー アプリケーションを作成する

最初の Azure AD アプリケーションは、ユーザーの Azure AD グループ メンバーシップを取得するために使われます。

1. **[Azure Active Directory]** > **[アプリの登録]** > **[新しいアプリケーションの登録]** を選択します。

  アプリケーションの名前を指定し、アプリケーションの種類として **[Web アプリ/API]** を選び、**[サインオン URL]** に URI 形式の値を入力します。 完了したら **[作成]** を選択します。

  ![Azure AD の登録を作成する](media/aad-integration/app-registration.png)

2. **[マニフェスト]** を選び、`groupMembershipClaims` の値を `"All"` に編集します。

  更新が終わったら保存します。

  ![グループ メンバーシップを全部に更新する](media/aad-integration/edit-manifest.png)

3. Azure AD アプリケーションに戻り、**[設定]** > **[キー]** を選びます。

  キーの説明を追加し、有効期限を選んで、**[保存]** を選びます。 キーの値を書き留めておきます。 Azure AD が有効な AKS クラスターを展開するときに、`Server application secret` としてこの値を参照します。

  ![アプリケーションの秘密キーを取得する](media/aad-integration/application-key.png)

4. Azure AD アプリケーションに戻り、**[設定]** > **[必要なアクセス許可]** > **[追加]** > **[API を選択します]** > **[Microsoft Graph]** > **[選択]** の順に選びます。

  ![Graph API を選択する](media/aad-integration/graph-api.png)

5. **[アプリケーションのアクセス許可]** で、**[ディレクトリ データの読み取り]** をオンにします。

  ![アプリケーションの Graph のアクセス許可を設定する](media/aad-integration/read-directory.png)

6. **[委任されたアクセス許可]** で、**[サインインとユーザー プロファイルの読み取り]** と **[ディレクトリ データの読み取り]** をオンにします。 終わったら保存します。

  ![アプリケーションの Graph のアクセス許可を設定する](media/aad-integration/delegated-permissions.png)

7. **[完了]** を選択し、API のリストから *[Microsoft Graph]* を選択してから、**[アクセス許可の付与]** を選択します。 現在のアカウントがテナント管理者ではない場合、このステップは失敗します。

  ![アプリケーションの Graph のアクセス許可を設定する](media/aad-integration/grant-permissions.png)

8. アプリケーションに戻り、**[アプリケーション ID]** を書き留めます。 Azure AD が有効な AKS クラスターを展開するときに、`Server application ID` としてこの値を参照します。

  ![アプリケーション ID を取得する](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>クライアント アプリケーションを作成する

もう 1 つの Azure AD アプリケーションは、Kubernetes CLI (kubectl) でログインするときに使われます。

1. **[Azure Active Directory]** > **[アプリの登録]** > **[新しいアプリケーションの登録]** を選択します。

  アプリケーションの名前を指定し、アプリケーションの種類として **[ネイティブ]** を選び、**[リダイレクト URI]** に URI 形式の値を入力します。 完了したら **[作成]** を選択します。

  ![AAD の登録を作成する](media/aad-integration/app-registration-client.png)

2. Azure AD アプリケーションから **[設定]** > **[必要なアクセス許可]** > **[追加]** > **[API を選択します]** の順に選び、このドキュメントの前のステップで作成したサーバー アプリケーションの名前を検索します。

  ![アプリケーションのアクセス許可を構成する](media/aad-integration/select-api.png)

3. アプリケーションの横にチェック マークを付けて、**[選択]** をクリックします。

  ![AKS AAD サーバー アプリケーション エンドポイントを選択する](media/aad-integration/select-server-app.png)

4. **[完了]** と **[アクセス許可の付与]** を選んで、このステップを完了します。

  ![アクセス許可を付与する](media/aad-integration/grant-permissions-client.png)

5. AD アプリケーションに戻り、**[アプリケーション ID]** を書き留めます。 Azure AD が有効な AKS クラスターを展開するときに、`Client application ID` としてこの値を参照します。

  ![アプリケーション ID を取得する](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>テナント ID を取得する

最後に、Azure テナントの ID を取得します。 この値は、AKS クラスターを展開するときにも使われます。

Azure portal から、**[Azure Active Directory]** > **[プロパティ]** の順に選び、**[ディレクトリ ID]** を書き留めます。 Azure AD が有効な AKS クラスターを展開するときに、`Tenant ID` としてこの値を参照します。

![Azure テナント ID を取得する](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>クラスターを展開する

AKS クラスターのリソース グループを作成するには、[az group create][az-group-create] コマンドを使います。

```azurecli
az group create --name myAKSCluster --location eastus
```

[az aks create][az-aks-create] コマンドを使って、クラスターを展開します。 以下のサンプル コマンドの値を、Azure AD アプリケーションを作成するときに収集した値に置き換えます。

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>RBAC のバインドを作成する

Azure Active Directory アカウントを AKS クラスターで使用できるようにするには、その前にまず、ロールのバインドまたはクラスター ロールのバインドを作成する必要があります。

最初に、[az aks get-credentials][az-aks-get-credentials] コマンドと `--admin` 引数を使って、管理者アクセス権でクラスターにログインします。

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

次に、以下のマニフェストを使って、Azure AD アカウントの ClusterRoleBinding を作成します。 ユーザー名は、Azure AD テナントのもので更新します。 この例では、クラスターのすべての名前空間へのフル アクセスをアカウントに付与します。

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: "user@contoso.com"
```

Azure AD グループのすべてのメンバーに対するロール バインドを作成することもできます。 Azure AD グループはグループ オブジェクト ID を使用して指定されます。

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

RBAC を使って Kubernetes クラスターをセキュリティで保護する方法について詳しくは、「[Using RBAC Authorization][rbac-authorization]」(RBAC の承認の使用) をご覧ください。

## <a name="access-cluster-with-azure-ad"></a>Azure AD でクラスターにアクセスする

次に、[az aks get-credentials][az-aks-get-credentials] コマンドを使って、管理者以外のユーザーのコンテキストを取得します。

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

いずれかの kubectl コマンドを実行すると、Azure による認証を求められます。 画面の指示に従います。

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

完了すると、認証トークンがキャッシュされます。 トークンの有効期限が切れたとき、または Kubernetes の構成ファイルが再作成されたときにのみ、ログインを再び求められます。

正常にサインインした後に承認エラー メッセージが表示される場合、サインインしようとしているユーザーが Azure AD で Guest ではないことを確認してください (これは、異なるディレクトリからのフェデレーション ログインを使用している場合によく発生する状況です)。
```console
error: You must be logged in to the server (Unauthorized)
```


## <a name="next-steps"></a>次の手順

「[Using RBAC Authorization][rbac-authorization]」(RBAC の承認の使用) ドキュメントで、RBAC を使った Kubernetes クラスターのセキュリティ保護についてさらに学習してください。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
