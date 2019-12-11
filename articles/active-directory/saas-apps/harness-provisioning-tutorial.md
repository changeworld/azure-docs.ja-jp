---
title: チュートリアル:Harness を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Harness に対してユーザー アカウントの自動的なプロビジョニングとプロビジョニング解除を実行するように Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 34d05d6392e00757bf1e5562ffd8341ad04cc9dc
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807665"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>チュートリアル:Harness を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Harness に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Harness と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Harness テナント](https://harness.io/pricing/)
* 管理者アクセス許可がある Harness のユーザー アカウント

## <a name="assigning-users-to-harness"></a>Harness へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Harness へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを Harness に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>ユーザーを Harness に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを Harness に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Harness にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-harness-for-provisioning"></a>プロビジョニングのための Harness の設定

1. [Harness 管理コンソール](https://app.harness.io/#/login)にサインインします。 **[Continuous Security]\(継続的なセキュリティ\) > [アクセス管理]** の順に移動します。

    ![Harness 管理コンソール](media/harness-provisioning-tutorial/admin.png)

2.  **[API キー]** をクリックします。

    ![Harness での SCIM の追加](media/harness-provisioning-tutorial/apikeys.png)

3. **[新しいキーを追加]** をクリックします。 **[Add Api Key]\(API キーの追加\)** ダイアログ ボックスで、**名前**を指定し、 **[Permissions Inherited from]\(アクセス許可の継承元\)** ドロップダウン メニューからオプションを選択します。 **[送信]** ボタンをクリックします。

    ![Harness の [新しいキーを追加]](media/harness-provisioning-tutorial/addkey.png)

    ![Harness の [新しいキーを追加] ダイアログ](media/harness-provisioning-tutorial/title.png)

3.  **キー**をコピーします。 この値を、Azure portal で Harness アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。

    ![Harness でのトークンの作成](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>ギャラリーからの Harness の追加

Azure AD での自動ユーザー プロビジョニング用に Harness を構成する前に、Azure AD アプリケーション ギャラリーから Harness をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Harness を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Harness**」と入力し、結果パネルで **[Harness]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Harness](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-harness"></a>Harness への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Harness のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Harness では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Harness シングル サインオンのチュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> Harness の SCIM エンドポイントの詳細については、[こちら](https://docs.harness.io/article/smloyragsm-api-keys)を参照してください。

### <a name="to-configure-automatic-user-provisioning-for-harness-in-azure-ad"></a>Azure AD で Harness の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Harness]** を選択します。

    ![アプリケーションの一覧の Harness のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`」と入力します。 **[シークレット トークン]** に先ほど取得した**SCIM 認証トークン**の値を入力します。 **[テスト接続]** をクリックして、Azure AD から Harness への接続を確認します。 接続できない場合は、使用中の Harness アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Harness]\(Azure Active Directory ユーザーを Harness に同期する\)** を選択します。

    ![Harness のユーザー マッピング](media/harness-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から Harness に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Harness のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Harness ユーザーの属性](media/harness-provisioning-tutorial/userattributes.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Harness]\(Azure Active Directory グループを Harness に同期する\)** を選択します。

    ![Harness グループのマッピング](media/harness-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Harness に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Harness のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Harness のグループ属性](media/harness-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Harness に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Harness にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Harness に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
