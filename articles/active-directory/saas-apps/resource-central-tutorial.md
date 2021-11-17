---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Resource Central – SAML SSO for Meeting Room Booking System との統合 | Microsoft Docs'
description: Azure Active Directory と Resource Central – SAML SSO for Meeting Room Booking System の間でシングル サインオンを構成する方法について学習します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: f77a6d0088d9ee45c1f39a46abbb6f88806b3e57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329497"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central--saml-sso-for-meeting-room-booking-system"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Resource Central – SAML SSO for Meeting Room Booking System の統合

このチュートリアルでは、Resource Central – SAML SSO for Meeting Room Booking System と Microsoft Azure Active Directory (Azure AD) を統合する方法について説明します。 Resource Central - Resource Central – SAML SSO for Meeting Room Booking System と Microsoft Azure Active Directory 統合すると、次のことが可能になります。

* Resource Central – SAML SSO for Meeting Room Booking System にアクセスできるユーザーを Microsoft Azure Active Directory で制御します。
* ユーザーが自分の Microsoft Azure Active Directory アカウントで Resource Central – SAML SSO for Meeting Room Booking System に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Resource Central – SAML SSO for Meeting Room Booking System でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Resource Central – SAML SSO for Meeting Room Booking System は **SP** 開始 SSO をサポート

* Resource Central – SAML SSO for Meeting Room Booking System は **Just In Time** ユーザー プロビジョニングをサポート

## <a name="add-resource-central--saml-sso-for-meeting-room-booking-system-from-the-gallery"></a>Resource Central – SAML SSO for Meeting Room Booking System のギャラリーからの追加

Microsoft Azure Active Directory への Resource Central – SAML SSO for Meeting Room Booking System の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Resource Central – SAML SSO for Meeting Room Booking System を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Resource Central – SAML SSO for Meeting Room Booking System**」と入力します。
1. 結果パネルから **[Resource Central – SAML SSO for Meeting Room Booking System]** を選択し、このアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-resource-central--saml-sso-for-meeting-room-booking-system"></a>Resource Central – SAML SSO for Meeting Room Booking System 用の Microsoft Azure Active Directory の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Microsoft Azure Active Directory を Resource Central – SAML SSO for Meeting Room Booking System 用に構成してテストします。 SSO が機能するために、Azure AD ユーザーと Resource Central – SAML SSO for Meeting Room Booking System の関連ユーザーとの間にリンク関係を確立する必要があります。

