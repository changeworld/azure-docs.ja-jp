---
title: チュートリアル:Snowflake を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Snowflake に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2aaf1d2b377abc0b10b0b14de03d01c7f6fae5b7
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767758"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>チュートリアル:Snowflake を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD によって自動的にユーザーまたはグループが Snowflake にプロビジョニングされる、または Snowflake からプロビジョニング解除されるように構成するため、Snowflake と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Snowflake テナント](https://www.Snowflake.com/pricing/)。
* Admin アクセス許可がある Snowflake のユーザー アカウント。

## <a name="assigning-users-to-snowflake"></a>Snowflake へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Snowflake へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Snowflake に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>ユーザーを Snowflake に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Snowflake に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Snowflake にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-snowflake-for-provisioning"></a>Snowflake をプロビジョニング用に設定する

Azure AD での自動ユーザー プロビジョニング用に Snowflake を構成する前に、Snowflake 上で SCIM プロビジョニングを有効にする必要があります。

1. Snowflake の管理コンソールにサインインします。 下に示されているクエリを、強調表示されているワークシートに入力し、 **[実行]** をクリックします。

    ![Snowflake 管理コンソール](media/Snowflake-provisioning-tutorial/image00.png)

2.  SCIM アクセス トークンが、Snowflake テナントに対して生成されます。 それを取得するには、下の強調表示されているリンクをクリックします。

    ![Snowflake での SCIM の追加](media/Snowflake-provisioning-tutorial/image01.png)

3. 生成されたトークンの値をコピーし、 **[完了]** をクリックします。 この値を、Azure portal の Snowflake アプリケーションの [プロビジョニング] タブの **[シークレット トークン]** フィールドに入力します。

    ![Snowflake での SCIM の追加](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>ギャラリーから Snowflake を追加する

Azure AD で自動ユーザー プロビジョニング用に Snowflake を構成するには、Azure AD アプリケーション ギャラリーから Snowflake をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Snowflake を追加するには、次の手順を行います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Snowflake**」と入力し、結果ウィンドウで **Snowflake** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Snowflake](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Snowflake への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Snowflake のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Snowflake では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、「[チュートリアル:Azure Active Directory と Snowflake の統合](Snowflake-tutorial.md)」で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Azure AD で Snowflake の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Snowflake]** を選択します。

    ![アプリケーションの一覧の Snowflake のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. [管理者資格情報] セクションのテナント URL に「`https://<Snowflake Account URL>/scim/v2`」と入力します。 テナント URL の例は次のとおりです: `https://acme.snowflakecomputing.com/scim/v2`

6. **[シークレット トークン]** に先ほど取得した**SCIM 認証トークン**の値を入力します。 **[接続テスト]** をクリックして、Azure AD から Snowflake に接続できることを確認します。 接続できない場合は、使用中の Snowflake アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[保存]** をクリックします。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Snowflake]\(Azure Active Directory ユーザーを Snowflake に同期する\)** を選択します。

    ![Snowflake のユーザー マッピング](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. **[属性マッピング]** セクションで、Azure AD から Snowflake に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Snowflake のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Snowflake のユーザー属性](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Snowflake]\(Azure Active Directory グループを Snowflake に同期する\)** を選択します。

    ![Snowflake のグループ マッピング](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. **[属性マッピング]** セクションで、Azure AD から Snowflake に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Snowflake のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Snowflake のグループ属性](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

14. Snowflake に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

15. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Snowflake にプロビジョニングするユーザーやグループを定義します。 このオプションが使用できない場合は、[Admin Credentials]\(管理者の資格情報\) の下の必須フィールドを構成し、 **[保存]** をクリックして、ページを更新してください。 

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

16. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Snowflake に対して実行されたすべてのアクションが記載されています。

    Azure AD プロビジョニング ログの読み方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Snowflake で生成される SCIM トークンの有効期間は 6 か月です。 プロビジョニングの同期を続行するには、有効期限が切れる前にこれらを更新する必要があることに注意してください。 

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
