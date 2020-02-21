---
title: チュートリアル:Workplace by Facebook を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory と Workplace by Facebook の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7d8a7881c00427023e5f174461b3d8b24d83444
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121450"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>チュートリアル:Workplace by Facebook を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Workplace by Facebook と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、ユーザーとグループの [Workplace by Facebook](https://work.workplace.com/) へのプロビジョニングおよびプロビジョニング解除が Azure AD プロビジョニング サービスを使って自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Workplace by Facebook の新しいアプリケーションへの移行
Workplace by Facebook と既に統合されている場合は、今後の変更について以下のセクションを参照してください。 Workplace by Facebook を初めて設定する場合は、このセクションをスキップして、サポートされている機能に移動することができます。 

#### <a name="whats-changing"></a>変更点
* Azure AD 側での変更点: Workplace でユーザーをプロビジョニングするための承認方法は、従来は有効期間が長いシークレット トークンでした。 間もなく、承認方法が OAuth 承認付与に変更されます。 
* Workplace 側での変更点: 以前は、Azure AD アプリは Workplace by Facebook でのカスタム統合でした。 今後は、Workplace integration ディレクトリにサード パーティ アプリケーションとして Azure AD が表示されるようになります。 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>既存のカスタム統合を新しいアプリケーションに移行するには、どうすればよいですか?
有効なトークンとの既存の Workplace 統合がある場合、**操作は必要ありません**。 お客様は新しいアプリケーションに、毎週自動的に移行されます。 これは、バックグラウンドで完全に実行されます。 待つことができず、新しいアプリケーションに手動で移動したい場合は、ギャラリーから Workplace の新しいインスタンスを追加し、再度プロビジョニングを構成することができます。 Workplace の新しいインスタンスはすべて、自動的に新しいアプリケーション バージョンを使用します。 

 
Workplace 統合が検疫状態の場合は、自動的に移行できるようにするために、有効なトークンを再度提供する必要があります。 管理者の資格情報セクションは淡色表示されますが、次の ( **?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true**) を URL に追加すると、資格情報を再度保存できます。 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>アプリケーションが移行されたかどうかを確認するにはどうすればよいですか? 
アプリケーションが移行されると、今後の変更に関する承認セクションのバナーが削除され、シークレット トークン フィールドが青い承認ボタンに置き換えられます。 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>アプリケーションの管理者の資格情報セクションが淡色表示されていて、保存することができません。 なぜですか?
既存の Workplace のお客様については、管理者の資格情報セクションがロックダウンされています。 テナントが新しい Workplace アプリケーションに移行されたら、管理者の資格情報セクションを再度更新できるようになります。 待てない場合は、上記の URL を使用してアプリケーションを編集できます。 

 
#### <a name="when-will-these-changes-happen"></a>これらの変更はいつ行われるのですか?
Workplace の新しいインスタンスはすべて、既に新しい統合/承認方法を使用することになっています。 既存の統合は 2 月に段階的に移行されます。 すべてのテナントの移行は、この月の終わりまでに完了する予定です。 

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Workplace by Facebook 上にユーザーを作成する
> * アクセスが不要になった場合に Workplace by Facebook 上のユーザーを削除する
> * Azure AD と Workplace by Facebook 間でユーザー属性の同期を維持する
> * Workplace by Facebook への[シングル サインオン](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)
* Workplace by Facebook でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
2. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
3. [Azure AD と Workplace by Facebook 間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Workplace by Facebook を構成する

プロビジョニング サービスを構成して有効にする前に、Workplace by Facebook アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Workplace by Facebook アプリに割り当てることができます。

*   単一の Azure AD ユーザーを Workplace by Facebook に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Workplace by Facebook にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Workplace by Facebook を追加する

Azure AD アプリケーション ギャラリーから Workplace by Facebook を追加して、Workplace by Facebook へのプロビジョニングの管理を開始します。 SSO のために Workplace by Facebook を以前に設定している場合は、その同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)を参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)で説明されているスコープ フィルターを使用できます。 

* Workplace by Facebook にユーザーとグループを割り当てるときは、**既定のアクセス**以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を指定できます。 

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Workplace by Facebook]** を選択します。

    ![アプリケーションの一覧の Workplace by Facebook リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクション下にある **[承認する]** をクリックします。 Workplace by Facebook の承認ページにリダイレクトされます。 Workplace by Facebook のユーザー名を入力して、 **[続行]** ボタンをクリックします。 **[テスト接続]** をクリックして、Azure AD から Workplace by Facebook に接続できることを確認します。 接続が失敗した場合、お使いの Workplace by Facebook アカウントに Admin アクセス許可があることを確認して、再試行してください。

    ![プロビジョニング](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![承認](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクション下にある **[Synchronize Azure Active Directory Users to Workplace by Facebook]\(Azure Active Directory ユーザーを Workplace by Facebook に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Workplace by Facebook に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Workplace by Facebook のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に Workplace by Facebook API によってサポートされている必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |Attribute|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |active|Boolean|
   |title|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "other"].formatted|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Workplace by Facebook に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションにある **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** に目的の値を選択して、Workplace by Facebook にプロビジョニングするユーザー、グループ、またはその両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)を参照してください。

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント
*  ユーザーが作成に失敗し、コード "1789003" の監査ログイ ベントがあることが確認された場合は、ユーザーが未検証のドメインに由来していることを意味します。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
