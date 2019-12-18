---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニング用に IDEO を構成する | Microsoft Docs
description: IDEO に対してユーザー アカウントが自動的にプロビジョニングおよびプロビジョニング解除されるように、Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: d6bc3170162710e86359b374d1ef707bba0ce268
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152220"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に IDEO を構成する

このチュートリアルの目的は、ユーザーまたはグループの IDEO へのプロビジョニングとプロビジョニング解除が自動的に行われるよう Azure AD を構成するために、IDEO と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [IDEO テナント](https://www.shape.space/product/pricing)
* 管理者アクセス許可を持つ IDEO 上のユーザー アカウント。

## <a name="assign-users-to-ideo"></a>ユーザーを IDEO に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、IDEO へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを IDEO に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>IDEO へのユーザーの割り当てに関する重要なヒント

* 1 人の Azure AD ユーザーを IDEO に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* IDEO にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-ideo-for-provisioning"></a>プロビジョニング用に IDEO を設定する

Azure AD での自動ユーザー プロビジョニング用に IDEO を構成する前に、IDEO からプロビジョニング情報を取得する必要があります。

1. **シークレット トークン**については、productsupport@ideo.com で IDEO サポート チームに問い合わせてください。 この値を、Azure portal で IDEO アプリケーションの [プロビジョニング] タブの **[シークレット トークン]** フィールドに入力します。 

## <a name="add-ideo-from-the-gallery"></a>ギャラリーから IDEO を追加する

Azure AD を使用した自動ユーザー プロビジョニング用に IDEO を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に IDEO を追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**IDEO**」と入力し、結果パネルで **IDEO** を選択します。 

    ![結果一覧の IDEO](common/search-new-app.png)

5. **[IDEO にサインアップ]** ボタンを選択します。IDEO のログイン ページにリダイレクトされます。 

    ![IDEO での OIDC の追加](media/ideo-provisioning-tutorial/signup.png)

6. IDEO は OpenIDConnect アプリであるため、Microsoft の職場アカウントを使用して IDEO にログインすることを選択します。

    ![IDEO での OIDC のログイン](media/ideo-provisioning-tutorial/login.png)

7. 認証に成功した後、同意ページの同意プロンプトを受け入れます。 その後、ご使用のテナントにアプリケーションが自動的に追加され、IDEO アカウントにリダイレクトされます。

    ![IDEO での OIDC の同意](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>IDEO への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて IDEO のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Azure AD で IDEO の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[IDEO]** を選択します。

    ![アプリケーション一覧の [IDEO] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://profile.ideo.com/api/scim/v2`」と入力します。 IDEO のサポート チームから取得した値を、 **[シークレット トークン]** に入力します。 **[テスト接続]** をクリックして、Azure AD から IDEO への接続を確保します。 接続できない場合は、使用中の IDEO アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to IDEO]\(Azure Active Directory ユーザーを IDEO に同期する\)** を選択します。

    ![IDEO のユーザー マッピング](media/ideo-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から IDEO に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で IDEO のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![IDEO のユーザー属性](media/ideo-provisioning-tutorial/userattributes.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. IDEO に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](media/ideo-provisioning-tutorial/groupmappings.png)

12. **[設定]** セクションの **[Scope]\(スコープ\)** で目的の値を選択して、IDEO にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](media/ideo-provisioning-tutorial/groupattributes.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって IDEO に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)


