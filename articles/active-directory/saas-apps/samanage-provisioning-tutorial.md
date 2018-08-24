---
title: 'チュートリアル: Samanage を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Samanage に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.date: 07/28/2018
ms.author: v-wingf-msft
ms.openlocfilehash: 98a1746804d0d5a9526e264f99d03ed4e8590ad6
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043561"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>チュートリアル: Samanage を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Samanage にプロビジョニングまたは Samanage からプロビジョニング解除するように構成するために、Samanage と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../active-directory-saas-app-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure AD テナント
*   Professional パッケージを含む [Samanage テナント](https://www.samanage.com/pricing/)
*   Admin アクセス許可がある Samanage のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合では、Professional パッケージを含むアカウントについて Samanage 開発者が使用できる [Samanage Rest API](https://www.samanage.com/api/) が必要です。

## <a name="adding-samanage-from-the-gallery"></a>ギャラリーからの Samanage の追加
Azure AD で自動ユーザー プロビジョニング用に Samanage を構成する前に、Azure AD アプリケーション ギャラリーから Samanage を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Samanage を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] セクション][2]

3. Samanage を追加するには、ダイアログの上部にある **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**Samanage**」と入力します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/AppSearch.png)

5. 結果パネルで **[Samanage]** を選択し、**[追加]** をクリックして Samanage を SaaS アプリケーションの一覧に追加します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/AppSearchResults.png)

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-samanage"></a>Samanage へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Samanage へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Samanage に割り当てることができます。

*   [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>ユーザーを Samanage に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Samanage に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Samanage にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Samanage への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Samanage のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Samanage では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Samanage シングル サインオンのチュートリアル](samanage-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Azure AD で Samanage の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインし、**[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** に移動します。

2. SaaS アプリケーションの一覧から Samanage を選択します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/AppInstanceSearch.png)

3. **[プロビジョニング]** タブを選択します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. **[管理者資格情報]** セクションの **[管理ユーザー名]** と **[管理パスワード]** に、Samanage アカウントの情報を入力します。 これらの値の例を次に示します。

    *   **[管理ユーザー名]** フィールドには、Samanage テナントの管理者アカウントのユーザー名を入力します。 例: admin@contoso.com.

    *   **[管理パスワード]** フィールドに、管理者ユーザー名に対応する管理者アカウントのパスワードを入力します。

6. 手順 5 の各フィールドに値を入力したら、**[テスト接続]** をクリックして、Azure AD が Samanage に接続できることを確認します。 接続できない場合は、使用中の Samanage アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/TestConnection.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. **[Save]** をクリックします。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Samanage]\(Azure Active Directory ユーザーを Samanage に同期する\)** を選びます。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/UserMappings.png)

10. **[属性マッピング]** セクションで、Azure AD から Samanage に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Samanage のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. グループ マッピングを有効にするには、**[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Samanage]\(Azure Active Directory グループを Samanage に同期する\)** を選択します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. **[有効化]** を **[はい]** に設定して、グループを同期します。 **[属性マッピング]** セクションで、Azure AD から Samanage に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Samanage のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../active-directory-saas-scoping-filters.md)の次の手順を参照してください。

14. Samanage に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Samanage にプロビジョニングするユーザーやグループを定義します。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![Samanage のプロビジョニング](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Samanage に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
