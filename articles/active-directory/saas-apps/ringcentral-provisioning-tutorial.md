---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニング用に RingCentral を構成する | Microsoft Docs
description: RingCentral に対してユーザー アカウントが自動的にプロビジョニングおよびプロビジョニング解除されるように、Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 976ac071-fa7e-4f31-aed1-d174942860d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: 6c6936b485526c07b3486874d6bdaacaf07ae8e5
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060831"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に RingCentral を構成する

このチュートリアルの目的は、ユーザーまたはグループの RingCentral へのプロビジョニングとプロビジョニング解除が自動的に行われるよう Azure AD を構成するために、RingCentral と Azure Active Directory (Azure AD) で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [RingCentral テナント](https://www.ringcentral.com/office/plansandpricing.html)
* 管理者アクセス許可がある RingCentral のユーザー アカウント。

## <a name="assigning-users-to-ringcentral"></a>RingCentral へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、RingCentral へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を RingCentral に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ringcentral"></a>RingCentral へのユーザーの割り当てに関する重要なヒント

* 1 人の Azure AD ユーザーを RingCentral に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* RingCentral にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-ringcentral-for-provisioning"></a>プロビジョニング用に RingCentral を設定する

1. [RingCentral 管理コンソール](https://login.ringcentral.com/sw.html)にサインインします。 **[Tools]\(ツール\) > [Directory Integration]\(ディレクトリの統合\)** に移動します。

    ![RingCentral 管理コンソール](media/ringcentral-provisioning-tutorial/admin.png)

2.  **[Select Directory Provider]\(ディレクトリ プロバイダーの選択\)** で **[SCIM]** を選択します。 (将来的には、Azure Active Directory がオプションに追加されます)。 **[Enable SCIM service]\(SCIM サービスを有効にする\)** をクリックします。

    ![RingCentral での SCIM の追加](media/ringcentral-provisioning-tutorial/scim.png)

3.  **SCIM 認証トークン**については、matthew.hunt@ringcentral.com で RingCentral のサポート チームに問い合わせてください。 この値を、Azure portal で RingCentral アプリケーションの [プロビジョニング] タブの [シークレット トークン] フィールドに入力します。

> [!NOTE]
> ユーザーにライセンスを割り当てる方法については、[こちら](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language)のビデオ リンクをご覧ください。

## <a name="add-ringcentral-from-the-gallery"></a>ギャラリーから RingCentral を追加する

Azure AD での自動ユーザー プロビジョニング用に RingCentral を構成する前に、Azure AD アプリケーション ギャラリーから RingCentral をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから RingCentral を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**RingCentral**」と入力し、結果パネルで **RingCentral** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの RingCentral](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ringcentral"></a>RingCentral への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、RingCentral でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> RingCentral では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[RingCentral シングル サインオンのチュートリアル](ringcentral-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> RingCentral の SCIM エンドポイントについて詳しくは、[RingCentral API のリファレンス](https://developers.ringcentral.com/api-reference)を参照してください。

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Azure AD で RingCentral の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[RingCentral]** を選択します。

    ![アプリケーションの一覧の RingCentral のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://platform.ringcentral.com/scim/v2`」と入力します。 **[シークレット トークン]** に先ほど取得した**SCIM 認証トークン**の値を入力します。 **[テスト接続]** をクリックして、Azure AD から RingCentral への接続を確認します。 接続できない場合は、使用中の RingCentral アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to RingCentral]\(Azure Active Directory ユーザーを RingCentral に同期する\)** を選択します。

    ![RingCentral のユーザー マッピング](media/ringcentral-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から RingCentral に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で RingCentral のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![RingCentral のユーザー属性](media/ringcentral-provisioning-tutorial/userattributes.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. RingCentral に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、RingCentral にプロビジョニングするユーザー、グループ、またはその両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって RingCentral に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

