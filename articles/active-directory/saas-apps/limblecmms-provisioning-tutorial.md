---
title: 'チュートリアル: Azure Active Directory を使用した自動ユーザー プロビジョニングのために LimbleCMMS を構成する | Microsoft Docs'
description: Azure AD から LimbleCMMS へのユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 5e0d5369-7230-4a16-bc3f-9eac2bc80a8c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: thwimmer
ms.openlocfilehash: 5ad0ef1911a76b0bc99966e76f66a8b1cd8a0215
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195643"
---
# <a name="tutorial-configure-limblecmms-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニングのために LimbleCMMS を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために LimbleCMMS と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用した [LimbleCMMS](https://limblecmms.com/) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * LimbleCMMS でユーザーを作成する。
> * アクセスが不要になった LimbleCMMS のユーザーを削除する。
> * LimbleCMMS にグループを作成する。
> * LimbleCMMS のグループでユーザーを追加/削除する
> * LimbleCMMS のグループを削除する
> * Azure AD と LimbleCMMS の間でユーザー属性の同期を維持する。
> * LimbleCMMS でグループとグループ メンバーシップをプロビジョニングする。
> * LimbleCMMS への[シングル サインオン](../manage-apps/add-application-portal-setup-oidc-sso.md) (推奨)。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* Business Plus 以上のライセンスを持つ [LimbleCMMS](https://limblecmms.com/signup/?plan=business-yearly) テナント。
* Super Admin アクセス許可がある LimbleCMMS のユーザー アカウント。
* LimbleCMMS テナントでシングル サインオンが有効になっていること (カスタマー サクセス マネージャーにお問い合わせください)。
* LimbleCMMS へのプロビジョニングを計画しているグループを少なくとも 1 つ (LimbleCMMS のアクセス許可はグループに基づいています。グループをプロビジョニングしない場合、プロビジョニングされるユーザーにはアクセス許可が関連付けられません)。 


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と LimbleCMMS の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-limblecmms-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように LimbleCMMS を構成する

1. **Super Admin** として LimbleCMMS にログインします。
1. **[詳細設定] > [Manage SSO]\(SSO の管理\)** の順に移動します。
      ![SSO を管理する](media/limblecmms-provisioning-tutorial/limble-manage-sso.png)
1. SSO プロバイダーとして **[Azure Active Directory]** を選択します。
1. シングル サインオンをサポートするように [OIDC をセットアップ](https://help.limblecmms.com/en/articles/4446986-active-directory-oidc-sso-setup-guide)します
1. **[Generate SCIM Token]\(SCIM トークンの生成\)** ボタンをクリックして SCIM トークンを取得し、これを後の手順のために保存します。
1. **[Enable SSO]\(SSO を有効にする\)** をクリックします。

## <a name="step-3-add-limblecmms-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから LimbleCMMS を追加する

Azure AD アプリケーション ギャラリーから LimbleCMMS を追加して、LimbleCMMS へのプロビジョニングの管理を開始します。 SSO のために LimbleCMMS を以前に設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* LimbleCMMS にユーザーとグループを割り当てる場合は、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-limblecmms"></a>手順 5. LimbleCMMS への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて LimbleCMMS のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-limblecmms-in-azure-ad"></a>Azure AD で LimbleCMMS の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[LimbleCMMS]** を選択します。

    ![アプリケーションの一覧の LimbleCMMS のリンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで、LimbleCMMS の **[テナントの URL]** と **[シークレット トークン]** を入力します。 **[テスト接続]** をクリックして、Azure AD から LimbleCMMS に接続できることを確認します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

1. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to LimbleCMMS]\(Azure Active Directory ユーザーを LimbleCMMS に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から LimbleCMMS に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で LimbleCMMS のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が LimbleCMMS SCIM API でサポートされていることを確認する必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|


1. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to LimbleCMMS]\(Azure Active Directory グループを LimbleCMMS に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から LimbleCMMS に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で LimbleCMMS のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|
      |---|---|---|
      |displayName|String|&check;
      |members|リファレンス|
      |externalId|String|      

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

1. LimbleCMMS に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、LimbleCMMS にプロビジョニングするユーザーやグループを定義します。

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