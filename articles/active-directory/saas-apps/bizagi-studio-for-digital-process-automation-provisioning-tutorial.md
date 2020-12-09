---
title: チュートリアル:Azure Active Directory で自動ユーザー プロビジョニング用に Bizagi Studio for Digital Process Automation を構成する | Microsoft Docs
description: Azure AD から Bizagi Studio for Digital Process Automation へのユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: d177931429642436ceccae9c9051106ba5880ada
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180310"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Bizagi Studio for Digital Process Automation を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Bizagi Studio for Digital Process Automation と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[Bizagi Studio for Digital Process Automation](https://www.bizagi.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Bizagi Studio for Digital Process Automation でユーザーを作成する
> * ユーザーがアクセスを必要としなくなった場合に Bizagi Studio for Digital Process Automation でユーザーを削除する
> * Azure AD と Bizagi Studio for Digital Process Automation の間でユーザー属性の同期を維持する
> * Bizagi Studio for Digital Process Automation に[シングル サインオンする](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* Bizagi Studio for Digital Process Automation バージョン 11.2.4.2X 以降。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Bizagi Studio for Digital Process Automation の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-configure-bizagi-studio-for-digital-process-automation-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Bizagi Studio for Digital Process Automation を構成する


1. **管理者のアクセス許可** を持つユーザーとして、作業ポータルにログインします。

2. **[管理者] -> [セキュリティ] -> [Oauth 2 Applications]\(Oauth 2 アプリケーション\)** に移動します。

   ![settings](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. [追加] ボタンをクリックします。
4. [付与タイプ] で **[ベアラー トークン]** を選択します。 [Allowed scope]\(許可されるスコープ\) で **[API]** と **[USER SYNC]\(ユーザー同期\)** を選択し、[保存] をクリックします。

   ![api](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. **クライアント シークレット** をコピーして保存します。 この値を、Azure portal で Bizagi Studio for Digital Process Automation アプリケーションの [プロビジョニング] タブにある **[シークレット トークン]** フィールドに入力します。

   ![token](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="step-3-add-bizagi-studio-for-digital-process-automation-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Bizagi Studio for Digital Process Automation を追加する

Azure AD アプリケーション ギャラリーから Bizagi Studio for Digital Process Automation を追加して、Bizagi Studio for Digital Process Automation へのプロビジョニングの管理を開始します。 SSO のために Bizagi Studio for Digital Process Automation を以前に設定している場合は、その同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Bizagi Studio for Digital Process Automation にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-bizagi-studio-for-digital-process-automation"></a>手順 5. Bizagi Studio for Digital Process Automation への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Azure AD で Bizagi Studio for Digital Process Automation に対する自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Bizagi Studio for Digital Process Automation]** を選択します。

    ![アプリケーションの一覧内の Bizagi Studio for Digital Process Automation リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Bizagi Studio for Digital Process Automation の [テナントの URL] と [シークレット トークン] を入力します。 

      * **テナント URL:** この構造を使用して Bizagi SCIM エンドポイントを入力します:  <Your_Bizagi_Project>/scim/v2/  例: `https://my-company.bizagi.com/scim/v2/`

      * **シークレット トークン:** この値は、前に手順 2 で取得したものです。

      **[テスト接続]** をクリックして、Azure AD が Bizagi Studio for Digital Process Automation に接続できることを確認します。 接続できない場合は、ご使用の Bizagi Studio for Digital Process Automation アカウントに管理者アクセス許可があることを確認し、もう一度試します。

   ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Bizagi Studio for Digital Process Automation]\(Azure Active Directory ユーザーを Bizagi Studio for Digital Process Automation に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Bizagi Studio for Digital Process Automation に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Bizagi Studio for Digital Process Automation のユーザー アカウントとの照合に使用されます。 [一致するターゲット属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)を変更する場合は、その属性に基づいたユーザーのフィルター処理を Bizagi Studio for Digital Process Automation API がサポートしているか確認する必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|
   
10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Bizagi Studio for Digital Process Automation に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Bizagi Studio for Digital Process Automation にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。  

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
