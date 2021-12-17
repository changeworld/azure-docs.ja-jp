---
title: 'チュートリアル: BrowserStack Single Sign-on を構成して Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure AD から BrowserStack Single Sign-on へのユーザー アカウントのプロビジョニングとその解除を自動的に行う方法について学習します。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 39999abc-e4a2-4058-81e0-bf88182f8864
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: thwimmer
ms.openlocfilehash: 5e3d33775fbf342f328cb4260607a540930ff22d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322314"
---
# <a name="tutorial-configure-browserstack-single-sign-on-for-automatic-user-provisioning"></a>チュートリアル: BrowserStack Single Sign-on を構成して自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために BrowserStack Single Sign-on と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[BrowserStack Single Sign-on](https://www.browserstack.com) に対するユーザーのプロビジョニングとその解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * BrowserStack Single Sign-on でユーザーを作成する
> * アクセスが不要になった場合に BrowserStack Single Sign-on のユーザーを削除する
> * Azure AD と BrowserStack Single Sign-on の間でユーザー属性の同期を維持する
> * BrowserStack Single Sign-on に[シングル サインオンする](./browserstack-single-sign-on-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* **所有者** アクセス許可を持つ BrowserStack 内のユーザー アカウント。
* BrowserStack を含む [Enterprise プラン](https://www.browserstack.com/pricing)。 
* BrowserStack との [Single Sign-on](https://www.browserstack.com/docs/enterprise/single-sign-on/azure-ad) の統合 (必須)。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と BrowserStack Single Sign-on の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-browserstack-single-sign-on-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように BrowserStack Single Sign-on を構成する

1. **所有者** アクセス許可を持つユーザーとして [BrowserStack](https://www.browserstack.com/users/sign_in) にログインします。

2. **[アカウント]**  ->  **[Settings & Permissions]\(設定とアクセス許可\)** の順に移動します。 **[セキュリティ]** タブをクリックします。

3. **[Auto User Provisioning]\(自動ユーザー プロビジョニング\)** で、 **[構成]** をクリックします。

    ![Settings](media/browserstack-single-sign-on-provisioning-tutorial/configure.png)

4. Azure AD で制御するユーザー属性を選択し、 **[確認]** をクリックします。

    ![ユーザー](media/browserstack-single-sign-on-provisioning-tutorial/attributes.png)

5. **[テナントの URL]** と **[シークレット トークン]** をコピーします。 これらの値は、Azure portal 内の BrowserStack Single Sign-on アプリケーションの [プロビジョニング] タブにある [テナント URL] および [シークレット トークン] フィールドに入力されます。 **[Done]** をクリックします。

    ![承認](media/browserstack-single-sign-on-provisioning-tutorial/credential.png)

6. プロビジョニングの構成が BrowserStack に保存されました。 **Azure AD でのプロビジョニングの設定** が完了したら、BrowserStack でのユーザー プロビジョニングを **有効** にします。これにより、BrowserStack [アカウント](https://www.browserstack.com/accounts/manage-users)からの新しいユーザーの招待がブロックされるのを防ぐことができます。 

    ![Account](media/browserstack-single-sign-on-provisioning-tutorial/enable.png)
    
## <a name="step-3-add-browserstack-single-sign-on-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから BrowserStack Single Sign-on を追加する

Azure AD アプリケーション ギャラリーから BrowserStack Single Sign-on を追加して、BrowserStack Single Sign-on へのプロビジョニングの管理を開始します。 以前に SSO を行うために BrowserStack Single Sign-on を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーをアプリケーションに割り当てることができます。 ユーザーの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* BrowserStack Single Sign-on にユーザーを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーでテストします。 プロビジョニングのスコープが割り当て済みユーザーに設定される場合、これを制御するには、1 人または 2 人のユーザーをアプリに割り当てます。 スコープがすべてのユーザーに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-browserstack-single-sign-on"></a>手順 5. BrowserStack Single Sign-on への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーの割り当てに基づいて、アプリでユーザーが作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-browserstack-single-sign-on-in-azure-ad"></a>Azure AD で BrowserStack Single Sign-on の自動ユーザー プロビジョニングを構成するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[BrowserStack Single Sign-on]** を選択します。

    ![アプリケーションの一覧の BrowserStack Single Sign-on リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[Admin Credentials]\(管理者資格情報\)** セクションで、BrowserStack Single Sign-on の [テナント URL] と [シークレット トークン] を入力します。 **[テスト接続]** をクリックし、Azure AD から BrowserStack Single Sign-on に接続できるようにします。 接続できない場合は、BrowserStack Single Sign-on アカウントに管理者アクセス許可がある状態にしてからもう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to BrowserStack Single Sign-on]\(Azure Active Directory ユーザーを BrowserStack Single Sign-on に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から BrowserStack Single Sign-on に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で BrowserStack Single Sign-on でのユーザー アカウントの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更することにした場合は、その属性に基づいたユーザーのフィルター処理が BrowserStack Single Sign-on API で確実にサポートされるようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|--|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_role|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_team|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_product|String|


10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. BrowserStack Single Sign-on に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、BrowserStack Single Sign-on にプロビジョニングしたいユーザーを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

- [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
- [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
- プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="connector-limitations"></a>コネクタの制限事項

* BrowserStack Single Sign-on ではグループ プロビジョニングはサポートされていません。
* BrowserStack Single Sign-on では、**emails[type eq "work"].value** と **userName** のソース値が同じである必要があります。

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

* トラブルシューティングのヒントについては、[こちら](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#troubleshooting)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [BrowserStack Single Sign-on での属性マッピングの構成](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad)
* [BrowserStack で自動ユーザー プロビジョニングを設定して有効にする](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#setup-and-enable-auto-user-provisioning)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)