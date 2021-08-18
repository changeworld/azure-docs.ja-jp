---
title: 'チュートリアル: UNIFI を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure AD から UNIFI に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法を学習します。
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 924c603f-574e-4e0a-9345-0cb0c7593dbb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: thwimmer
ms.openlocfilehash: 6fee01cbaf2c299ebc54536e75e278b3616f0166
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195463"
---
# <a name="tutorial-configure-unifi-for-automatic-user-provisioning"></a>チュートリアル: UNIFI を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成する場合に、UNIFI と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD で Azure AD プロビジョニング サービスを使用して、[UNIFI](http://www.unifilabs.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * UNIFI でユーザーを作成する
> * アクセスが不要になった場合に UNIFI のユーザーを削除する
> * Azure AD と UNIFI の間でユーザー属性の同期を維持する
> * UNIFI でグループとグループ メンバーシップをプロビジョニングする
> * UNIFI への[シングル サインオン](unifi-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [UNIFI](http://www.unifilabs.com/) テナント。
* 管理者アクセス許可を持つ UNIFI のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と UNIFI の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-unifi-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように UNIFI を構成する

1. Azure 内のご利用のエンタープライズ アプリケーションで SSO が正常に有効になっていることを確認します。 
1. [シングル サインオン] で **[ログイン URL]** を見つけます。 ここでは、`https://login.microsoftonline.com/<guid>/saml2` です。
1. [SAML 署名証明書] セクションで証明書 (Base64) をダウンロードします。

    ![エンタープライズ アプリケーションの SSO ビュー](media/unifi-provisioning-tutorial/enterprise-application-view.png)

1. 使用する ID プロバイダーが UNIFI に追加されていない場合は、**会社の管理者** として UNIFI ポータルにログインします。 **[ユーザー] -> [SSO の構成] -> [プロバイダーの追加]** ボタンの順に移動します。

    ![ID プロバイダー ビューの追加](media/unifi-provisioning-tutorial/add-identity-provider-view.png)

1. [SSO プロバイダーの追加] モーダルが表示されます。

    ![ID プロバイダー モデルの追加](media/unifi-provisioning-tutorial/add-identity-provider-modal.png)

1. **[名前]** に希望する一意の値を指定します。 **[URL]** は、ご利用の Azure AD エンタープライズ アプリケーションからの **[ログイン URL]** とします。 **[トークン]** に任意の値を指定します。 **[証明書]** フィールドに証明書 (Base64) の値を入力します。 この時点から、作成したすべてのユーザーがこの ID プロバイダーを使用できるようにする場合は、 **[Make this the default identity provider]\(これを既定の ID プロバイダーにする\)** チェックボックスをオンにします。

    ![設定された [ID プロバイダーの追加] モーダル](media/unifi-provisioning-tutorial/add-identity-provider-modal-populated.png)

1. [保存] ボタンをクリックします。

## <a name="step-3-add-unifi-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから UNIFI を追加する

Azure AD アプリケーション ギャラリーから UNIFI を追加して、UNIFI へのプロビジョニングの管理を開始します。 以前に SSO 向けに UNIFI を設定したことがある場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* UNIFI にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-unifi"></a>手順 5. UNIFI への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて UNIFI のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-unifi-in-azure-ad"></a>Azure AD で UNIFI の自動ユーザー プロビジョニングを構成するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[UNIFI]** を選択します。

    ![アプリケーションの一覧の UNIFI リンク](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで、UNIFI の **[テナントの URL]**  -`https://licensing.inviewlabs.com/api/scim/v2/` と **[シークレット トークン]** を入力します。 **[テスト接続]** をクリックして、Azure AD から UNIFI に確実に接続できるようにします。 接続できない場合は、ご利用の UNIFI アカウントに管理者アクセス許可を確実に割り当ててから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

1. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to UNIFI]\(Azure Active Directory ユーザーを UNIFI に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から UNIFI に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で UNIFI のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に UNIFI API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|   
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|


1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to UNIFI] (Azure Active Directory グループを UNIFI に同期する)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から UNIFI に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で UNIFI のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|フィルター処理のサポート|
      |---|---|---|
      |displayName|String|&check;
      |members|リファレンス|
      |externalId|String|      

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

1. UNIFI に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、UNIFI にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="more-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)