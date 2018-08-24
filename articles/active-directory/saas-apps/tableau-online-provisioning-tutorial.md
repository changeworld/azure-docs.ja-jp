---
title: 'チュートリアル: Tableau Online を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Tableau Online に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: v-wingf-msft
ms.openlocfilehash: bbee7f0e6022e2945563c102a851819734d52e77
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "40107115"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>チュートリアル: Tableau Online を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Tableau Online にプロビジョニングまたは Tableau Online からプロビジョニング解除するように構成するために、Tableau Online と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../active-directory-saas-app-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure AD テナント
*   [Tableau Online テナント](https://www.tableau.com/)
*   Admin アクセス許可がある Tableau Online のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合は、[Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm) に依存しています。この API は Tableau Online 開発者から入手できます。

## <a name="adding-tableau-online-from-the-gallery"></a>ギャラリーからの Tableau Online の追加
Azure AD で自動ユーザー プロビジョニング用に Tableau Online を構成する前に、Azure AD アプリケーション ギャラリーから Tableau Online を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Tableau Online を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] セクション][2]

3. Tableau Online を追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「 **Tableau Online**」と入力します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/AppSearch.png)

5. 結果パネルで **[Tableau Online]** を選択し、**[追加]** をクリックして Tableau Online を SaaS アプリケーションの一覧に追加します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/AppSearchResults.png)

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-tableau-online"></a>Tableau Online へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Tableau Online へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Tableau Online に割り当てることができます。

*   [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>ユーザーを Tableau Online に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Tableau Online に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Tableau Online にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>Tableau Online への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Tableau Online のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Tableau Online では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Tableau Online シングル サインオンのチュートリアル](tableauonline-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Azure AD で Tableau Online の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインし、**[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** に移動します。

2. SaaS アプリケーションの一覧から Tableau Online を選択します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/AppInstanceSearch.png)

3. **[プロビジョニング]** タブを選択します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションの **[ドメイン]**、**[管理ユーザー名]**、**[管理パスワード]**、**[コンテンツ URL]** に、Tableau Online アカウントの情報を入力します。

    *   **[ドメイン]** フィールドに、手順 6. に基づくサブドメインを設定します。

    *   **[管理ユーザー名]** フィールドに、Clarizen テナントの管理者アカウントのユーザー名を入力します。 例: admin@contoso.com.

    *   **[管理パスワード]** フィールドに、管理者ユーザー名に対応する管理者アカウントのパスワードを入力します。

    *   **[コンテンツ URL]** フィールドに、手順 6. に基づくサブドメインを設定します。

6. **[ドメイン]** および **[コンテンツ URL]** の値は、Tableau Online の管理者アカウントにログインした後、管理者ページの URL から抽出できます。

    *   Tableau Online アカウントの **[ドメイン]** は、URL の中の次の部分からコピーできます。![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    *   Tableau Online アカウントの **[コンテンツ URL]** は、次のセクションからコピーできます。この値は、アカウントのセットアップ中に定義されます。 この例では、"contoso" という値が該当します。![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 実際の**ドメイン**は、ここに示したものとは異なる場合があります。 


7. 手順 5 に示されたフィールドに値を入力したら、**[テスト接続]** をクリックして、Azure AD が Tableau Online に接続できることを確認します。 接続できない場合は、使用中の Tableau Online アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

10. **[Save]** をクリックします。

11. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Tableau]\(Azure Active Directory ユーザーを Tableau に同期する\)** を選択します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/UserMappings.png)

12. **[属性マッピング]** セクションで、Azure AD から Tableau Online に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Tableau Online のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

13. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Tableau]\(Azure Active Directory グループを Tableau に同期する\)** を選択します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

14. **[属性マッピング]** セクションで、Azure AD から Tableau Online に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Tableau Online のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

15. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../active-directory-saas-scoping-filters.md)の次の手順を参照してください。

16. Tableau Online に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

17. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Tableau Online にプロビジョニングするユーザーやグループを定義します。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

18. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![Tableau Online のプロビジョニング](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Tableau Online に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
