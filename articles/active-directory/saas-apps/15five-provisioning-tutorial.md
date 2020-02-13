---
title: チュートリアル:15Five を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: 15Five に対してユーザー アカウントの自動的なプロビジョニングとプロビジョニング解除を実行するように Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f1f66a7b69048180bc41c8f2fa432598f00f7f09
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059268"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>チュートリアル:15Five を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを 15Five にプロビジョニングまたは 15Five からプロビジョニング解除するように構成するために、15Five と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [15Five テナント](https://www.15five.com/pricing/)。
* Admin アクセス許可がある 15Five のユーザー アカウント

## <a name="assigning-users-to-15five"></a>15Five へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、15Five へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを 15Five に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>ユーザーを 15Five に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを 15Five に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* 15Five にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-15five-for-provisioning"></a>プロビジョニングのために 15Five を設定する

Azure AD での自動ユーザー プロビジョニング用に 15Five を構成する前に、15Five 上で SCIM プロビジョニングを有効にする必要があります。

1. [15Five 管理コンソール](https://my.15five.com/)にサインインします。 **[機能] > [統合]** の順に移動します。

    ![15Five 管理コンソール](media/15five-provisioning-tutorial/integration.png)

2.  **[SCIM 2.0]** をクリックします。

    ![15Five 管理コンソール](media/15five-provisioning-tutorial/image00.png)

3.  **[SCIM integration]\(SCIM 統合\) > [Generate OAuth token]\(OAuth トークンの生成\)** の順に移動します。

    ![15Five での SCIM の追加](media/15five-provisioning-tutorial/image02.png)

4.  **[SCIM 2.0 base URL]\(SCIM 2.0 ベース URL\)** と **[アクセス トークン]** の値をコピーします。 この値を、Azure portal の 15Five アプリケーションの [プロビジョニング] タブにある **[テナント URL]** および **[シークレット トークン]** フィールドに入力します。
    
    ![15Five での SCIM の追加](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>ギャラリーからの 15Five の追加

Azure AD で自動ユーザー プロビジョニング用に 15Five を構成するには、Azure AD アプリケーション ギャラリーから 15Five をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから 15Five を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**15Five**」と入力し、結果パネルで **[15Five]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の 15Five](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>15Five への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて 15Five のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> 15Five では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[15Five シングル サインオンのチュートリアル](15five-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Azure AD で 15Five の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[15Five]** を選択します。

    ![アプリケーションの一覧の 15Five のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5.  [管理者資格情報] セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した **SCIM 2.0 ベース URL およびアクセス トークン**の値をそれぞれ入力します。 **[Test Connection]\(テスト接続\)** をクリックして、Azure AD から 15Five に接続できることを確認します。 接続できない場合は、その 15Five アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to 15Five]\(Azure Active Directory ユーザーを 15Five に同期する\)** を選択します。

    ![15Five のユーザー マッピング](media/15five-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から 15Five に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で 15Five のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![15Five のユーザー属性](media/15five-provisioning-tutorial/userattribute.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to 15Five]\(Azure Active Directory グループを 15Five に同期する\)** を選択します。

    ![15Five のグループ マッピング](media/15five-provisioning-tutorial/groupmapping.png)

11. **[属性マッピング]** セクションで、Azure AD から 15Five に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で 15Five のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![15Five のグループ属性](media/15five-provisioning-tutorial/groupattribute.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. 15Five に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、15Five にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって 15Five に対して実行されたすべてのアクションが記載されています。

    Azure AD プロビジョニング ログの読み方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。
    
## <a name="connector-limitations"></a>コネクタの制限事項

* 15Five では、ユーザーのハード削除はサポートされていません。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
