---
title: 'チュートリアル: Azure Active Directory を使用して、自動ユーザー プロビジョニングを行うように Cisco Umbrella User Management を構成する | Microsoft Docs'
description: Azure AD から Cisco Umbrella User Management に対するユーザー アカウントの自動プロビジョニングおよびその解除の方法を学習します。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 1aa20f40-19ec-4213-9a3b-5eb2bcdd9bbd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: thwimmer
ms.openlocfilehash: 38b4c7cc04e5a9cc0d3dd35875ed94af804ca383
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122325085"
---
# <a name="tutorial-configure-cisco-umbrella-user-management-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニングを行うように Cisco Umbrella User Management を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Cisco Umbrella User Management と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD プロビジョニング サービスを使用した [Cisco Umbrella User Management](https://umbrella.cisco.com/) に対するユーザーとグループのプロビジョニングとその解除が、Azure AD によって自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Cisco Umbrella User Management でユーザーを作成する
> * ユーザーがアクセスを必要としなくなったときに Cisco Umbrella User Management からそのユーザーを削除する
> * Azure AD と Cisco Umbrella User Management の間でのユーザー属性の同期を維持する
> * Cisco Umbrella User Management でグループとグループ メンバーシップをプロビジョニングする

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [Cisco Umbrella のサブスクリプション](https://signup.umbrella.com)。
* 完全な管理者権限を持つ Cisco Umbrella のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Cisco Umbrella User Management の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-import-objectguid-attribute-via-azure-ad-connect-optional"></a>手順 2. Azure AD Connect を使用して ObjectGUID 属性をインポートする (省略可能)
以前にオンプレミス AD から Cisco Umbrella にユーザーとグループの ID をプロビジョニングしており、ここで Azure AD から同じユーザーとグループをプロビジョニングしたい場合は、以前にプロビジョニングした ID が Umbrella ポリシーで保持されるように ObjectGUID 属性を同期する必要があります。

> [!NOTE]
> ObjectGUID 属性をインポートする前に、オンプレミスの Umbrella AD コネクタをオフにする必要があります。
  
Microsoft Azure AD Connect を使用する場合、既定ではユーザーとグループの ObjectGUID 属性がオンプレミス AD から Azure AD に同期されません。 この属性を同期するには、省略可能な **[Directory Extension attribute sync]\(ディレクトリ拡張属性の同期\)** を有効にし、グループとユーザーの objectGUID 属性を選択します。  

   ![Azure Active Directory Connect ウィザードの [オプション機能] ページ](./media/cisco-umbrella-user-management-provisioning-tutorial/active-directory-connect-directory-extension-attribute-sync.png)

> [!NOTE]
> **[使用可能な属性]** での検索は、大文字と小文字が区別されます。

   ![[ディレクトリ拡張機能] 選択ページを示すスクリーンショット](./media/cisco-umbrella-user-management-provisioning-tutorial/active-directory-connect-directory-extensions.png)

## <a name="step-3-configure-cisco-umbrella-user-management-to-support-provisioning-with-azure-ad"></a>手順 3. Azure AD を使用したプロビジョニングをサポートするように Cisco Umbrella User Management を構成する

1. [Cisco Umbrella のダッシュボード](https://login.umbrella.com )にログインします。 **[デプロイ]**  >  **[コア ID]**  >  **[ユーザーとグループ]** の順に移動します。

2. インポート メカニズムが手動インポートに設定されている場合は、 **[Import from IdP]\(IdP からインポート\)** をクリックしてインポート メカニズムを切り替えます。 
  
3. Azure Active Directory カードを展開し、 **[API Keys page]\(API キー ページ\)** をクリックします。

   ![Api](./media/cisco-umbrella-user-management-provisioning-tutorial/keys.png)

4. [API キー] ページで Azure Active Directory カードを展開し、 **[トークンの生成]** をクリックします。

   ![Generate](./media/cisco-umbrella-user-management-provisioning-tutorial/token.png)

5. 生成されたトークンは 1 回だけ表示されます。 URL とトークンをコピーして保存します。 これらの値は、Azure portal の Cisco Umbrella User Management アプリケーションの [プロビジョニング] タブで、 **[テナント URL]** および **[シークレット トークン]** フィールドにそれぞれ入力することになります。  


## <a name="step-4-add-cisco-umbrella-user-management-from-the-azure-ad-application-gallery"></a>手順 4. Azure AD アプリケーション ギャラリーから Cisco Umbrella User Management を追加する

Azure AD アプリケーション ギャラリーから Cisco Umbrella User Management を追加して、Cisco Umbrella User Management へのプロビジョニングの管理を開始します。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-5-define-who-will-be-in-scope-for-provisioning"></a>手順 5. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Cisco Umbrella User Management にユーザーとグループを割り当てる際に、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 

## <a name="step-6-configure-automatic-user-provisioning-to-cisco-umbrella-user-management"></a>手順 6. Cisco Umbrella User Management への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Cisco Umbrella User Management のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-cisco-umbrella-user-management-in-azure-ad"></a>Azure AD で Cisco Umbrella User Management 用に自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション一覧で **[Cisco Umbrella User Management]** を選択します。

    ![アプリケーション一覧の Cisco Umbrella User Management リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Cisco Umbrella User Management のテナント URL とシークレット トークンを入力します。 **[接続テスト]** をクリックし、Azure AD から Cisco Umbrella User Management に確実に接続できるようにします。 接続できない場合は、ご自分の Cisco Umbrella User Management アカウントに確実に管理者権限がある状態になってから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to Cisco Umbrella User Management]\(Azure Active Directory ユーザーを Cisco Umbrella User Management に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Cisco Umbrella User Management に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Cisco Umbrella User Management のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更することにした場合は、その属性に基づいたユーザーのフィルター処理が Cisco Umbrella User Management API で確実にサポートされるようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |active|Boolean|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId|String|

> [!NOTE]
> Azure AD Connect を使用してユーザーの objectGUID 属性をインポートした場合は (手順 2 を参照)、objectGUID から urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId へのマッピングを追加します。

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Cisco Umbrella User Management]\(Azure Active Directory グループを Cisco Umbrella User Management に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Cisco Umbrella User Management に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Cisco Umbrella User Management のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |members|リファレンス|
      |urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:Group:nativeObjectId|String|

> [!NOTE]
> Azure AD Connect を使用してグループの objectGUID 属性をインポートした場合は (手順 2 を参照)、objectGUID から urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:Group:nativeObjectId へのマッピングを追加します。

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Cisco Umbrella User Management に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択することによって、Cisco Umbrella User Management にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-7-monitor-your-deployment"></a>手順 7. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="connector-limitations"></a>コネクタの制限事項
* Cisco Umbrella User Management では、最大 200 のグループのプロビジョニングがサポートされています。 スコープ内のこの数を超えるグループは、Cisco Umbrella にプロビジョニングされない場合があります。 

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)