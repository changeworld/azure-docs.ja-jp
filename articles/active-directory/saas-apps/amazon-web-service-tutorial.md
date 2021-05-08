---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と AWS Single-Account Access の統合 | Microsoft Docs'
description: Azure Active Directory と AWS Single-Account Access の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: jeedes
ms.openlocfilehash: eb469c757e2898a9925dd7d3358cfe95734cb2e9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537726"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と AWS Single-Account Access の統合

このチュートリアルでは、AWS Single-Account Access と Azure Active Directory (Azure AD) を統合する方法について説明します。 AWS Single-Account Access を Azure AD と統合すると、次のことができます。

* AWS Single-Account Access にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで AWS Single-Account Access に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーのさまざまな AWS アプリケーションについて
下記の情報を利用して、Azure AD アプリケーション ギャラリーの AWS Single Sign-On アプリケーションと AWS Single-Account Access アプリケーションのどちらを使用するかを判断します。

**AWS Single Sign-On**

[AWS Single Sign-On](./aws-single-sign-on-tutorial.md) は、2021 年 2 月に Azure AD アプリケーション ギャラリーに追加されました。 Microsoft Azure AD を通してサインインすると、複数の AWS アカウントや AWS アプリケーションへのアクセスを一元的に管理しやすくなります。 一度 Microsoft Azure AD と AWS SSO とのフェデレーションを行って、1 つの場所から AWS SSO を使用して、すべての AWS アカウントにわたってアクセス許可を管理します。 AWS SSO では、アクセス許可が自動的にプロビジョニングされ、それらは、ポリシーやアクセスの割り当てを更新するときに最新の状態に維持されます。 エンド ユーザーは、自分の Azure AD 資格情報を使用して認証し、AWS コンソール、コマンド ライン インターフェイス、AWS SSO 統合アプリケーションにアクセスすることができます。

**AWS Single-Account Access**

[AWS Single-Account Access]() は、これまで数年間にわたってお客様に使用されており、これを使用すると、Azure AD を 1 つの AWS アカウントにフェデレーションし、Azure AD を使用して AWS IAM ロールへのアクセスを管理できます。 AWS IAM 管理者は、各 AWS アカウントにロールとポリシーを定義します。 各 AWS アカウントのために、Azure AD 管理者は、AWS IAM へのフェデレーション、アカウントへのユーザーまたはグループの割り当て、ロールのアクセスを承認するアサーションを送信するための Azure AD の構成を行います。  

| 機能 | AWS Single Sign-On | AWS Single-Account Access |
|:--- |:---:|:---:|
|条件付きアクセス| すべての AWS アカウントに対する単一の条件付きアクセス ポリシーがサポートされます。 | すべてのアカウントに対する単一の条件付きアクセス ポリシーまたはアカウントごとのカスタム ポリシーがサポートされます。|
| CLI アクセス | サポートされています | サポートされています|
| Privileged Identity Management | まだサポートされていません | まだサポートされていません |
| アカウント管理の一元化 | AWS でのアカウント管理を一元化します。 | Azure AD でのアカウント管理を一元化します (おそらく、アカウントごとに Azure AD のエンタープライズ アプリケーションが必要になります)。 |
| SAML 証明書| 単一の証明書| アプリやアカウントごとに別個の証明書 | 

