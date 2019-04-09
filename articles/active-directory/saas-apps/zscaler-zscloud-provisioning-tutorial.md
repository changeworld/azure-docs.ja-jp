---
title: チュートリアル:Zscaler ZSCloud を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Zscaler ZSCloud に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant-msft
ms.openlocfilehash: 3f7fcd59bafe5619a1ef411bf81a6b8c3431f22c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087416"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>チュートリアル:Zscaler ZSCloud を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Zscaler ZSCloud にプロビジョニングまたは Zscaler ZSCloud からプロビジョニング解除するように構成するために、Replicon と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../active-directory-saas-app-provisioning.md)」を参照してください。
> 
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure AD テナント
*   Zscaler ZSCloud テナント
*   Admin アクセス許可がある Zscaler ZSCloud のユーザー アカウント

> [!NOTE]
> Azure AD プロビジョニング統合では、Enterprise パッケージを含むアカウントについて Zscaler ZSCloud 開発者が使用できる Zscaler ZSCloud SCIM API が必要です。

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>ギャラリーからの Zscaler ZSCloud の追加
Azure AD で自動ユーザー プロビジョニング用に Zscaler ZSCloud を構成する前に、Azure AD アプリケーション ギャラリーから Zscaler ZSCloud を管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Zscaler ZSCloud を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] セクション][2]

3. Zscaler ZSCloud を追加するには、ダイアログの上部にある **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**Zscaler ZSCloud**」と入力します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/appsearch.png)

5. 結果パネルで **[Zscaler ZSCloud]** を選択し、**[追加]** をクリックして Zscaler ZSCloud を SaaS アプリケーションの一覧に追加します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/appsearchresults.png)

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/appcreation.png)

## <a name="assigning-users-to-zscaler-zscloud"></a>Zscaler ZSCloud にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zscaler ZSCloud へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Zscaler ZSCloud に割り当てることができます。

*   [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>ユーザーを Zscaler ZSCloud に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Zscaler ZSCloud に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Zscaler ZSCloud にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-zscaler-zscloud"></a>Zscaler ZSCloud への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Zscaler ZSCloud のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Zscaler ZSCloud では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Zscaler ZSCloud シングル サインオンのチュートリアル](zscaler-zsCloud-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-zscloud-in-azure-ad"></a>Azure AD で Zscaler ZSCloud の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインし、**[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** に移動します。

2. SaaS アプリケーションの一覧から Zscaler ZSCloud を選択します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/appinstancesearch.png)

3. **[プロビジョニング]** タブを選択します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. **[管理者資格情報]** セクションで、手順 6 で説明する Zscaler ZSCloud アカウントの **[テナント URL]** と **[シークレット トークン]** を入力します。

6. **[テナント URL]** と **[シークレット トークン]** を取得するには、Zscaler ZSCloud ポータル ユーザー インターフェイスで **[管理]、[認証の設定]** の順に移動し、**[認証の種類]** で **[SAML]** をクリックします。 

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    **[Configure SAML]\(SAML の構成\)** をクリックし、**[Configuration SAML]\(構成 SAML\)** オプションを開きます。 

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)
    
    **[Enable SCIM-Based Provisioning]\(SCIM ベースのプロビジョニングを有効にする\)** を選択して、**ベース URL** と**ベアラー トークン**を取得し、設定を保存します。 Azure portal で**ベース URL**を**テナント URL** にコピーし、**ベアラー トークン**を**シークレット トークン**にコピーします。

7. 手順 5 の各フィールドに値を入力したら、**[テスト接続]** をクリックして、Azure AD が Zscaler ZSCloud に接続できることを確認します。 接続できない場合は、使用中の Zscaler ZSCloud アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)
    
8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、**[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. **[Save]** をクリックします。

10. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to Zscaler ZSCloud]\(Azure Active Directory ユーザーを Zscaler ZSCloud に同期する\)** を選択します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Zscaler ZSCloud に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler ZSCloud のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. **[マッピング]** セクションで **[Synchronize Azure Active Directory Groups to Zscaler ZSCloud]\(Azure Active Directory グループを Zscaler ZSCloud に同期する\)** を選択します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. **[属性マッピング]** セクションで、Azure AD から Zscaler ZSCloud に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler ZSCloud のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](./../active-directory-saas-scoping-filters.md)の次の手順を参照してください。

15. Zscaler ZSCloud に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Zscaler ZSCloud にプロビジョニングするユーザーやグループを定義します。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. プロビジョニングの準備ができたら、**[保存]** をクリックします。

    ![Zscaler ZSCloud プロビジョニング](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

これにより、**[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Zscaler ZSCloud に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
