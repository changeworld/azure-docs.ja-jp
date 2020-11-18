---
title: Azure Red Hat OpenShift 4 の組み込みのコンテナー レジストリを構成する
description: Azure Red Hat OpenShift 4 の組み込みのコンテナー レジストリを構成する
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413616"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 の組み込みのコンテナー レジストリを構成する

この記事では、Azure Red Hat OpenShift (ARO) 4 クラスター用の組み込みのコンテナー イメージ レジストリを構成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD の設定
> * OpenID Connect の設定
> * 組み込みのコンテナー イメージ レジストリへのアクセス

## <a name="prerequisites"></a>前提条件

* 「[Azure Red Hat OpenShift 4 クラスターを作成する](/azure/openshift/tutorial-create-cluster)」の手順に従いクラスターを作成します。 `az aro create` に対して `--pull-secret` 引数を指定してクラスターを作成する必要があります。  これは、この記事で必要とされるサインイン用に Azure AD を設定する場合に必要です。
* 「[Azure Red Hat OpenShift 4 クラスターに接続する](/azure/openshift/tutorial-connect-cluster)」の手順に従いクラスターに接続します。
   * この記事の後半で `oc` コマンドを使用するため、「OpenShift CLI をインストールする」の手順に従ってください。
   * `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` のようなクラスター コンソールの URL をメモします。 `<random>` と `<region>` の値は、この記事の後半で使用します。
   * `kubeadmin` 資格情報をメモします。 これは、この記事の後半で使用します。

## <a name="set-up-azure-active-directory"></a>Azure Active Directory を設定する

Azure Active Directory (Azure AD) は、OpenID Connect (OIDC) を実装しています。 OIDC により、Azure AD を使用して ARO クラスターにサインインできます。 次の手順に従い Azure AD を設定します。

1. Azure AD テナントを設定します。手順については、「[クイック スタート: テナントを設定する](/azure/active-directory/develop/quickstart-create-new-tenant)」を参照してください。 ご使用の Azure アカウントに既にテナントがある場合があります。 その場合、そのテナントに手順を実行するための十分な特権があれば、テナントの作成はスキップしてかまいません。 **テナント ID** をメモします。 この値は後で使用します。
2. 「[Azure Active Directory を使用してユーザーを追加または削除する](/azure/active-directory/fundamentals/add-users-azure-active-directory)」の手順に従い、少なくとも 1 名の Azure AD ユーザーを作成します。 これらのアカウントまたは自分のアカウントを使用して、アプリケーションをテストできます。 サインインのために、これらのアカウントのメール アドレスとパスワードをメモします。
3. Azure AD テナントで新しいアプリケーション登録を作成します。手順については、「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app)」を参照してください。 
   1. `<random>` と `<region>` の値に関する前提条件のメモを思い出してください。 これらの値を使用して、次の式に従って URI を作成します。

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. **[リダイレクト URI]** フィールドを処理する手順まで進んだら、前の手順で作成した URI を入力します。
   1. **[登録]** を選択します。
   1. アプリの **[概要]** ページで、こちらに示すような **[アプリケーション (クライアント) ID]** の値をメモします。
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Azure AD アプリケーションの [概要] ページ。":::

4. 新しいクライアント シークレットを作成します。 
   1. 新しく作成したアプリケーション登録で、左側のナビゲーション ウィンドウにある **[証明書とシークレット]** を選択します。
   1. **[新しいクライアント シークレット]** を選択します。
   1. **説明** を入力し、 **[追加]** を選択します。
   1. 生成された **クライアント シークレット** の値を保存します。 この値は、この記事の後半で使用します。

### <a name="add-openid-connect-identity-provider"></a>OpenID Connect ID プロバイダーを追加する

ARO には、組み込みのコンテナー レジストリが用意されています。  これにアクセスするには、こちらの手順に従って Azure AD OIDC を使用して ID プロバイダー (IDP) を構成します。