## <a name="aws-single-account-access-architecture"></a>AWS Single-Account Access のアーキテクチャ
![Azure AD と AWS の関係の図](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

複数のインスタンスに対して複数の識別子を構成できます。 次に例を示します。

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Azure AD ではこれらの値から **#** の値を削除し、正しい値 `https://signin.aws.amazon.com/saml` を SAML トークンの対象 URL として送信します。

次の理由により、このアプローチをお勧めします。

- アプリケーションごとに一意の X509 証明書が提供されます。 AWS アプリのインスタンスごとに異なる証明書の有効期限を設定でき、それらを個別の AWS アカウントに基づいて管理できます。 この場合、証明書全体のロールオーバーが容易になります。

- Azure AD での AWS アプリによるユーザー プロビジョニングを有効にでき、続いてその AWS アカウントからすべてのロールが Microsoft のサービスによってフェッチされます。 アプリでの AWS ロールの追加や更新は手動で行う必要はありません。

- アプリに対してアプリ所有者を個別に割り当てることができます。 このユーザーは、Azure AD で直接アプリを管理できます。

> [!Note]
> 必ずギャラリー アプリケーションのみを使用してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AWS シングル サインオン (SSO) が有効なサブスクリプション。

> [!Note]
> ロールのインポートを行う際に、Azure AD でロールを手動で編集することは避けてください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* AWS Single-Account Access では、**SP および IDP** によって開始される SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-aws-single-account-access-from-the-gallery"></a>ギャラリーからの AWS Single-Account Access の追加

Azure AD への AWS Single-Account Access の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に AWS Single-Account Access を追加する必要があります。

1. 職場アカウント、学校アカウント、または個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。
1. Azure Active Directory の概要メニューで、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** を選択します。
1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**AWS Single-Account Access**」と入力します。
1. 結果のパネルから **[AWS Single-Account Access]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>AWS Single-Account Access の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、AWS Single-Account Access に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと AWS Single-Account Access の関連ユーザーとの間にリンク関係を確立する必要があります。

AWS Single-Account Access で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[AWS Single-Account Access の SSO の構成](#configure-aws-single-account-access-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[AWS Single-Account Access のテスト ユーザーの作成](#create-aws-single-account-access-test-user)** - AWS Single-Account Access で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
    1. **[AWS Single-Account Access でロール プロビジョニングを構成する方法](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **AWS Single-Account Access** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、 **[識別子 (エンティティ ID)]** と **[応答 URL]** の両方を同じ既定値 (`https://signin.aws.amazon.com/saml`) に更新します。 **[保存]** を選択して構成の変更を保存する必要があります。

1. 複数のインスタンスを構成している場合は、識別子の値を指定します。 2 番目のインスタンス以降は、次の形式を使用します。これには、一意の SPN 値を指定するための **#** 符号が含まれます。

    `https://signin.aws.amazon.com/saml#2`

1. AWS アプリケーションでは特定の形式の SAML アサーションが使用されるため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、AWS アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前  | ソース属性  | 名前空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Role | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | 「900 秒 (15 分) から43200 秒 (12 時間) の値を指定してください」 |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > AWS では、アプリケーションに対してユーザーのロールが割り当てられていることを想定しています。 ユーザーに適切なロールを割り当てることができるように、Azure AD でこれらのロールを設定してください。 Azure AD でロールを構成する方法については、[こちら](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)を参照してください

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** (手順 3) ダイアログ ボックスで、 **[証明書の追加]** を選択します。

    ![新しい SAML 証明書を作成する](common/add-saml-certificate.png)

1. 新しい SAML 署名証明書を生成し、 **[新しい証明書]** を選択します。 証明書通知のメール アドレスを入力します。
   
    ![新しい SAML 証明書](common/new-saml-certificate.png) 

1. **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/amazon-web-service-tutorial/certificate.png)

1. **[AWS Single-Account Access のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。
1. Azure Active Directory の概要メニューで、 **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に AWS Single-Account Access へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーション一覧で **[AWS Single-Account Access]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-aws-single-account-access-sso"></a>AWS Single-Account Access SSO の構成

1. 別のブラウザー ウィンドウで、管理者として AWS 企業サイトにサインオンします。

2. **AWS ホーム** を選択します。

    ![AWS ホーム アイコンが強調表示された AWS 企業サイトのスクリーンショット][11]

3. **[Identity and Access Management]\(ID とアクセス管理\)** を選択します。

    ![IAM が強調表示された AWS サービス ページのスクリーンショット][12]

4. **[ID プロバイダー]**  >  **[プロバイダーの作成]** を選択します。

    ![[ID プロバイダー] と [プロバイダーの作成] が強調表示された IAM ページのスクリーンショット][13]

5. **[プロバイダーの設定]** ページで、次の手順を行います。

    ![[プロバイダーの設定] のスクリーンショット][14]

    a. **[Provider Type]\(プロバイダーの種類\)** で **[SAML]** を選択します。

    b. **[プロバイダ名]** にプロバイダー名を入力します (例: *WAAD*)。

    c. Azure Portal からダウンロードした **メタデータ ファイル** をアップロードするには、 **[Choose File]\(ファイルの選択\)** を選択します。

    d. **[Next Step]\(次のステップ\)** を選択します。

6. **[プロバイダー情報の検証]** ページで **[作成]** を選択します。

    ![[作成] が強調表示された [プロバイダー情報の検証] のスクリーンショット][15]

7. **[ロール]**  >  **[ロールの作成]** を選択します。

    ![[ロール] ページのスクリーンショット][16]

8. **[Create role]** ページで、以下の手順を実行します。  

    ![[ロールの作成] ページのスクリーンショット][19]

    a. **[信頼されたエンティティの種類を選択]** で、 **[SAML 2.0 フェデレーション]** を選択します。

    b. **[SAML 2.0 プロバイダーを選択]** で、先ほど作成した **SAML プロバイダー** を選択します (例: *WAAD*)。

    c. **[Allow programmatic and AWS Management Console access]** を選択します。
  
    d. **Permissions\(次へ: アクセス許可\)** をクリックします。

9. **[Attach アクセス権限ポリシー]** ダイアログ ボックスで、組織の規定に準拠した適切なポリシーをアタッチします。 次に、**次のステップ: 確認\)** をクリックします。  

    ![アクセス権限ポリシーをアタッチするダイアログ ボックスのスクリーンショット][33]

10. **[確認]** ダイアログ ボックスで、次の手順を行います。

    ![[確認] ダイアログ ボックスのスクリーンショット][34]

    a. **[ロール名]** に、使用するロール名を入力します。

    b. **[ロールの説明]** に説明を入力します。

    c. **[ロールの作成]** を選択します。

    d. 必要な数のロールを作成し、それらを ID プロバイダーにマップします。

11. Azure AD ユーザー プロビジョニングの際に AWS アカウントからロールをフェッチするには、AWS サービス アカウントの資格情報を使用します。 そのためには、AWS コンソール ホームを開きます。

12. **[サービス]** を選択します。 **[セキュリティ、アイデンティティ、コンプライアンス]** の **[IAM]** を選択します。

    ![[サービス] と [IAM] が強調表示された AWS コンソール ホームのスクリーンショット](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. IAM セクションで **[ポリシー]** を選択します。

    ![[ポリシー] が強調表示された IAM セクションのスクリーンショット](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Azure AD ユーザー プロビジョニングの際に AWS アカウントからロールをフェッチするために、 **[ポリシーの作成]** を選択して新しいポリシーを作成します。

    ![[ポリシーの作成] が強調表示された [ロールの作成] ページのスクリーンショット](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. AWS アカウントからすべてのロールをフェッチする独自のポリシーを作成します。

    ![[JSON] が強調表示された [ポリシーの作成] ページのスクリーンショット](./media/amazon-web-service-tutorial/policy1.png)

    a. **[ポリシーの作成]** セクションで、 **[JSON]** タブを選択します。

    b. ポリシー ドキュメントで、次の JSON を追加します。

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. **[Review policy] ボタン** を選択して、ポリシーを検証します。

    ![[ポリシーの作成] ページのスクリーンショット](./media/amazon-web-service-tutorial/policy5.png)

16. 新しいポリシーを定義します。

    ![[名前] と [説明] のフィールドが強調表示された [ポリシーの作成] ページのスクリーンショット](./media/amazon-web-service-tutorial/policy2.png)

    a. **[名前]** に「**AzureAD_SSOUserRole_Policy**」と入力します。

    b. ポリシーの **[説明]** に、「**This policy will allow to fetch the roles from AWS accounts**」と入力します。

    c. **[ポリシーの作成]** を選択します。

17. AWS IAM サービスの新しいユーザー アカウントを作成します。

    a. AWS IAM コンソールで、 **[ユーザー]** を選択します。

    ![[ユーザー] が強調表示された AWS IAM コンソールのスクリーンショット](./media/amazon-web-service-tutorial/policy3.png)

    b. 新しいユーザーを作成するために、 **[ユーザーを追加]** を選択します。

    ![[ユーザーを追加] ボタンのスクリーンショット](./media/amazon-web-service-tutorial/policy4.png)

    c. **[ユーザーを追加]** セクションで、次の手順を行います。

    ![[ユーザー名] と [アクセスの種類] が強調表示された [ユーザーを追加] ページのスクリーンショット](./media/amazon-web-service-tutorial/adduser1.png)

    * ユーザー名として「**AzureADRoleManager**」を入力します。

    * [アクセスの種類] で **[プログラムによるアクセス]** を選択します。 こうすると、ユーザーは API を呼び出し、AWS アカウントからロールをフェッチできます。

    * **[次のステップ: アクセス権限]** を選択します。

18. このユーザー用の新しいポリシーを作成します。

    ![このスクリーンショットは、[Add user]\(ユーザーを追加\) ページを示しています。ここで、ユーザーのポリシーを作成できます。](./media/amazon-web-service-tutorial/adduser2.png)

    a. **[既存のポリシーを直接アタッチ]** を選択します。

    b. [フィルター] セクションで、新しく作成されたポリシー **AzureAD_SSOUserRole_Policy** を検索します。

    c. ポリシーを選択し、**次のステップ: 確認\)** をクリックします。

19. ユーザーにアタッチしたポリシーを確認します。

    ![[ユーザーの作成] が強調表示された [ユーザーを追加] ページのスクリーンショット](./media/amazon-web-service-tutorial/adduser3.png)

    a. ユーザー名、アクセスの種類、ユーザーにマップされているポリシーを確認します。

    b. **[Create user]\(ユーザーの作成\)** を選択します。

20. ユーザーのユーザー資格情報をダウンロードします。

    ![このスクリーンショットは、ユーザー資格情報を取得するための [Download c s v]\(c s v のダウンロード\) ボタンを備えた [Add user]\(ユーザーを追加\) ページを示しています。](./media/amazon-web-service-tutorial/adduser4.png)

    a. ユーザーの **[Access key ID]** と **[Secret access key]** をコピーします。

    b. これらの資格情報を Azure AD の [ユーザー プロビジョニング] セクションに入力して、AWS コンソールからロールをフェッチします。

    c. **[閉じる]** を選択します。

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>AWS Single-Account Access でロール プロビジョニングを構成する方法

1. Azure AD 管理ポータルの AWS アプリで、 **[プロビジョニング]** に移動します。

    ![[プロビジョニング] が強調表示された AWS アプリのスクリーンショット](./media/amazon-web-service-tutorial/provisioning.png)

2. アクセス キーとシークレットをそれぞれ **[クライアント シークレット]** フィールドと **[シークレット トークン]** フィールドに入力します。

    ![[管理者資格情報] ダイアログ ボックスのスクリーンショット](./media/amazon-web-service-tutorial/provisioning1.png)

    a. AWS ユーザーのアクセス キーを **[clientsecret]/(clientsecret/)** フィールドに入力します。

    b. AWS ユーザー シークレットを **[シークレット トークン]** フィールドに入力します。

    c. **[接続テスト]** を選択します。

    d. **[保存]** を選択して設定を保存します。

3. **[設定]** セクションの **[プロビジョニング状態]** で **[オン]** を選択します。 次に、 **[保存]** を選択します。

    ![[オン] が強調表示された [設定] セクションのスクリーンショット](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> プロビジョニング サービスは、AWS から Azure AD にロールをインポートするだけです。 Azure AD のユーザーとグループが、このサービスによって AWS にプロビジョニングされることはありません。

> [!NOTE]
> プロビジョニングの資格情報を保存したら、初回同期サイクルが実行されるまで待機する必要があります。 同期には通常、約 40 分かかります。 その状態は、 **[プロビジョニング]** ページ下部の **[現在の状態]** で確認できます。

### <a name="create-aws-single-account-access-test-user"></a>AWS Single-Account Access のテスト ユーザーの作成

このセクションの目的は、AWS Single-Account Access で B.Simon というユーザーを作成することです。 AWS Single-Account Access では、SSO 用にユーザーをシステムに作成する必要がないため、ここで操作を実行する必要はありません。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる AWS Single-Account Access のサインオン URL にリダイレクトされます。  

* AWS Single-Account Access のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した AWS Single-Account Access に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [AWS Single-Account Access] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した AWS Single-Account Access に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="known-issues"></a>既知の問題

* AWS Single-Account Access プロビジョニング統合は、AWS パブリック クラウド エンドポイントに接続する場合にのみ使用できます。 AWS Single-Account Access プロビジョニング統合を使用して AWS Government 環境にアクセスすることはできません。
 
* **[プロビジョニング]** セクションの **[マッピング]** サブセクションには、"読み込み中..." というメッセージが表示され、属性マッピングは表示されません。 現在サポートされている唯一のプロビジョニング ワークフローは、ユーザーまたはグループ割り当て時の選択のために、AWS から Azure AD にロールをインポートすることです。 このための属性マッピングは事前に決定されており、構成はできません。

* **[準備中]** セクションでは、1 つの AWS テナントに対して、一度に 1 セットの資格情報の入力だけがサポートされています。 インポートされたすべてのロールは、AWS テナントの Azure AD [`servicePrincipal` オブジェクト](/graph/api/resources/serviceprincipal)の `appRoles` プロパティに書き込まれます。

  プロビジョニングのために複数の AWS テナント (`servicePrincipals` によって表される) をギャラリーから Azure AD に追加できます。 ただし、プロビジョニングに使用される複数の AWS `servicePrincipals` からインポートされたすべてのロールを、SSO に使用される単一の `servicePrincipal` に自動的に書き込むことができないという既知の問題があります。

  回避策として、[Microsoft Graph API](/graph/api/resources/serviceprincipal) を使用して、プロビジョニングが構成されている各 AWS `servicePrincipal` にインポートされたすべての `appRoles` を抽出できます。 その後、これらのロール文字列を、SSO が構成されている AWS `servicePrincipal` に追加できます。

* AWS から Azure AD へのインポート対象となるロールは、次の要件を満たす必要があります。

  * ロールには、AWS で SAML プロバイダーが 1 つだけ定義されている必要があります。
  * ロールの ARN (Amazon Resource Name) と、関連付けられている SAML プロバイダーの ARN を組み合わせた長さが 240 文字未満である必要があります。

## <a name="change-log"></a>ログの変更

* 01/12/2020 - ロールの長さの上限が 119 文字から 239 文字に増加。 

## <a name="next-steps"></a>次の手順

AWS Single-Account Access を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。


[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png