---
title: 'チュートリアル: Azure Active Directory による自動ユーザー プロビジョニングに対応するように Grammarly を構成する | Microsoft Docs'
description: Azure AD から Grammarly に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: ca01289ce66afe642081e5be17373e640dd1e46d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864807"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニングに対応するように Grammarly を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Grammarly と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[Grammarly](https://www.grammarly.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Grammarly でユーザーを作成する
> * アクセスが不要になった場合に Grammarly のユーザーを削除する
> * Azure AD と Grammarly の間でユーザー属性の同期を維持する

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* プロビジョニングを構成するための[アクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* 管理者アクセス権を持つ Grammarly ビジネス アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)を確認します。
1. [プロビジョニングの対象](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)となるユーザーを決定します。
1. [Azure AD と Grammarly の間でマップする](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)データを決定します。 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Grammarly を構成する

Grammarly の担当者に連絡するか、<support@grammarly.com> に電子メールを送信して、プロビジョニング トークンを要求します。

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Grammarly を追加する

Azure AD アプリケーション ギャラリーから Grammarly を追加して、Grammarly へのプロビジョニングの管理を開始します。 SSO 用に Grammarly を以前に設定している場合は、同じアプリケーションを使用できます。 統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちらのクイックスタート](../manage-apps/add-application-portal.md)に関するページを参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する

Azure AD プロビジョニング サービスを使用して、アプリケーションへの割り当てや、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープ設定を選択した場合は、[スコープ フィルターを使用したアプリのプロビジョニング](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)に関するページで説明されているスコープ フィルターを使用できます。

* Grammarly にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、このオプションを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>手順 5. Grammarly への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて TestApp のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Azure AD で Grammarly の自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ペインのスクリーンショット。](common/enterprise-applications.png)

1. アプリケーションの一覧で、**Grammarly** を選択します。

    ![アプリケーションの一覧に Grammarly リンクが表示されているスクリーンショット。](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブを示すスクリーンショット。](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング モード] が [自動] に設定されていることを示すスクリーンショット。](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションの **[テナントの URL]** フィールドに「`https://sso.grammarly.com/scim/v2`」と入力し、 **[シークレット トークン]** フィールドに Grammarly から提供されたトークンを入力します (上記の手順 2 を参照してください)。 **[テスト接続]** をクリックして、Azure AD から Grammarly への接続を確保します。 接続できない場合は、ご自分の Grammarly アカウントに管理者アクセス許可があることを保証してから、もう一度試します。

    ![[テナントの URL] と [シークレット トークン] のボックスが表示されているスクリーンショット。](common/provisioning-testconnection-tenanturltoken.png)

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![[通知用メール] ボックスを示すスクリーンショット。](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to Grammarly]\(Azure Active Directory ユーザーを Grammarly に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Grammarly に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Grammarly のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Grammarly API で確実にサポートされる必要があります。 すべての変更をコミットするには、 **[保存]** を選択します。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |active|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|


1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)に記載されている手順を参照してください。

1. Grammarly に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[Provisioning Status]\(プロビジョニングの状態\)** を **[オン]** に変更します。

    ![[プロビジョニングの状態] が [オン] に設定された画面のスクリーンショット。](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Grammarly にプロビジョニングするユーザーまたはグループを定義します。

    ![プロビジョニングのスコープが表示されているスクリーンショット。](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![[保存] ボタンが表示されているスクリーンショット。](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 最初のサイクルの実行は後続のサイクルよりも時間がかかります。Azure AD のプロビジョニング サービスが実行されている限り、後続のサイクルは約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する

プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[検疫状態のアプリケーションのプロビジョニング](../app-provisioning/application-provisioning-quarantine-status.md)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
