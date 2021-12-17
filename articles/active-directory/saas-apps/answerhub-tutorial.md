---
title: 'チュートリアル: Azure AD SSO と AnswerHub の統合'
description: Azure Active Directory と AnswerHub の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jeedes
ms.openlocfilehash: 9eb25fd7cc96c960ff8020a29b8801d0e8ea8853
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318399"
---
# <a name="tutorial-azure-ad-sso-integration-with-answerhub"></a>チュートリアル: Azure AD SSO と AnswerHub の統合

このチュートリアルでは、AnswerHub と Azure Active Directory (Azure AD) を統合する方法について説明します。 AnswerHub を Azure AD と統合すると、次のことができます。

* AnswerHub にアクセスできるユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して AnswerHub に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AnswerHub でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* AnswerHub では、SP Initiated SSO がサポートされます。

## <a name="add-answerhub-from-the-gallery"></a>ギャラリーからの AnswerHub の追加

AnswerHub の Azure AD への統合を構成するには、AnswerHub をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**AnswerHub**」と入力します。
1. 結果のパネルから **[AnswerHub]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="set-up-and-test-azure-ad-sso-for-answerhub"></a>AnswerHub に対する Azure AD SSO の設定とテスト

**B.Simon** というテスト ユーザーを使用して、AnswerHub に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと AnswerHub の関連ユーザーとの間にリンク関係を確立する必要があります。

AnswerHub に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[AnswerHub SSO の構成](#configure-answerhub-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[AnswerHub のテスト ユーザーの作成](#create-answerhub-test-user)** - AnswerHub で B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **AnswerHub** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。`https://<company>.answerhub.com`
    
    b. **[サインオン URL]** ボックスに、次のパターンで URL を入力します。`https://<company>.answerhub.com`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 この値を取得するには、[AnswerHub サポート チーム](mailto:success@answerhub.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、実際の要件に従って、 **[証明書 (Base64)]** の横にある **[ダウンロード]** リンクを選択し、証明書を自分のコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

6. **[AnswerHub のセットアップ]** セクションで、実際の要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、AnswerHub へのアクセスを許可して、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、**[AnswerHub]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-answerhub-sso"></a>AnswerHub SSO を構成する

このセクションでは、AnswerHub のシングル サインオンを設定します。  

**AnswerHub のシングル サインオンを構成するには:**

1. 別の Web ブラウザー ウィンドウで、AnswerHub 企業サイトに管理者としてサインインします。

    > [!NOTE]
    > AnswerHub の構成について不明な点がある場合は、[AnswerHub サポート チーム](mailto:success@answerhub.com.)にお問い合わせください。

2. **[Administration]** に移動します。

3. **[User and Groups]\(ユーザーとグループ\)** タブの左側のウィンドウで、 **[Social Settings]\(ソーシャル設定\)** セクションの **[SAML Setup]\(SAML セットアップ\)** を選択します。

4. **[IDP Config]\(IDP 構成\)** タブで、以下の手順を実行します。

    ![このスクリーンショットは、[User and Groups]\(ユーザーとグループ\) タブが選択された状態の AnswerHub ページを示しています。](./media/answerhub-tutorial/admin.png "[SAML 設定]")  

    a. **[IDP Login URL]\(IDP ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。

    b. **[IDP Logout URL]\(IDP ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** を貼り付けます。

    c. **[IDP Name Identifier Format]\(IDP 名前識別子形式\)** ボックスに、Azure portal の **[ユーザー属性]** セクションで選択した **識別子** の値を入力します。

    d. **[Keys and Certificates]\(キーと証明書\)** を選択します。

5. **[Keys and Certificates]\(キーと証明書\)** セクションで、以下の手順を実行します。

    ![[Keys and Certificates] (キーと証明書)](./media/answerhub-tutorial/users.png "Keys and Certificates")  

    a. Azure portal からダウンロードした Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーしてから、 **[IDP Public Key (x509 Format)]\(IDP 公開キー (x509 形式)\)** ボックスに貼り付けます。

    b. **[保存]** を選択します。

6. **[IDP Config]\(IDP 構成\)** タブで、もう一度 **[Save]\(保存\)** を選択します。

### <a name="create-answerhub-test-user"></a>AnswerHub のテスト ユーザーの作成

Azure AD ユーザーが AnswerHub にサインインできるようにするには、それらを AnswerHub に追加する必要があります。 AnswerHub では、このタスクを手動で実行します。

**ユーザー アカウントを設定するには:**

1. **AnswerHub** 企業サイトに管理者としてサインインします。

2. **[Administration]** に移動します。

3. **[User & Groups]\(ユーザーとグループ\)** タブを選択します。

4. 左側のウィンドウの **[Manage Users]\(ユーザーの管理\)** セクションで、 **[Create or import users]\(ユーザーの作成またはインポート\)** を選択し、 **[Users & Groups]\(ユーザーとグループ\)** を選択します。

    ![このスクリーンショットは、[User and Groups]\(ユーザーとグループ\) タブが選択され、[Create or import users]\(ユーザーの作成またはインポート\) リンクがコールアウトされた状態の AnswerHub ページを示しています。](./media/answerhub-tutorial/groups.png "ユーザーとグループ")

5. 追加したい有効な Azure AD アカウントの **メール アドレス**、**ユーザー名**、**パスワード** を適切なボックスに入力してから、 **[Save]\(保存\)** を選択します。

> [!NOTE]
> AnswerHub から提供されている他のユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントを設定できます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる AnswerHub のサインオン URL にリダイレクトされます。 

* AnswerHub のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [AnswerHub] タイルをクリックすると、AnswerHub のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

AnswerHub を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
