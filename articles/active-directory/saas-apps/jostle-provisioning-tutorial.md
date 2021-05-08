---
title: 'チュートリアル: Azure Active Directory を使用した自動ユーザー プロビジョニングに対応するよう Jostle を構成する | Microsoft Docs'
description: Azure AD から Jostle に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368499"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニングに対応するよう Jostle を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Jostle と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用した [Jostle](https://www.jostle.me/) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Jostle でユーザーを作成する
> * アクセスが不要になった Jostle のユーザーを削除する
> * Azure AD と Jostle の間でユーザー属性の同期を維持する
> * Jostle への[シングル サインオン](jostle-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [Jostle テナント](https://www.jostle.me/)。
* 管理者アクセス許可がある Jostle のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する

1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と Jostle の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Jostle を構成する

### <a name="automation-account"></a>Automation アカウント

開始する前に、Jostle イントラネットで **Automation ユーザー** を作成する必要があります。 これは Azure での構成に使用するアカウントになります。 Automation ユーザーは、管理の **[Settings]\(設定\) > [User accounts and data]\(ユーザー アカウントとデータ\) > [Manage Automation users]\(Automation ユーザーの管理\)** で作成できます。

Automation ユーザーとその作成方法の詳細については、[こちらの記事](https://forum.jostle.us/hc/en-us/articles/360057364073)を参照してください。

作成した Automation ユーザー アカウントは、Azure の構成に使用する前に、**アクティブ化する必要があります** (つまり、少なくとも 1 回はイントラネットにログインする必要があります)。

### <a name="manage-user-provisioning"></a>ユーザー プロビジョニングを管理する

開始する前に、アカウントのサブスクリプションに **SSO/ユーザープロビジョニング機能が含まれている** ことを確認してください。 含まれていない場合は、カスタマー サクセス マネージャー<success@jostle.me> にご連絡いただければ、アカウントへの追加を支援いたします。

次のステップでは、Jostle から **API URL** と **API キー** を取得します。

1. メイン ナビゲーションに移動し、 **[Admin Settings]\(管理者設定\)** をクリックします。
1. **[User data to/from other systems]\(他システムから/へのユーザー データ\)** で、 **[Manage user provisioning]\(ユーザー プロビジョニングの管理\)** をクリックします。ここで [Manage user provisioning]\(ユーザー プロビジョニングの管理\) が表示されず、アカウントに SSO またはユーザー プロビジョニングが含まれていることを確認した場合は、サポート <support@jostle.me> に連絡して、管理者設定でこのページを有効にするようご依頼ください。
1. **[User Provisioning API details]\(User Provisioning AP の詳細\)** セクションで、 **[ベース URL]** フィールドに移動し、[コピー] ボタンをクリックして、後で簡単にアクセスできる場所に URL を保存します。                                                           

   ![プロビジョニング](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. 次に、 **[Add a new key]\(新しいキーの追加\)** ボタンをクリックします。
1. 次の画面で、 **[Automation User]\(Automation ユーザー\)** フィールドに移動し、ドロップダウン メニューを使用して Automation ユーザー アカウントを選択します。 

   ![統合アカウント](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. **[Provisioning API key description]\(Provisioning API キーの説明\)** フィールドで、キーに名前 (つまり、「Azure」) を付けてから、 **[追加]** ボタンをクリックします。

1. キーが生成されたら **すぐにコピーして**、URL を保存した場所に保存します (キーが表示されるのはこのタイミングだけのため)。                                                               
1. 次に、**API URL** と **API キー** を使用して、Azure での統合を構成します。
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Jostle を追加する

Azure AD アプリケーション ギャラリーから Jostle を追加して、Jostle へのプロビジョニングの管理を開始します。 以前に SSO 用の Jostle を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-gallery-app.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Jostle にユーザーとグループを割り当てる場合は、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>手順 5. Jostle への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーとグループの割り当てに基づいて Jostle アプリでユーザーとグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Azure AD で Jostle の自動ユーザー プロビジョニングを構成するには、以下の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Jostle]** を選択します。

    ![アプリケーションの一覧の [Jostle] リンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

1.  **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで、Jostle **[テナント URL]** と **[シークレット トークン]** の情報を入力します。 **[テスト接続]** を選択して、Azure AD が Jostle に接続できることを確認します。 接続に失敗した場合は、Jostle アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力します。 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[Mappings]\(マッピング\)** セクションで、 **[Synchronize Azure Active Directory Users to Jostle]\(Azure Active Directory ユーザーを Jostle に同期する\)** を選択します。

1. **[Attribute Mapping]\(属性マッピング\)** セクションで、Azure AD から Jostle に同期されたユーザー属性を確認します。 **照合** プロパティとして選択された属性は、更新の操作を行う場合に Jostle のユーザー アカウントを照合する際に使用されます。 [照合するターゲット属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、Jostle API がその属性に基づくユーザーのフィルター処理をサポートしていることを確認する必要があります。 すべての変更をコミットするには、 **[保存]** を選択します。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "personal"].value|String|
   |emails[type eq "alternate1"].value|String|
   |emails[type eq "alternate2"].value|String|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|String|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |String|  

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)に記載されている手順を参照してください。

1. Jostle の Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **オン** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Jostle にプロビジョニングするユーザーまたはグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 最初のサイクルの実行は以降のサイクルよりも時間がかかります。Azure AD のプロビジョニング サービスが実行されている限り、後続のサイクルは約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する

プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[検疫状態のアプリケーションのプロビジョニング](../app-provisioning/application-provisioning-quarantine-status.md)に関する記事をご覧ください。

## <a name="more-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)