---
title: チュートリアル:G Suite を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から G Suite に対してユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: beaa8561028a9e21d0623c0eb8e19592f3cad055
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167869"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>チュートリアル:G Suite を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、ユーザーまたはグループ、あるいはその両方を G Suite に自動的にプロビジョニングしたり、プロビジョニング解除したりするように Azure AD を構成するために G Suite と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

> [!NOTE]
> G Suite コネクタは最近、2019 年 10 月に更新されました。 G Suite コネクタに加えられた変更には、次のものがあります。
- G Suite の追加のユーザーおよびグループ属性に対するサポートが追加されました。 
- G Suite ターゲットの属性名が、[ここで](https://developers.google.com/admin-sdk/directory)定義されている内容に一致するように更新されました。
- 既定の属性マッピングが更新されました。

## <a name="prerequisites"></a>前提条件

G Suite と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD テナント
- [G Suite テナント](https://gsuite.google.com/pricing.html)
- 管理者アクセス許可を持つ G Suite 上のユーザー アカウント。

## <a name="assign-users-to-g-suite"></a>ユーザーを G Suite に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Azure AD 内のどのユーザーまたはグループ、あるいはその両方に G Suite へのアクセスが必要かを決定する必要があります。 決定したら、次の手順に従って、これらのユーザーまたはグループ、あるいはその両方を G Suite に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>ユーザーを G Suite に割り当てるための重要なヒント

* 単一の Azure AD ユーザーを G Suite に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* G Suite にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-g-suite-for-provisioning"></a>プロビジョニングのために G Suite を設定する

Azure AD での自動ユーザー プロビジョニング用に G Suite を構成する前に、G Suite 上で SCIM プロビジョニングを有効にする必要があります。

1. [G Suite の管理コンソール](https://admin.google.com/)に管理者アカウントでサインインし、 **[セキュリティ]** を選択します。 このリンクが表示されていない場合、画面下部の **[その他の設定]** メニューに隠れていることがあります。

    ![[セキュリティ] を選択する][10]

2. **[セキュリティ]** ページで、 **[API リファレンス]** を選択します。

    ![API リファレンスを選択する][15]

3. **[API アクセスを有効にする]** を選択します。

    ![API リファレンスを選択する][16]

   > [!IMPORTANT]
   > G Suite にプロビジョニングしようとしているすべてのユーザーについて、その Azure AD でのユーザー名がカスタム ドメインに関連付けられている**必要があります**。 たとえば G Suite では、bob@contoso.onmicrosoft.com のようなユーザー名は使用できませんが、 bob@contoso.com のようなユーザー名は使用できます。 既存のユーザーのドメインは、[ここ](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)にある手順に従って変更できます。

4.  Azure AD で目的のカスタム ドメインを追加して検証したら、それらを G Suite で再度検証する必要があります。 G Suite でドメインを検証するには、次の手順を参照してください。

    a. [G Suite の管理コンソール](https://admin.google.com/)で、 **[ドメイン]** を選択します。

    ![ドメインを選択する][20]

    b. **[ドメインやドメイン エイリアスを追加]** を選択します。

    ![Add a new domain][21]

    c. **[別のドメインを追加]** を選択し、追加するドメインの名前を入力します。

    ![Type in your domain name][22]

    d. **[Continue and verify domain ownership]\(続行してドメインの所有権を確認\)** を選択します。 次に、手順に従って、ドメイン名を所有していることを確認します。 Google でドメインを検証する方法に関する包括的な手順については、「[サイトの所有権を確認する](https://support.google.com/webmasters/answer/35179)」を参照してください。

    e. G Suite に追加しようとしているすべての追加のドメインについて、前の手順を繰り返します。

5. 次に、G Suite でユーザー プロビジョニングを管理するためにどの管理者アカウントを使用するかを決定します。 **[Admin Roles] (管理者ロール)** に移動します。

    ![Google Apps を選択する][26]
    
6. そのアカウントの **[Admin role] (管理者ロール)** で、そのロールの **[特権]** を編集します。 このアカウントをプロビジョニングに使用できるように、 **[Admin API Privileges]\(管理 API の権限\)** がすべて有効になっていることを確認します。

    ![Google Apps を選択する][27]

## <a name="add-g-suite-from-the-gallery"></a>ギャラリーから G Suite を追加する

Azure AD で自動ユーザー プロビジョニング用に G Suite を構成するには、Azure AD アプリケーション ギャラリーから G Suite をマネージド SaaS アプリケーションの一覧に追加する必要があります。 

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**G Suite**」と入力し、結果ウィンドウで **[G Suite]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の G Suite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>G Suite への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーまたはグループ、あるいはその両方の割り当てに基づいて G Suite でユーザーまたはグループ、あるいはその両方を作成、更新、および無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> [G Suite のシングル サインオン](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)に関するチュートリアルで提供されている手順に従って、G Suite で SAML ベースのシングル サインオンを有効にすることも選択できます。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> G Suite の Directory API エンドポイントについて詳しくは、[Directory API](https://developers.google.com/admin-sdk/directory) に関するページをご覧ください。

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Azure AD で G Suite の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[G Suite]** を選択します。

    ![アプリケーションの一覧の G Suite のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションにある **[承認する]** を選択します。 これで、ブラウザーの新しいウィンドウで Google 認可ダイアログ ボックスが開きます。

    ![G Suite の [承認する]](media/google-apps-provisioning-tutorial/authorize.png)

6. Azure AD に G Suite テナントを変更するためのアクセス許可を付与することを確認します。 **[Accept]\(承認\)** を選択します。

    ![Confirm permissions.][28]

7. Azure portal で、 **[テスト接続]** を選択して Azure AD がアプリに接続できることを確認します。 接続が失敗した場合は、使用中の G Suite アカウントに Team Admin アクセス許可があることを確認します。 その後、**承認**手順を再び試します。

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[Save]** をクリックします。

9. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Users to G Suite] (Azure Active Directory ユーザーを G Suite に同期する)** を選択します。

    ![G Suite のユーザー マッピング](media/google-apps-provisioning-tutorial/usermappings.png)

10. **[属性マッピング]** セクションで、Azure AD から G Suite に同期されるユーザー属性を確認します。 **[Matching] (照合)** プロパティとして選択されている属性は、更新操作のために G Suite のユーザー アカウントを照合するために使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![G Suite のユーザー属性](media/google-apps-provisioning-tutorial/userattributes.png)

11. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to G Suite] (Azure Active Directory グループを G Suite に同期する)** を選択します。

    ![G Suite のグループ マッピング](media/google-apps-provisioning-tutorial/groupmappings.png)

12. **[属性マッピング]** セクションで、Azure AD から G Suite に同期されるグループ属性を確認します。 **[Matching] (照合)** プロパティとして選択されている属性は、更新操作のために G Suite のグループを照合するために使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![G Suite のグループ属性](media/google-apps-provisioning-tutorial/groupattributes.png)

13. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

14. G Suite で Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

15. **[設定]** セクションの **[スコープ]** で目的の値を選択することによって、G Suite にプロビジョニングするユーザーまたはグループ、あるいはその両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

16. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視したり、G Suite 上で Azure AD プロビジョニング サービスによって実行されたすべてのアクションが記載されているプロビジョニング アクティビティ レポートへのリンクに従ったりできます。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)


<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
