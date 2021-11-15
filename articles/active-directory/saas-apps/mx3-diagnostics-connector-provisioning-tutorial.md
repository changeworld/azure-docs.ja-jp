---
title: 'チュートリアル: MX3 Diagnostics Connector を構成し、Azure Active Directory を使って自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure AD から、MX3 Diagnostics Connector のユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に行う方法について説明します。
services: active-directory
documentationcenter: ''
author: twimmers
writer: Thwimmer
manager: beatrizd
ms.assetid: 6d54ea28-0208-45bc-8e29-c6cf9a912f00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2021
ms.author: Thwimmer
ms.openlocfilehash: 2021b7a7af7aee23e118de0b555e86cb7ee20272
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270094"
---
# <a name="tutorial-configure-mx3-diagnostics-connector-for-automatic-user-provisioning"></a>チュートリアル: MX3 Diagnostics Connector を構成して自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために MX3 Diagnostics Connector と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD から [MX3 Diagnostics Connector](https://www.mx3diagnostics.com/) に対して、Azure AD プロビジョニング サービスを使ったユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * MX3 Diagnostics Connector でユーザーを作成する。
> * MX3 Diagnostics Connector にアクセスする必要がなくなったユーザーを削除する。
> * Azure AD と MX3 Diagnostics Connector の間でユーザー属性の同期を維持する。
> * MX3 Diagnostics Connector でグループとグループのメンバーシップをプロビジョニングする。
> * MX3 Diagnostics Connector へのシングル サインオン。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* 組織機能が有効な MX3 アカウント。
* SSO が有効な MX3 ポータルのアカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と MX3 Diagnostics Connector の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-mx3-diagnostics-connector-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD を使ったプロビジョニングをサポートするように MX3 Diagnostics Connector を構成する

1. MX3 アカウントで組織機能が有効になっていない場合、この[ドキュメント](https://www.mx3diagnostics.com/files/files/MX3_PortalGuide_0321.pdf)の説明に従って組織機能を申請します。

1. MX3 アカウントでシングル サインオン機能が有効になっていない場合は、このドキュメントの説明に従って Azure AD SSO を設定します。

1. [MX3 ポータル](https://portal.mx3.app)にログインします。 [Settings]\(設定\) をクリックして SSO 設定ページに移動し、 **[Single sign-on]\(シングル サインオン\)** をクリックします。

    ![MX3 Diagnostics Connector の [Single sign-on]\(シングル サインオン\) 設定のスクリーンショット。](media/mx3-provisioning/sso-settings.png)


1. 下にスクロールして、トークンを表示します。 トークンをコピーして保存します。 これは、**ステップ 5** で必要になります。

    ![MX3 Diagnostics Connector の Azure AD のシークレット トークンのスクリーンショット。](media/mx3-provisioning/sso-settings-token.png)

## <a name="step-3-add-mx3-diagnostics-connector-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから MX3 Diagnostics Connector を追加する

Azure AD アプリケーション ギャラリーから MX3 Diagnostics Connector を追加して、MX3 Diagnostics Connector へのプロビジョニングの管理を開始します。 SSO のために MX3 Diagnostics Connector を以前に設定している場合は、その同じアプリケーションを使うことができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* MX3 Diagnostics Connector にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選ぶ必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-mx3-diagnostics-connector"></a>手順 5. MX3 Diagnostics Connector への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて MX3 Diagnostics Connector のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-mx3-diagnostics-connector-in-azure-ad"></a>Azure AD で MX3 Diagnostics Connector の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレードが表示されているスクリーンショット。](common/enterprise-applications.png)

1. アプリケーション一覧で **[MX3 Diagnostics Connector]** を選びます。

    ![アプリケーション一覧の [MX3 Diagnostics Connector] リンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブのとその場所のスクリーンショット。](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動] の選択を示すスクリーンショット。](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで MX3 Diagnostics Connector のテナント URL (https://scim.mx3.app) とシークレット トークン) を入力します。 **[テスト接続]** をクリックして、Azure AD から MX3 Diagnostics Connector に接続できることを確認します。 接続できない場合は、使用中の MX3 Diagnostics Connector アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークンと SCIM URL を入力するテキスト フィールドが表示されているスクリーンショット](common/provisioning-testconnection-tenanturltoken.png)

1. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用のメール アドレスを入力できるテキスト フィールドのスクリーンショット。](common/provisioning-notification-email.png)

1. <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> を選択します。

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to MX3 Diagnostics Connector]\(Azure Active Directory ユーザーを MX3 Diagnostics Connector に同期する\)** を選びます。

1. **[属性マッピング]** セクションで、Azure AD から MX3 Diagnostics Connector に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選ばれている属性は、更新処理で MX3 Diagnostics Connector のユーザー アカウントとの照合に使われます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に MX3 Diagnostics Connector API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

    |属性|Type|フィルター処理のサポート|
    |---|---|---|
    |userName|String|&check;
    |externalId|String|&check;
    |active|Boolean|
    |name.givenName|String|
    |name.familyName|String|

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to MX3 Diagnostics Connector]\(Azure Active Directory グループを MX3 Diagnostics Connector に同期する\)** を選びます。

1. **[属性マッピング]** セクションで、Azure AD から MX3 Diagnostics Connector に同期されるグループ属性を確認します。 **[照合]** プロパティとして選ばれている属性は、更新操作で MX3 Diagnostics Connector のグループとの照合に使われます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|
      |---|---|---|
      |displayName|String|&check;
      |externalId|String|
      |members|リファレンス|

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

1. MX3 Diagnostics Connector に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選び、MX3 Diagnostics Connector にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="more-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)