---
title: 'クイックスタート: エンタープライズ アプリケーションのシングル サインオンを有効にする'
titleSuffix: Azure AD
description: Azure Active Directory でエンタープライズ アプリケーションのシングル サインオンを有効にします。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/21/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: 337561853bb34c8ecc67483dc8b94448b0b853a1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548522"
---
# <a name="quickstart-enable-single-sign-on-for-an-enterprise-application"></a>クイックスタート: エンタープライズ アプリケーションのシングル サインオンを有効にする

このクイックスタートでは、Azure Active Directory 管理センターを使用して、Azure Active Directory (Azure AD) テナントに追加したエンタープライズ アプリケーションのシングル サインオン (SSO) を有効にします。 SSO を構成すると、ユーザーは自分の Azure AD 資格情報を使用してサインインできるようになります。 

Azure AD には、SSO を使用する、あらかじめ統合された何千ものアプリケーションが含まれるギャラリーがあります。 このクイックスタートでは、例として **Azure AD SAML Toolkit** というエンタープライズ アプリケーションを使用しますが、この概念はあらかじめ構成されている [ギャラリーのエンタープライズ アプリケーション](../saas-apps/tutorial-list.md)のほとんどに適用されます。

このクイックスタートの手順をテストするには、非運用環境を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件

SSO を構成するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- 「[クイックスタート: ユーザー アカウントを作成して割り当てる](add-application-portal-assign-users.md)」の手順を完了していること。

## <a name="enable-single-sign-on"></a>シングル サインオンの有効化

アプリケーション用に SSO を有効にするには:

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動し、前提条件の一覧で示されているロールのいずれかを使用してサインインします。
1. 左側のメニューで、 **[エンタープライズ アプリケーション]** を選択します。 **[すべてのアプリケーション]** ペインが開き、Azure AD テナントのアプリケーションの一覧が表示されます。 使用するアプリケーションを検索して選択します。 たとえば、**Azure AD SAML Toolkit 1** などです。
1. 左側のメニューの **[管理]** セクションで、 **[シングル サインオン]** を選択して、編集用の **[シングル サインオン]** ペインを開きます。
1. **[SAML]** を選択して SSO の構成ページを開きます。 アプリケーションの構成が済むと、ユーザーは Azure AD テナントから自分の資格情報を使用してそれにサインインできるようになります。
1. SAML ベースの SSO に Azure AD を使用するようにアプリケーションを構成するプロセスは、アプリケーションによって異なります。 ギャラリー内のどのエンタープライズ アプリケーションについても、リンクを使用して、アプリケーションの構成に必要な手順に関する情報を見つけることができます。 このクイックスタートでは、**Azure AD SAML Toolkit** の手順について説明します。

    :::image type="content" source="media/add-application-portal-setup-sso/saml-configuration.png" alt-text="エンタープライズ アプリケーションのシングル サインオンを構成する。":::

1. **[Azure AD SAML Toolkit 1 のセットアップ]** セクションで、後で使用する **[ログイン URL]** 、 **[Azure AD 識別子]** 、 **[ログアウト URL]** の各プロパティの値を記録します。

## <a name="configure-single-sign-on-in-the-tenant"></a>テナントでシングル サインオンを構成する

サインインと応答 URL の値を追加し、証明書をダウンロードして Azure AD で SSO の構成を開始します。

Azure AD で SSO を構成するには:

1. Azure portal の **[Set up single sign-on]\(シングル サインオンのセットアップ\)** ペインで、 **[基本的な SAML 構成]** セクションの **[編集]** を選択します。 
1. **[応答 URL (Assertion Consumer Service URL)]** に「`https://samltoolkit.azurewebsites.net/SAML/Consume`」と入力します。
1. **[サインオン URL]** に「`https://samltoolkit.azurewebsites.net/`」と入力します。
1. **[保存]** を選択します。
1. **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** の **[ダウンロード]** を選択して SAML 署名証明書をダウンロードし、後で使用できるように保存します。

