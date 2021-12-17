---
title: 'チュートリアル: Azure Active Directory と Learning Pool LMS の統合 | Microsoft Docs'
description: Azure Active Directory と Learning Pool LMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: jeedes
ms.openlocfilehash: ab0ada258b8df3ccca7086f2d8c84a2b4b1ee336
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313480"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-pool-lms"></a>チュートリアル: Azure Active Directory と Learning Pool LMS の統合

このチュートリアルでは、Learning Pool LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Learning Pool LMS を統合すると、次のことができます。

* Learning Pool LMS にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Learning Pool LMS に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンを使用した Learning Pool LMS に対するアクティブなサブスクリプション。

> [!NOTE]
> シングル サインオン プロジェクトを開始すると、Learning Pool LMS Delivery チームのメンバーが、このプロセスについて説明します。 Learning Pool LMS Delivery チームのメンバーと連絡を取っていない場合は、Learning Pool LMS のアカウント マネージャーにお問い合わせください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Learning Pool LMS では、**SP** Initiated SSO がサポートされます。

## <a name="adding-learning-pool-lms-from-the-gallery"></a>ギャラリーからの Learning Pool LMS の追加

Azure AD への Learning Pool LMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Learning Pool LMS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Learning Pool LMS**」と入力します。
1. 結果のパネルから **[Learning Pool LMS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-learning-pool-lms"></a>Learning Pool LMS の Azure AD SSO の構成とテスト

既存の Azure ユーザーを使用して、Learning Pool LMS に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Learning Pool LMS の関連ユーザーとの間にリンク関係を確立する必要があります。

Learning Pool LMS に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD テスト ユーザーの割り当て](#assign-an-azure-ad-user)** - ユーザーが Azure AD シングル サインオンを使用できるようにします。
1. **[Learning Pool LMS SSO の構成](#configure-learning-pool-lms-sso)** - アプリケーション側でシングル サインオン設定を構成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Learning Pool LMS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://parliament.preview.Learningpool.com/auth/shibboleth/index.php`」と入力します。

    > [!Note]
    > **識別子** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

5. Azure ユーザーと Learning Pool LMS のユーザーの照合に使用される、少なくとも 1 つの属性を送信する必要があります。 通常は既定の属性で十分ですが、いくつかのカスタム属性を使用して送信する必要がある場合があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして [ユーザー属性] ダイアログを開き、必要に応じて属性を追加します。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン** を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。 

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![[新しい要求の追加] オプションが備わっている [ユーザー要求] のスクリーンショット。](common/new-save-attribute.png)

    ![スクリーンショットは、説明されている値を入力できる [ユーザー要求の管理] ダイアログ ボックスを示しています。](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

7. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[アプリのフェデレーション メタデータ URL]** のコピー ボタンをクリックして、その URL を Learning Pool Delivery チームに渡します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="assign-an-azure-ad-user"></a>Azure AD ユーザーの割り当て

このセクションでは、既存の Azure AD ユーザーに Learning Pool LMS へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Learning Pool LMS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] リストで適切なユーザーを選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-learning-pool-lms-sso"></a>Learning Pool LMS SSO の構成

Learning Pool Delivery チームは、 **[アプリのフェデレーション メタデータ URL]** を使用して、SAML2 接続を受け入れるように LMS を構成します。 接続が正しく構成されていることを確認するために、いくつかのテスト手順を実行するように求めるメッセージが表示され、Learning Pool Delivery チームがこのプロセスについて説明します。

### <a name="test-sso"></a>SSO のテスト

Learning Pool Delivery チームがテスト プロセスを説明します。

## <a name="next-steps"></a>次のステップ

Learning Pool LMS を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
