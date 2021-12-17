---
title: AKS クラスターから別の AD テナントの Azure コンテナー レジストリに対して認証する
description: 別の AD テナントの Azure コンテナー レジストリにアクセスするためのアクセス許可を使用して AKS クラスターのサービス プリンシパルを構成します
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 09/13/2021
ms.openlocfilehash: da7a03e1f46523b67666a4a0ab7ab53fba909720
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545474"
---
# <a name="pull-images-from-a-container-registry-to-an-aks-cluster-in-a-different-azure-ad-tenant"></a>コンテナー レジストリから別の Azure AD テナントの AKS クラスターにイメージをプルする

ある Azure Active Directory (Azure AD) テナントに Azure AKS クラスターがあり、別のテナントに Azure コンテナー レジストリがある場合があります。 この記事では、AKS サービス プリンシパルの資格情報を使用したテナント間認証を有効してコンテナー レジストリからプルする手順について説明します。

> [!NOTE]
> クラスターとコンテナー レジストリが異なるテナントにある場合、レジストリをアタッチして、AKS マネージド ID を使用して認証することはできません。

## <a name="scenario-overview"></a>シナリオの概要
この例の前提条件は、次のとおりです。

* AKS クラスターは **テナント A** にあり、Azure コンテナー レジストリは **テナント B** にあります。 
* AKS クラスターは、**テナント A** のサービス プリンシパル認証で構成されています。[AKS クラスターのサービス プリンシパル](../aks/kubernetes-service-principal.md)を作成して使用する方法の詳細を参照してください。

AKS クラスターのサブスクリプションの共同作成者ロールと、コンテナー レジストリのサブスクリプションの所有者ロールが少なくとも必要です。

次の手順を使用します。

* **テナント A** で新しいマルチテナント アプリ (サービス プリンシパル) を作成します。 
* **テナント B** でアプリをプロビジョニングします。
* **テナント B** のレジストリからプルするようにサービス プリンシパルを構成します。
* 新しいサービス プリンシパルを使用して認証するように、**テナント A** の AKS クラスターを更新します。


## <a name="step-by-step-instructions"></a>詳細な手順

### <a name="step-1-create-multitenant-azure-ad-application"></a>手順 1: マルチテナント Azure AD アプリケーションを作成する

1. **テナント A** の [Azure portal](https://portal.azure.com/) にサインインします。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で、 **[アプリの登録] > [+ 新規登録]** を選択します。
1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
1. **[リダイレクト URI]** に「 *https://www.microsoft.com* 」と入力します。
1. **[登録]** を選択します。
1. **[概要]** ページで、**アプリケーション (クライアント) ID** を書き留めます。 これは、手順 2 と手順 4 で使用します。

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/service-principal-overview.png" alt-text="サービス プリンシパルのアプリケーション ID":::
1. **[証明書とシークレット]** で、 **[クライアント シークレット]** の **[+ 新しいクライアント シークレット]** を選択します。
1. "*パスワード*" などの **説明** を入力し、 **[追加]** を選択します。
1. **[クライアント シークレット]** で、クライアント シークレットの値を書き留めます。 これは、手順 4 で AKS クラスターのサービス プリンシパルを更新する際に使用します。

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/configure-client-secret.png" alt-text="クライアント シークレットを構成する":::
### <a name="step-2-provision-the-service-principal-in-the-acr-tenant"></a>手順 2: ACR テナントでサービス プリンシパルをプロビジョニングする

1. **テナント B** の管理者アカウントを使用して、次のリンクを開きます。次に示すように、**テナント B の ID** と、マルチテナント アプリの **アプリケーション ID** (クライアント ID) を挿入します。

    ```console
    https://login.microsoftonline.com/<Tenant B ID>/oauth2/authorize?client_id=<Multitenant application ID>&response_type=code&redirect_uri=<redirect url>
    ```
1. **[組織の代理として同意する]** をオンにし、 **[同意する]** を選択します。 
    
    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-consent.png" alt-text="テナントにアプリケーションへのアクセスを許可する":::
 

### <a name="step-3-grant-service-principal-permission-to-pull-from-registry"></a>手順 3: レジストリからプルするためのアクセス許可をサービス プリンシパルに付与する

**テナント B** で、スコープがターゲット コンテナー レジストリに設定された AcrPull ロールをサービス プリンシパルに割り当てます。 ロールは、[Azure portal](../role-based-access-control/role-assignments-portal.md) または他のツールを使用して割り当てることができます。 Azure CLI を使用した手順の例については、「[サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md#use-an-existing-service-principal)」を参照してください。

:::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-acr-pull.png" alt-text="マルチテナント アプリに AcrPull ロールを割り当てる":::

### <a name="step-4-update-aks-with-the-azure-ad-application-secret"></a>手順 4: Azure AD アプリケーション シークレットで AKS を更新する

手順 1 で収集したマルチテナント アプリケーション (クライアント) ID とクライアント シークレットを使用して、[AKS サービス プリンシパルの資格情報を更新](../aks/update-credentials.md#update-aks-cluster-with-new-service-principal-credentials)します。

サービス プリンシパルの更新には数分かかることがあります。

## <a name="next-steps"></a>次のステップ

* [サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md)の詳細を確認する
* [Kubernetes のドキュメント](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)でイメージのプル シークレットの詳細を確認します
- [Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)の詳細を確認する
- Kubernetes クラスターから [Azure Container Registry の認証を受けるシナリオ](authenticate-kubernetes-options.md)について確認する


