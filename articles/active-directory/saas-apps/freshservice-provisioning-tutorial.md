---
title: 'チュートリアル: Freshservice Provisioning を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure AD から Freshservice Provisioning へのユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に行う方法について説明します。
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e03ec65a-25ef-4c91-a364-36b2f007443c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: thwimmer
ms.openlocfilehash: aa4242b65157776ef48f4095d64366344c0cbce6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734227"
---
# <a name="tutorial-configure-freshservice-provisioning-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニングのために Freshservice Provisioning を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Freshservice Provisioning と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、ユーザーの [Freshservice Provisioning](https://effy.co.in/) へのプロビジョニングおよびプロビジョニング解除が Azure AD プロビジョニング サービスを使って自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Freshservice Provisioning でユーザーを作成する
> * アクセスが不要になった場合に Freshservice Provisioning のユーザーを削除する
> * Azure AD と Freshservice Provisioning の間でユーザー属性の同期を維持する

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../users-groups-roles/directory-assign-admin-roles.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* 組織の管理者アクセス許可を持つ [Freshservice アカウント](https://www.freshservice.com)。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../manage-apps/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Freshservice Provisioning の間でマップする](../manage-apps/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-freshservice-provisioning-to-support-provisioning-with-azure-ad"></a>手順 2. Freshservice Provisioning を構成し、Azure AD を使用したプロビジョニングをサポートする

1. Freshservice アカウントで、 **[Freshservice Admin] \(Freshservice 管理者\)**  >  **[アプリ]**  >  **[アプリの取得]** に移動し、マーケットプレースから **Azure Provisioning (SCIM)** アプリをインストールします。
2. 構成画面で、**Freshservice ドメイン** (例: `acme.freshservice.com`) と **組織管理者 API キー** を指定します。
3. **[続行]** をクリックします。
4. **ベアラー トークン** をハイライトしてコピーします。 この値を、Azure portal で Freshservice Provisioning アプリケーションの [プロビジョニング] タブ内の **[シークレット トークン]** フィールドに入力します。
5. **[インストール]** をクリックしてインストールを完了します。
6. **テナント URL** は `https://scim.freshservice.com/scim/v2` です。 この値を、Azure portal で Freshservice Provisioning アプリケーションの [プロビジョニング] タブ内の **[テナント URL]** フィールドに入力します。

## <a name="step-3-add-freshservice-provisioning-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Freshservice Provisioning を追加する

Azure AD アプリケーション ギャラリーから Freshservice Provisioning を追加して、Freshservice Provisioning へのプロビジョニングの管理を開始します。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-gallery-app.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーをアプリケーションに割り当てることができます。 ユーザーの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Freshservice Provisioning にユーザーを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーでテストします。 プロビジョニングのスコープが割り当て済みユーザーに設定される場合、これを制御するには、1 人または 2 人のユーザーをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-freshservice-provisioning"></a>手順 5. Freshservice Provisioning への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーの割り当てに基づいて、Freshservice Provisioning でユーザーが作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-freshservice-provisioning-in-azure-ad"></a>Azure AD で Freshservice Provisioning のための自動ユーザー プロビジョニングを構成するには、以下を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Freshservice Provisioning]** を選択します。

    ![アプリケーションの一覧の Freshservice Provisioning リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Freshservice Provisioning のテナント URL とシークレット トークンを入力します。 **[接続テスト]** をクリックして、Azure AD から Freshservice Provisioning に確実に接続できるようにします。 接続できない場合は、使用中の Freshservice Provisioning アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Freshservice Provisioning] \(Azure Active Directory ユーザーを Freshservice Provisioning に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Freshservice Provisioning に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Freshservice Provisioning のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../manage-apps/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に Freshservice Provisioning API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |addresses[type eq "work"].formatted|String|
   |locale|String|
   |title|String|
   |timezone|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス|
   |urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:isAgent|String|

> [!NOTE]
> 下の手順に従い、アプリケーションのニーズに合わせてカスタム拡張属性をスキーマに追加できます。
> * [マッピング] で、 **[Provision Azure Active Directory Users]\(Azure Active Directory ユーザーをプロビジョニングする\)** を選択します。
> * ページの下部にある **[Show advanced options]\(詳細オプションの表示\)** を選択します。
> * **[Edit attribute list for Freshservice]\(Freshservice の属性リストの編集\)** を選択します。
> * 属性の一覧の下部にあるフィールドに、カスタム属性に関する情報を入力します。 カスタム属性 urn 名前空間は、下の例に示すパターンに従う必要があります。 **CustomAttribute** は、アプリケーションの要件に応じてカスタマイズできます。例: urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:**isAgent**。
> * カスタム属性に対して適切なデータ型を選択し、 **[保存]** をクリックする必要があります。
> * 既定のマッピング画面に戻り、 **[新しいマッピングの追加]** をクリックします。 カスタム属性が **[ターゲット属性]** ドロップダウン リストに表示されます。

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Freshservice Provisioning に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で必要な値を選択して、Freshservice Provisioning にプロビジョニングするユーザーを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作によって、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../manage-apps/application-provisioning-quarantine-status.md)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)