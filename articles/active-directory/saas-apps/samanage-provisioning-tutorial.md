---
title: チュートリアル:Azure Active Directory を使用した自動ユーザー プロビジョニングに対応するように SolarWinds Service Desk (旧称 Samanage) を構成する | Microsoft Docs
description: Azure AD から SolarWinds Service Desk (旧称 Samanage) にユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: d68811c5e7b92698944d7b1b1736eef864d97d20
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802303"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングに対応するように SolarWinds Service Desk (旧称 Samanage) を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために SolarWinds Service Desk (旧称 Samanage) と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、ユーザーとグループの [SolarWinds Service Desk](https://www.samanage.com/pricing/) へのプロビジョニングおよびプロビジョニング解除が Azure AD プロビジョニング サービスを使って自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>新しい SolarWinds Service Desk アプリケーションに移行する

SolarWinds Service Desk との既存の統合がある場合は、今後の変更について次のセクションを参照してください。 SolarWinds Service Desk を初めて設定する場合は、このセクションをスキップして、「**サポートされる機能**」に進むことができます。

#### <a name="whats-changing"></a>変更点

* Azure AD 側での変更点: Samanage でユーザーをプロビジョニングするための承認方法は、従来は **Basic 認証** でした。間もなく、承認方法が **有効期間の長いシークレット トークン** に変更されます。


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>既存のカスタム統合を新しいアプリケーションに移行するには、どうすればよいですか?

有効な管理者資格情報を持つ既存の SolarWinds Service Desk 統合がある場合、**操作は必要はありません**。 新しいアプリケーションに顧客が自動的に移行されます。 この処理は、完全にバックグラウンドで実行されます。 既存の資格情報の有効期限が切れている場合、またはアプリケーションへのアクセスを再度承認する必要がある場合は、有効期間の長いシークレット トークンを生成する必要があります。 新しいトークンを生成するには、この記事の手順 2 を参照してください。


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>アプリケーションが移行されたかどうかを確認するにはどうすればよいですか? 

アプリケーションを移行すると、 **[管理者資格情報]** セクションで、 **[管理ユーザー名]** と **[管理パスワード]** フィールドが、1 つの **[シークレット トークン]** フィールドに置き換えられます。

## <a name="capabilities-supported"></a>サポートされる機能

> [!div class="checklist"]
> * SolarWinds Service Desk でユーザーを作成する
> * アクセスが不要になったユーザーを SolarWinds Service Desk から削除する
> * Azure AD と SolarWinds Service Desk の間でユーザー属性の同期を維持する
> * SolarWinds Service Desk でグループとグループ メンバーシップをプロビジョニングする
> * SolarWinds Service Desk への[シングル サインオン](./samanage-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* Professional パッケージを使用する [SolarWinds Service Desk テナント](https://www.samanage.com/pricing/)。
* 管理者のアクセス許可を持つ SolarWinds Service Desk のユーザー アカウント。

> [!Note]
> ロールのインポートを行う場合、Azure Active Directory でロールを手動で編集することはできません。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と SolarWinds Service Desk の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように SolarWinds Service Desk を構成する

認証用のシークレット トークンを生成するには、[API 統合のトークン認証に関するチュートリアル](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)を参照してください。

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから SolarWinds Service Desk を追加する

Azure AD アプリケーション ギャラリーから SolarWinds Service Desk を追加し、SolarWinds Service Desk へのプロビジョニングの管理を開始します。 以前に SSO に対応するように SolarWinds Service Desk を設定した場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* ユーザーとグループを SolarWinds Service Desk に割り当てるときは、 **[既定のアクセス]** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>手順 5. SolarWinds Service Desk への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Azure AD で SolarWinds Service Desk の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **SolarWinds Service Desk** を選択します。

3. **[プロビジョニング]** タブを選択します。

    ![選択されている [プロビジョニング] タブを示すスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング モード] が [自動] に設定されていることを示すスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://api.samanage.com`」と入力します。  前に取得したシークレット トークンの値を、 **[シークレット トークン]** に入力します。 **[接続テスト]** を選択して、Azure AD から SolarWinds Service Desk に接続できることを確認します。 接続できない場合は、使用中の SolarWinds Service Desk アカウントで管理者アクセス許可を確保してから、もう一度試します。

    ![[テスト接続] ボタンが選択されていることを示すスクリーンショット。](./media/samanage-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to SolarWinds Service Desk]\(Azure Active Directory ユーザーを SolarWinds Service Desk に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から SolarWinds Service Desk に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で SolarWinds Service Desk のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に SolarWinds Service Desk API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

      ![Samanage のユーザー マッピング](./media/samanage-provisioning-tutorial/user-attributes.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to SolarWinds Service Desk]\(Azure Active Directory グループを SolarWinds Service Desk に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から SolarWinds Service Desk に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で SolarWinds Service Desk のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      ![Samange のグループ マッピング](./media/samanage-provisioning-tutorial/group-attributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. SolarWinds Service Desk に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、SolarWinds Service Desk にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="connector-limitations"></a>コネクタの制限事項

**[すべてのユーザーとグループを同期する]** オプションを選択し、SolarWinds Service Desk の **ロール** 属性に値を構成した場合、 **[null の場合の既定値 (オプション)]** ボックスの値は次の形式で表現する必要があります。

- {"displayName":"role"}。この role は使用する既定値です。

## <a name="change-log"></a>ログの変更

* 2020 年 9 月 14 日 - https://github.com/ravitmorales に従い、2 つの SaaS チュートリアルに記載されている会社名を Samanage から SolarWinds Service Desk (旧称 Samanage) に変更しました。
* 2020 年 4 月 22 日 - 基本認証から有効期間の長いシークレット トークンへ承認方法を更新しました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
