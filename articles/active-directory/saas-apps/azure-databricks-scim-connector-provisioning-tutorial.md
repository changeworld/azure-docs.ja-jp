---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニング用に Azure Databricks SCIM Connector を構成する | Microsoft Docs
description: Azure AD から Azure Databricks SCIM Connector に対してユーザー アカウントを自動的にプロビジョニング、またはプロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: de60b4ea1b09998e84bab4d204e3c8c3bc8779a4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050361"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Azure Databricks SCIM Connector を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Azure Databricks SCIM Connector と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[Azure Databricks SCIM Connector](https://databricks.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Azure Databricks SCIM Connector でユーザーを作成する
> * アクセスが不要になった Azure Databricks SCIM Connector のユーザーを削除する
> * Azure AD と Azure Databricks SCIM Connector の間でユーザー属性の同期を維持する
> * Azure Databricks SCIM Connector でグループとグループ メンバーシップをプロビジョニングする

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* 管理者アクセス許可を持つ Azure Databricks アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Azure Databricks SCIM Connector の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Azure Databricks SCIM Connector を構成する

1. Azure Databricks SCIM プロビジョニングを設定するには、これを Azure Active Directory テナントのリソースとして追加し、以下の設定を使用して構成します。

    ![Azure Databricks のセットアップ](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Azure Databricks で個人用アクセス トークンを生成するには、[こちら](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management)を参照してください。

3. **トークン**をコピーします。 この値を、Azure portal で Azure Databricks SCIM Connector アプリケーションの [プロビジョニング] タブにある [シークレット トークン] フィールドに入力します。

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Azure Databricks SCIM Connector を追加する

Azure AD アプリケーション ギャラリーから Azure Databricks SCIM Connector を追加して、Azure Databricks SCIM Connector へのプロビジョニングの管理を開始します。 SSO のために Azure Databricks SCIM Connector を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Azure Databricks SCIM Connector にユーザーとグループを割り当てるときは、**既定のアクセス**以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>手順 5. Azure Databricks SCIM Connector に自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!NOTE]
> Azure Databricks の SCIM エンドポイントの詳細については、[こちら](https://docs.databricks.com/dev-tools/api/latest/scim.html
)を参照してください。

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Azure AD で Azure Databricks SCIM Connector に対して自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Azure Databricks SCIM Connector]** を選択します。

    ![アプリケーション一覧での Azure Databricks SCIM Connector のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Azure Databricks SCIM Connector 管理者の資格情報とユーザー名を入力します。 **[テスト接続]** をクリックして、Azure AD から Azure Databricks SCIM Connector に接続できることを確認します。 接続できない場合は、使用中の Azure Databricks SCIM Connector アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![プロビジョニング](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Azure Databricks SCIM Connector] (Azure Active Directory ユーザーを Azure Databricks SCIM Connector に同期する)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Azure Databricks SCIM Connector に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Azure Databricks SCIM Connector のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に Azure Databricks SCIM Connector API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |Attribute|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |active|Boolean|

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Azure Databricks SCIM Connector] (Azure Active Directory グループを Azure Databricks SCIM Connector に同期する)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Azure Databricks SCIM Connector に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Azure Databricks SCIM Connector のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

     |Attribute|Type|
     |---|---|
     |displayName|String|
     |members|リファレンス|

11. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Azure Databricks SCIM Connector] (Azure Active Directory グループを Azure Databricks SCIM Connector に同期する)** を選択します。

12. Azure Databricks SCIM Connector に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Azure Databricks SCIM Connector にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。  

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント
* Databricks は、SCIM を介して送信される値の大文字小文字に関係なく、ユーザー名の値を常に小文字に変換します。
* 現在、Azure Databricks のユーザーへの SCIM API に対する GET 要求は、大文字と小文字が区別されます。そのため、USER@contoso.com でクエリを実行した場合、user@contoso.com として保存されているため 0 件の結果が返されます。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