Microsoft Azure Active Directory SSO を Resource Central – SAML SSO for Meeting Room Booking System と一緒に構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
    1. **[Resource Central SAML SSO for Meeting Room Booking System のテスト ユーザーの作成](#create-resource-central-saml-sso-for-meeting-room-booking-system-test-user)** - Resource Central – SAML SSO for Meeting Room Booking System に、Microsoft Azure Active Directory の B.Simon を表すユーザーにリンクされた対応ユーザーを作成します。
1. **[Resource Central SAML SSO for Meeting Room Booking System SSO の構成](#configure-resource-central-saml-sso-for-meeting-room-booking-system-sso)** - アプリケーション側でシングル サインオン設定を構成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Resource Central – SAML SSO for Meeting Room Booking System** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** で、次のフィールドの値を入力します。

   1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<DOMAIN_NAME>/ResourceCentral`

   1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<DOMAIN_NAME>/ResourceCentral`

   1. **[応答 URL]** ボックスに、`https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらの値はリテラル値ではありません。 これらの値は、実際のサインオン URL、識別子、応答 URL の値で更新してください。 これらの値を取得するには、[Resource Central – SAML SSO for Meeting Room Booking System クライアント サポート チーム](mailto:st@aod.vn)にお問い合わせください。  Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** で、 **[証明書 (Base64)]** を見つけて **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Resource Central – SAML SSO for Meeting Room Booking System の設定]** で、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「`username@companydomain.extension`」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Resource Central – SAML SSO for Meeting Room Booking System へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーション リストで、 **[Resource Central – SAML SSO for Meeting Room Booking System]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ペインで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ペインの **[ユーザー]** の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールを割り当てる必要がある場合は、 **[ロールの選択]** からそれを選択できます。 このアプリに対してロールが設定されていない場合は、 **[既定のアクセス]** ロールが選択されていることを確認します。
1. **[割り当ての追加]** ペインで、 **[割り当て]** ボタンをクリックします。

### <a name="create-resource-central-saml-sso-for-meeting-room-booking-system-test-user"></a>Resource Central SAML SSO for Meeting Room Booking System のテスト ユーザーの作成

このセクションでは、**B.Simon** というユーザーが、**Resource Central – SAML SSO for Meeting Room Booking System** に作成されます。

1. Resource Central – SAML SSO for Meeting Room Booking System で、 **[セキュリティ]**  >  **[Persons]**  >  **[新規]** の順に選択します。
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="[新規] ボタンが強調表示された、Resource Central の [Persons]\(ユーザー\) ペインを示すスクリーンショット。":::

1. **[Person Details]\(ユーザーの詳細\)** で、 **[Display name]\(表示名\)** に、ユーザー「**B. Simon**」と入力します。 **[SMTP Address]\(SMTP アドレス\)** には、ユーザーの Azure AD ユーザー名を入力します。 (例: `B.Simon@contoso.com`)。

    :::image type="content" source="./media/resource-central/person.png" alt-text="Resource Central の [Person Details] \(ユーザーの詳細 \) ペインを示すスクリーンショット。":::

## <a name="configure-resource-central-saml-sso-for-meeting-room-booking-system-sso"></a>Resource Central SAML SSO for Meeting Room Booking System SSO の構成

このセクションでは、**Resource Central System Administrator** でシングル サインオンを構成します。

1. Resource Central – SAML SSO for Meeting Room Booking System システム管理者の **[External Authentication]\(外部認証\)** を選択します。
1.  **[Enable Configuration]\(構成を有効にする\)** で、 **[Yes]\(\はい\)** を選択します。

    ![Resource Central – SAML SSO for Meeting Room Booking System の [External Authentication]\(外部認証\) ペインで選択された [Enable Configuration]\(構成を有効にする\) オプションを示すスクリーンショット。](./media/resource-central/enable.png)

1. **[Authentication Protocol]\(認証プロトコル\)** で **[SAML2]** を選択します。 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Resource Central の [Authentication Protocol]\(認証プロトコル\) で選択された [SAML2] を示すスクリーンショット。":::

1. **[SAML2 Configuration]\(SAML2 構成\)** で、次のフィールドの値を入力します。

    1. **[Identifier (Entity ID)]\(識別子 (エンティティ ID)\)** 、 **[Login URL]\(ログイン URL\)** 、 **[Logout URL]\(ログアウト URL\)** 、および **[Azure AD Identifier]\(Azure AD 識別子\)** に、適切な URL を入力します。

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Resource Central の [SAML2 Configuration]\(SAML2 構成\) ペインのスクリーンショット。":::

        URL を **[Resource Central – SAML SSO for Meeting Room Booking System の設定]** ペインからコピーします。

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Resource Central の [Set up Resource Central]\(Resource Central のセットアップ\) ペインのスクリーンショット。":::

   1. **[Return URL]\(戻り先 URL\)** には「`https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler`」と入力します。
  
1. **[Certificate]\(証明書\)** については、証明書をアップロードし、パスワードを入力します。

   ![Resource Central – SAML SSO for Meeting Room Booking System の証明書セクションのスクリーンショット。](./media/resource-central/cert.png)
   
1. **[保存]** を選択します。

1. **Azure portal** に戻ります。 **[SAML 署名証明書]** で、証明書をアップロードし、パスワードを入力します。

   ![Azure portal の [証明書のインポート] ペインのスクリーンショット。](./media/resource-central/cert2.png).

1. **[追加]** を選択します。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、Azure AD のシングル サインオン構成をテストします。 シングル サインオンをテストするには、3 つのオプションがあります。

* Azure portal で、 **[このアプリケーションをテストします]** を選択します。 このリンクは、Resource Central – SAML SSO for Meeting Room Booking System サインオン URL にリダイレクトされます。この URL でログインを開始できます。

* Resource Central – SAML SSO for Meeting Room Booking System のサインオン URL に直接アクセスし、ログインを開始します。

   :::image type="content" source="./media/resource-central/test.png" alt-text="Resource Central シングル サインオンのテスト Web ページのスクリーンショット。":::

* Microsoft のマイ アプリ ポータルを使用します。 マイ アプリ ポータルで、 **[Resource Central – SAML SSO for Meeting Room Booking System]** タイルを選択し、Resource Central – SAML SSO for Meeting Room Booking System サインオン URL にリダイレクトします。 詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure AD によるシングル サインオンを Resource Central – SAML SSO for Meeting Room Booking System にセットアップしたら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
