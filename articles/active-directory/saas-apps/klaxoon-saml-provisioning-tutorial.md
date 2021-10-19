---
title: 'チュートリアル: Azure Active Directory を使用して自動ユーザー プロビジョニング用に Klaxoon SAML を構成する | Microsoft Docs'
description: Azure AD から Klaxoon SAML に対してユーザー アカウントを自動的にプロビジョニング、またはプロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: twimmer
writer: twimmer
manager: beatrizd
ms.assetid: 5aaacb86-4fb0-49f3-9f7d-e9ea94829b2b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2021
ms.author: thwimmer
ms.openlocfilehash: c032dcd6cfe41e2893b53029254a96f54c9fc595
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620895"
---
# <a name="tutorial-configure-klaxoon-saml-for-automatic-user-provisioning"></a>自動ユーザー プロビジョニング用に Klaxoon SAML を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Klaxoon SAML と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用した [Klaxoon SAML](https://www.klaxoon.com/) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Klaxoon でユーザーを作成する。
> * アクセスが不要になった場合に Klaxoon でユーザーを無効にする。
> * Azure AD と Klaxoon の間でユーザー属性の同期を維持する。
> * Azure AD グループに基づいて Klaxoon のユーザーにライセンスを提供する。
> * SAML を使用して Klaxoon に[シングル サインオンする](klaxoon-saml-tutorial.md) (推奨)。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* 既存の [Klaxoon コントラクト](https://klaxoon.com/enterprise)。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と Klaxoon SAML の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-klaxoon-saml-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように Klaxoon SAML を構成する

* [Klaxoon](https://klaxoon.com/) にお問い合わせいただき、一意の **テナント URL** と **シークレット トークン** を受け取ります。

## <a name="step-3-add-klaxoon-saml-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Klaxoon SAML を追加する

Azure AD アプリケーション ギャラリーから Klaxoon SAML を追加して、Klaxoon へのプロビジョニングの管理を開始します。 以前に、SSO 用に Klaxoon SAML を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。

* Klaxoon SAML にユーザーとグループを割り当てる場合は、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-klaxoon"></a>手順 5. Klaxoon への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、Klaxoon SAML でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-klaxoon-saml-in-azure-ad"></a>Azure AD で Klaxoon SAML の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Klaxoon SAML]** を選択します。

    ![アプリケーションの一覧の Klaxoon SAML のリンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで、自分の Klaxoon テナントの URL と Klaxoon から提供されたシークレット トークンを入力します。 **[テスト接続]** をクリックして、Azure AD から Klaxoon への接続を確保します。 接続できない場合は、Klaxoon にお問い合わせいただき、アカウントのセットアップを確認してください。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

1. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Klaxoon SAML]\(Azure Active Directory のユーザーを Klaxoon SAML に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Klaxoon に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Klaxoon のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Klaxoon API で確実にサポートされている必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|Klaxoon で必要|
   |---|---|---|---|
   |userName|String|&check;|&check;|
   |emails[type eq "work"].value|String|&check;|&check;|
   |externalId|String||&check;|
   |name.givenName|String||&check;|
   |name.familyName|String||&check;|
   |active|Boolean||&check;|

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Klaxoon SAML]\(Azure Active Directory のグループを Klaxoon SAML に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Klaxoon に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Klaxoon のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|Klaxoon で必要|
      |---|---|---|---|
      |displayName|String|&check;|&check;
      |externalId|String||&check;
      |members|リファレンス||
      |urn:ietf:params:scim:schemas:extension:klaxoon:2.0:Group:license|String||


1. **urn:ietf:params:scim:schemas:extension:klaxoon:2.0:Group:license** 属性を定義して、グループにリンクされているユーザーに Klaxoon PRO のライセンスを提供します。

      |値|Klaxoon PRO のライセンスを持つグループ|
      |---|---|
      |true|&check;|
      |false|No|
      |指定されていない (既定値)|&check;|

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

1. Klaxoon SAML に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択することによって、Klaxoon SAML にプロビジョニングするユーザーまたはグループ、あるいはその両方を定義します。

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