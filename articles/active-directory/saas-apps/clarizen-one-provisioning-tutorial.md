---
title: チュートリアル:Clarizen One を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から Clarizen One へのユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 133e12450f01e14f2204810f0d24cd28b5f948f8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795028"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>チュートリアル:Clarizen One を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Clarizen One と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用した [Clarizen One](https://www.clarizen.com/) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Clarizen One でユーザーを作成する
> * アクセスが不要になった Clarizen One のユーザーを削除する
> * Azure AD と Clarizen One の間でのユーザー属性の同期を維持する
> * Clarizen One にグループとグループ メンバーシップをプロビジョニングする
> * Clarizen One に[シングル サインオンする](https://docs.microsoft.com/azure/active-directory/saas-apps/clarizen-tutorial) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* Clarizen One のユーザー アカウントに、 **統合ユーザー** と **Lite 管理者** の[アクセス許可](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)がある。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Clarizen One の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように Clarizen One を構成する
1. Clarizen One 環境とデータセンターに応じて、次の 4 つのテナント URL のいずれかを選択します。
      * 米国運用データ センター: https://servicesapp2.clarizen.com/scim/v2
      * ヨーロッパ運用データ センター: https://serviceseu1.clarizen.com/scim/v2
      * 米国サンドボックス データ センター: https://servicesapp.clarizentb.com/scim/v2
      * ヨーロッパ サンドボックス データ センター: https://serviceseu.clarizentb.com/scim/v2

2. [API キー](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)を生成します。 この値を、Azure portal で Clarizen One アプリケーションの [プロビジョニング] タブ内の **[シークレット トークン]** フィールドに入力します。

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Clarizen One を追加する

Azure AD アプリケーション ギャラリーから Clarizen One を追加して、Clarizen One へのプロビジョニングの管理を開始します。 SSO のために Clarizen One を以前に設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Clarizen One にユーザーとグループを割り当てる場合は、 **既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>手順 5. Clarizen One への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Azure AD で Clarizen One の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションのリストで **[Clarizen One]** を選択します。

    ![アプリケーションのリストの [Clarizen One] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Clarizen One のテナント URL とシークレット トークンを入力します。 **[テスト接続]** をクリックして、Azure AD から Clarizen One に接続できることを確認します。 接続できない場合は、使用中の Clarizen One アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Azure Active Directory ユーザーを Clarizen One に同期する]** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Clarizen One に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Clarizen One のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Clarizen One API で確実にサポートされている必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |active|Boolean|
   |title|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |name.honorificPrefix|String|
   |name.honorificSuffix|String|
   |addresses[type eq "other"].formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].streetAddress|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |phoneNumbers[type eq "home"].value|String|
   |phoneNumbers[type eq "other"].value|String|
   |phoneNumbers[type eq "pager"].value|String|
   |externalId|String|
   |nickName|String|
   |locale|String|
   |roles[primary eq"True".type]|String|
   |roles[primary eq"True".value]|String|
   |timezone|String|
   |userType|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|

10. **[マッピング]** セクションの **[Azure Active Directory グループを Clarizen One に同期する]** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Clarizen One に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Clarizen One のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|リファレンス|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Clarizen One に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Clarizen One にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。  

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント
Clarizen One ギャラリー アプリにユーザーを割り当てるときは、 **ユーザー** ロールのみを選択します。 以下に表示されているロールは無効です。

* 管理者 (Admin)
* 電子メール レポート ユーザー
* 外部ユーザー
* 財務ユーザー
* ソーシャル ユーザー
* スーパー ユーザー
* 時間と経費ユーザー



## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
