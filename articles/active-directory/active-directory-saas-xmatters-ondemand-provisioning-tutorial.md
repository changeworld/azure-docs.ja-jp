---
title: 'チュートリアル: xMatters OnDemand を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを xMatters OnDemand に自動的にプロビジョニングおよびプロビジョニング解除する方法を説明します。
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
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: e0b945a99766ee52cb357f54d7135fbbdf1fada2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353791"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>チュートリアル: xMatters OnDemand を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを xMatters OnDemand に追加または Cornerstone OnDemand から削除するように構成するために、xMatters OnDemand と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](./active-directory-saas-app-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure AD テナント
*   [スターター](https://www.xmatters.com/pricing)以上のプランが有効になっている xMatters OnDemand テナント 
*   管理者アクセス許可がある xMatters OnDemand のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合では、Standard プラン以上の xMatters OnDemand チームで使用できる [xMatters OnDemand API](https://help.xmatters.com/xmAPI) が必要です。

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>ギャラリーからの xMatters OnDemand の追加

Azure AD で自動ユーザー プロビジョニング用に xMatters OnDemand を構成する前に、Azure AD アプリケーション ギャラリーから xMatters OnDemand を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから xMatters OnDemand を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] セクション][2]
    
3. xMatters OnDemand を追加するには、ダイアログの上部にある **[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**xMatters OnDemand**」と入力します。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. 結果ウィンドウで **[xMatters OnDemand]** を選択し、**[追加]** ボタンをクリックして xMatters OnDemand を SaaS アプリケーションの一覧に追加します。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>ユーザーを xMatters OnDemand に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成および有効にする前に、xMatters OnDemand にアクセスが必要な Azure AD のユーザーやグループを決定する必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを xMatters OnDemand に割り当てることができます。

*   [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>ユーザーを xMatters OnDemand に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを xMatters OnDemand に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをおすすめします。 後でユーザーやグループを追加で割り当てられます。

*   xMatters OnDemand にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>xMatters OnDemand への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて xMatters OnDemand のユーザーやグループを作成、更新、削除する手順について説明します。

> [!TIP]
> xMatters OnDemand では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[xMatters OnDemand シングル サインオンのチュートリアル](active-directory-saas-xmatters-ondemand-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>次のようにして、Azure AD で xMatters OnDemand の自動ユーザー プロビジョニングを構成します。

1. [Azure Portal](https://portal.azure.com) にサインインし、**[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** に移動します。

2. SaaS アプリケーションの一覧から xMatters OnDemand を選択します。
 
    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. **[プロビジョニング]** タブを選択します。
    
    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションに、xMatters OnDemand アカウントの **[管理ユーザー名]**、**[管理パスワード]**、**[ドメイン]** を入力します。

    *   **[管理ユーザー名]** フィールドに、xMatters OnDemand テナントの管理者アカウントのユーザー名を入力します。 例: admin@contoso.com.

    *   **[管理パスワード]** フィールドに、管理ユーザー名に対応するパスワードを入力します。

    *   **[ドメイン]** フィールドに、xMatters OnDemand テナントのサブドメインを設定します。
    例: https://my-tenant.xmatters.com のテナント URL があるアカウントの場合、サブドメインは **my-tenant** になります。

6. 手順 5 のフィールドに入力したら、**[テスト接続]** をクリックして Azure AD が xMatters OnDemand に接続できることを確認します。 接続できない場合は、使用中の xMatters OnDemand アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. **[Save]** をクリックします。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to xMatters OnDemand](Azure Active Directory ユーザーを xMatters OnDemand に同期する)** を選択します。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. **[属性マッピング]** セクションで、Azure AD から xMatters OnDemand に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で xMatters OnDemand のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to xMatters OnDemand](Azure Active Directory グループを xMatters OnDemand に同期する)** を選択します。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. **[属性マッピング]** セクションで、Azure AD から xMatters OnDemand に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で xMatters OnDemand のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](./active-directory-saas-scoping-filters.md)の次の手順を参照してください。

14. xMatters OnDemand に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. **[設定]** セクションの **[スコープ]** で目的の値を選択して、xMatters OnDemand にプロビジョニングするユーザーやグループを定義します。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![xMatters OnDemand のプロビジョニング](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって xMatters OnDemand に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](./active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
