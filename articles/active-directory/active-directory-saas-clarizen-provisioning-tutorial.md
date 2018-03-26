---
title: 'チュートリアル: Clarizen を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Clarizen に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 3e75ce17f2806e4cb66f90f233b265398cdd0878
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>チュートリアル: Clarizen を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Clarizen にプロビジョニングまたは Clarizen からプロビジョニング解除するように構成するために、Clarizen と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](./active-directory-saas-app-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure AD テナント
*   [Enterprise Edition](https://www.clarizen.com/product/pricing/) プラン以上の有効な Clarizen テナント 
*   管理者アクセス許可がある Clarizen のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合では、Essential Edition プラン以上の Clarizen チームで使用できる [Clarizen API](https://api.clarizen.com/v2.0/services/) が必要です。

## <a name="adding-clarizen-from-the-gallery"></a>ギャラリーからの Clarizen の追加
Azure AD で自動ユーザー プロビジョニング用に Clarizen を構成する前に、Azure AD アプリケーション ギャラリーから Clarizen を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Clarizen を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] セクション][2]
    
3. Clarizen を追加するには、ダイアログの上部にある **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**Clarizen**」と入力します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. 結果ペインで **[Clarizen]** を選択し、**[追加]** をクリックして Clarizen を SaaS アプリケーションの一覧に追加します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Clarizen へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

自動ユーザー プロビジョニングを構成して有効にする前に、Clarizen へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Clarizen に割り当てることができます。

*   [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>ユーザーを Clarizen に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Clarizen に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Clarizen にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Clarizen への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Clarizen のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Clarizen では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Clarizen シングル サインオンのチュートリアル](active-directory-saas-clarizen-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Azure AD で Clarizen の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインし、**[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** に移動します。

2. SaaS アプリケーションの一覧から Clarizen を選択します。
 
    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. **[プロビジョニング]** タブを選択します。
    
    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションの **[ドメイン]**、**[管理ユーザー名]**、**[管理パスワード]** に、Clarizen アカウントの情報を入力します。 これらの値の例を次に示します。

    *   **[管理ユーザー名]** フィールドに、Clarizen テナントの管理者アカウントのユーザー名を入力します。 例: admin@contoso.com.

    *   **[管理パスワード]** フィールドに、管理者ユーザー名に対応する管理者アカウントのパスワードを入力します。

    *   **[ドメイン]** フィールドに、手順 6. に基づくサブドメインを設定します。
    
6. [Clarizen の Rest API ガイド](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2)の**認証**に関するセクションに記載されている手順に基づいて、Clarizen アカウントの **serverLocation** を取得します。 serverLocation を取得したら、以下で強調されている URL のサブドメインを使用して、**[ドメイン]** フィールドを設定します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. 手順 5 のフィールドに入力したら、**[テスト接続]** をクリックして Azure AD が Clarizen に接続できることを確認します。 接続できない場合は、使用中の Clarizen アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Clarizen]\(Azure Active Directory ユーザーを Clarizen に同期する\)** を選択します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Clarizen に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Clarizen のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Clarizen]\(Azure Active Directory グループを Clarizen に同期する\)** を選択します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. **[属性マッピング]** セクションで、Azure AD から Clarizen に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Clarizen のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](./active-directory-saas-scoping-filters.md)の次の手順を参照してください。

15. Clarizen に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Clarizen にプロビジョニングするユーザーやグループを定義します。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![Clarizen のプロビジョニング](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Clarizen に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](./active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
