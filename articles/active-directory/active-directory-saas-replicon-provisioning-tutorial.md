---
title: 'チュートリアル: Replicon を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Replicon に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: 8d612012505ea43a3635650c6a38fe993b8e57f6
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>チュートリアル: Replicon を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Replicon にプロビジョニングまたは Replicon からプロビジョニング解除するように構成するために、Replicon と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](./active-directory-saas-app-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure AD テナント
*   [Plus プラン](https://www.replicon.com/time-bill-pricing/)以上の有効な Replicon テナント
*   Admin アクセス許可がある Replicon のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合では、Plus プラン以上の Replicon チームで使用できる [Replicon API](https://www.replicon.com/help/developers) が必要です。

## <a name="adding-replicon-from-the-gallery"></a>ギャラリーからの Replicon の追加
Azure AD で自動ユーザー プロビジョニング用に Replicon を構成する前に、Azure AD アプリケーション ギャラリーから Replicon を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Replicon を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] セクション][2]
    
3. Replicon を追加するには、ダイアログの上部にある **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**Replicon**」と入力します。

    ![Replicon アプリケーションの検索](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. 結果パネルで **[Replicon]** を選択し、**[追加]** をクリックして Replicon を SaaS アプリケーションの一覧に追加します。

    ![Replicon の検索結果](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Replicon アプリケーションの作成](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Replicon へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Replicon へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Replicon に割り当てることができます。

*   [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>ユーザーを Replicon に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Replicon に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Replicon にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Replicon への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Replicon のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Replicon では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Replicon シングル サインオンのチュートリアル](active-directory-saas-replicon-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Azure AD で Replicon の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインし、**[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** に移動します。

2. SaaS アプリケーションの一覧から Replicon を選択します。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. **[プロビジョニング]** タブを選択します。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. **[管理者資格情報]** セクションに、Replicon アカウントの **[管理ユーザー名]**、**[管理パスワード]**、**[会社 ID]**、 **[ドメイン]**を入力します。 これらの値の例を次に示します。

    *   **[管理ユーザー名]** フィールドには、Replicon テナントの管理者アカウントのユーザー名を入力します。 例: contosoadmin。

    *   **[管理パスワード]** フィールドに、管理ユーザー名に対応するパスワードを入力します。

    *   **[会社 ID]**フィールドには、Replicon テナントの会社 ID を入力します。 例: 下記のログインに基づく会社 ID は、Contoso です。

    ![Replicon のログイン](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   **[ドメイン]** フィールドには、手順 6 で説明しているドメインを入力します。
    
6. [Replicon サービス ヘルプ](https://www.replicon.com/help/determining-the-url-for-your-service-calls)で説明されている手順に基づいて、Replicon テナント アカウントの **serviceEndpointRootURL** を取得します。 URL を取得すると、下記に示すように、**ドメイン**は **serviceEndpointRootURL** のサブドメインになります 。 

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. 手順 5 の各フィールドに値を入力したら、**[テスト接続]** をクリックして、Azure AD が Replicon に接続できることを確認します。 接続できない場合は、使用中の Replicon アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Replicon]\(Azure Active Directory ユーザーを Replicon に同期する\)** を選択します。
    
    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Replicon に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Replicon のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](./active-directory-saas-scoping-filters.md)の次の手順を参照してください。

13. Replicon に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Replicon にプロビジョニングするユーザーやグループを定義します。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![Replicon のプロビジョニング](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Replicon に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](./active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
