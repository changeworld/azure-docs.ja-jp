---
title: 'チュートリアル: Tribeloo を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure AD から Tribeloo に対してユーザー アカウントを自動的にプロビジョニングしたり、プロビジョニングを解除したりする方法を学習します。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: d1063ef2-5d39-4480-a1e2-f58ebe7f98c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: thwimmer
ms.openlocfilehash: e01d8584825e1e9a74c82ee34dd3b9c6a2457cf2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045921"
---
# <a name="tutorial-configure-tribeloo-for-automatic-user-provisioning"></a>チュートリアル: Tribeloo を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Tribeloo と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用した [Tribeloo](https://www.tribeloo.com/) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Tribeloo でユーザーを作成する。
> * アクセスする必要がなくなった Tribeloo のユーザーを削除する。
> * Azure AD と Tribeloo の間でユーザー属性の同期を維持する。
> * Tribeloo に対して[シングル サインオン](tribeloo-tutorial.md)を行う (推奨)。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [Tribeloo](https://www.tribeloo.com/) テナント。
* 管理者アクセス許可を持つ Tribeloo のユーザー アカウント。


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と Tribeloo の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-tribeloo-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Tribeloo を構成する

[Tribeloo アプリ](https://app.tribeloo.com/)に移動し、管理者アクセス許可を持つユーザーでログインします。
1. サイドメニュー (1) を用いて **[Admin]\(管理\)** (2) に移動し、 **[User management]\(ユーザー管理\)** (3) を選択します。

    ![ユーザー管理へのアクセス](media/tribeloo-provisioning-tutorial/tribeloo-user-management.png)

1. **[User provisioning]\(ユーザー プロビジョニング\)** (1) タブをクリックします。このタブでは、Azure AD の統合を構成するために使用する必要がある Tribeloo 情報にアクセスできます。
   1. **SCIM ベース URL** (2)
   1. **SCIM ベアラー トークン** (3)
1. これらの値をクリップボードにコピーし、対応する Azure AD のフィールドに貼り付けます (手順 5 を参照)。 AD のフィールドにはそれぞれ **[テナントの URL]** と **[シークレット トークン]** という名前がついています。

    ![Tribeloo のプロビジョニング パラメーター](media/tribeloo-provisioning-tutorial/tribeloo-provisioning-parameters.png)

1. **[User Provisioning]\(ユーザー プロビジョニング\)** タブで **[Enable User provisioning]\(ユーザー プロビジョニングを有効にする\)** (1) ボタンをクリックすると、Tribeloo でのユーザー プロビジョニングが有効になります。

    ![Tribeloo のプロビジョニングを有効にする](media/tribeloo-provisioning-tutorial/tribeloo-enable-provisioning.png)

## <a name="step-3-add-tribeloo-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Tribeloo を追加する

Azure AD アプリケーション ギャラリーから Tribeloo を追加して、Tribeloo へのプロビジョニングの管理を開始します。 SSO のために Tribeloo を以前に設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Tribeloo にユーザーとグループを割り当てる場合は、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-tribeloo"></a>手順 5. Tribeloo への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Tribeloo のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-tribeloo-in-azure-ad"></a>Azure AD で Tribeloo の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Tribeloo]** を選択します。

    ![アプリケーション一覧の [Tribeloo] リンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで、Tribeloo の **[テナントの URL]** と **[シークレット トークン]** を入力します。 **[テスト接続]** をクリックして、Azure AD から Tribeloo に接続できるか確認します。 接続できない場合は、使用している Tribeloo アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

1. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Tribeloo]\(Azure Active Directory ユーザーを Tribeloo に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Tribeloo に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Tribeloo のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Tribeloo API でサポートされていることを確認する必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;
   |emails[type eq "work"].value|String|
   |active|Boolean|   
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |addresses[type eq "work"].formatted|String|

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

1. Tribeloo に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Tribeloo にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。 

## <a name="change-log"></a>変更履歴
* 2021 年 8 月 12 日 - コア ユーザー属性 **emails[type eq "work"].value** と **addresses[type eq "work"].formatted** のサポートを追加。

## <a name="more-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