## <a name="configure-single-sign-on-in-the-application"></a>アプリケーションでシングル サインオンを構成する

アプリケーションでシングル サインオンを使用するには、ユーザー アカウントをアプリケーションに登録し、前に記録した SAML 構成の値を追加する必要があります。

### <a name="register-the-user-account"></a>ユーザー アカウントを登録する

ユーザー アカウントをアプリケーションに登録するには:

1. 新しいブラウザー ウィンドウを開き、アプリケーションのサインイン URL を参照します。 **Azure AD SAML Toolkit** アプリケーションの場合、アドレスは `https://samltoolkit.azurewebsites.net` です。
1. ページの右上隅にある **[登録]** を選択します。

    :::image type="content" source="media/add-application-portal-setup-sso/toolkit-register.png" alt-text="Azure AD SAML Toolkit アプリケーションでユーザー アカウントを登録する。":::

1. **[電子メール]** には、アプリケーションにアクセスするユーザーのメール アドレスを入力します。 たとえば、前のクイックスタートでは、`contosouser1@contoso.com` のアドレスを使用するユーザー アカウントを作成しました。 必ず `contoso.com` を自分のテナントのドメインに変更してください。
1. **[パスワード]** を入力し、確認用にもう一度入力します。
1. **[登録]** を選択します。

### <a name="configure-saml-settings"></a>SAML の設定を構成する

アプリケーションの SAML 設定を構成するには:

1. 作成したユーザー アカウントの資格情報を使用してサインインし、ページの左上隅にある **[SAML Configuration]\(SAML の構成\)** を選択します。
1. ページ中央の **[作成]** を選択します。
1. **[ログイン URL]** 、 **[Azure AD 識別子]** 、 **[ログアウト URL]** に、前に記録した値を入力します。
1. **[ファイルの選択]** を選択して、前にダウンロードした証明書をアップロードします。
1. **[作成]** を選択します。
1. 後で使用する **[SP Initiated Login URL]\(SP によって開始されたログイン URL\)** と **[Assertion Consumer Service (ACS) URL]\(Assertion Consumer Service (ACS) URL\)** の値をコピーします。

## <a name="update-single-sign-on-values"></a>シングル サインオンの値を更新する

**[SP Initiated Login URL]\(SP によって開始されたログイン URL\)** と **[Assertion Consumer Service (ACS) URL]\(Assertion Consumer Service (ACS) URL\)** の記録した値を使用して、テナントのシングル サインオンの値を更新します。

シングル サインオンの値を更新するには:

1. Azure portal の **[Set up single sign-on]\(シングル サインオンのセットアップ\)** ペインで、 **[基本的な SAML 構成]** セクションの **[編集]** を選択します。 
1. **[応答 URL (Assertion Consumer Service URL)]** には、前に記録した **Assertion Consumer Service (ACS) URL** の値を入力します。
1. **[サインオン URL]** には、前に記録した **[SP Initiated Login URL]\(SP によって開始されたログイン URL\)** の値を入力します。
1. **[保存]** を選択します。

## <a name="test-single-sign-on"></a>シングル サインオンのテスト

**[Set up single sign-on]\(シングル サインオンのセットアップ\)** ペインから、シングル サインオンの構成をテストできます。

SSO をテストするには:

1. **[Azure AD SAML Toolkit 1 によるシングル サインオンのテスト]** セクションの **[Set up single sign-on]\(シングル サインオンのセットアップ\)** ペインで、 **[テスト]** を選択します。
1. アプリケーションに割り当てたユーザー アカウントの Azure AD 資格情報を使用して、アプリケーションにサインインします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のクイックスタートを行う予定の場合は、作成したエンタープライズ アプリケーションをそのままにします。 そうでない場合は、削除してテナントをクリーンアップしてもかまいません。 詳細については、[アプリケーションの削除](delete-application-portal.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

エンタープライズ アプリケーションのプロパティを構成する方法について学習します。
> [!div class="nextstepaction"]
> [アプリケーションの構成](add-application-portal-configure.md)