1. お使いのブラウザーから `kubeadmin` として OpenShift Web コンソールにサインインします。  これは、「[チュートリアル: Azure Red Hat OpenShift 4 クラスターに接続する](/azure/openshift/tutorial-connect-cluster)」の手順を実行したときに使用した手順と同じです。
1. 左側のナビゲーション ウィンドウで、 **[管理]**  >  **[クラスターの設定]** を選択します。
1. ページの中央で、 **[グローバル構成]** を選択します。
1. テーブルの **[Configuration Resource]\(構成リソース\)** 列で **[OAuth]** を見つけて選択します。
1. **[ID プロバイダー]** で、 **[追加]** を選択し、 **[OpenID Connect]** を選択します。
1. **[名前]** として **openid** を設定します。  この値は既に入力されている場合があります。
1. **[クライアント ID]** および **[クライアント シークレット]** を、前の手順の値に設定します。
1. 次の手順に従って **[発行者の URL]** の値を見つけます。
   1. URL `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` 内の **\<tenant-id>** を、「**Azure Active Directory を設定する**」セクションで保存したものに置き換えます。
   1. Azure portal の操作に使用していたのと同じブラウザーでこの URL を開きます。
   1. 返された JSON 本文でプロパティ **issuer** の値をコピーし、 **[発行者の URL]** というラベルのテキスト ボックスに貼り付けます。  **issuer** 値は、`https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` のようになります。
1. ページの下部に移動し、 **[追加]** を選択します。
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenShift の OpenID Connect。":::
1. ブラウザー ウィンドウの右上にある **[kube:admin]** ボタンを選択し、 **[ログアウト]** を選択して OpenShift Web コンソールからサインアウトします。

### <a name="access-the-built-in-container-image-registry"></a>組み込みのコンテナー イメージ レジストリへのアクセス

次の手順を使用すると、組み込みのレジストリにアクセスできます。

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>管理者にする Azure AD ユーザーを定義する

1. Azure AD ユーザーの資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。

   1. InPrivate、Incognito、またはその他の同等のブラウザー ウィンドウ機能を使用して、コンソールにサインインします。
   1. このウィンドウは、OIDC が有効になった後、外観が変わります。
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect が有効なサインイン ウィンドウ。":::
   1. **[openid]** を選択します。

   > [!NOTE]
   > ここでサインインに使用するユーザー名とパスワードをメモします。 このユーザー名とパスワードは、この記事および他の記事の他のアクションで管理者用に使用します。
1. 次の手順に従い、OpenShift CLI を使用してサインインします。  このプロセスは `oc login` と呼ばれています。
   1. Web コンソールの右上で、サインイン済みユーザーのコンテキスト メニューを展開し、 **[Copy Login Command]\(ログイン コマンドのコピー\)** を選択します。
   1. 必要に応じて、同じユーザーを使用して新しいタブ ウィンドウにサインインします。
   1. **[Display Token]\(トークンの表示\)** を選択します。
   1. こちらに示すように、 **[Login with this token]\(このトークンを使用してログイン\)** の下に表示されている値をクリップボードにコピーし、それをシェルで実行します。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. コンソールで `oc whoami` を実行し、 **\<aad-user>** としての出力をメモします。  この値は、この記事の後の方で使用します。
1. OpenShift Web コンソールからサインアウトします。 ブラウザー ウィンドウの右上にある **\<aad-user>** というラベルのボタンを選択し、 **[ログアウト]** を選択します。


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>レジストリの操作に必要なロールを Azure AD ユーザーに付与する

1. `kubeadmin` の資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。
1. 上の `oc login` の手順に従って、`kubeadmin` のトークンを使用して OpenShift CLI にサインインします。ただし、これは、`kubeadmin` を使用して Web コンソールにサインインした後に実行します。
1. 次のコマンドを実行して、**aad-user** の組み込みレジストリにアクセスできるようにします。

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   出力は次のようになります。

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   出力は次のようになります。

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   出力は次のようになります。

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   出力は次のようになります。

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>コンテナー レジストリ URL を取得する

次に示すように、`oc get route` コマンドを使用してコンテナー レジストリの URL を取得します。

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > **コンテナー レジストリ URL** のコンソール出力をメモします。 これは、このガイドと以降のガイドで完全修飾レジストリ名として使用します。

## <a name="next-steps"></a>次のステップ

OpenShift でアプリケーションをデプロイして、組み込みのコンテナー イメージ レジストリを使用します。  Java アプリケーションの場合は、[Azure Red Hat OpenShift 4 クラスターへの Open Liberty/WebSphere Liberty を使用した Java アプリケーションのデプロイ](howto-deploy-java-liberty-app.md)に関するハウツーガイドに従います。
