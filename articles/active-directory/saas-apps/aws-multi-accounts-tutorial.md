---
title: 'チュートリアル: Azure Active Directory とアマゾン ウェブ サービスを統合して複数のアカウントを接続する | Microsoft Docs'
description: Azure AD とアマゾン ウェブ サービスの間でシングル サインオンを構成する方法について説明します (従来のチュートリアル)。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384114"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>チュートリアル: Azure Active Directory とアマゾン ウェブ サービスの統合

このチュートリアルでは、Azure Active Directory (Azure AD) とアマゾン ウェブ サービス (AWS) を統合する方法について説明します (従来のチュートリアル)。

この統合には次のような利点があります。

- AWS にアクセスできるユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントでシングル サインオン (SSO) を使用して自動的に AWS にサインインできるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

![Azure AD と AWS の統合を示す図。](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> 1 つの AWS アプリを自分のすべての AWS アカウントに接続 "*しない*" ことをお勧めします。 代わりに、[Azure AD SSO と AWS の統合](./amazon-web-service-tutorial.md)を使用して、Azure AD で AWS アプリの複数のインスタンスに対して AWS アカウントの複数のインスタンスを構成することをお勧めします。 

以下の理由から、1 つの AWS アプリを自分のすべての AWS アカウントに接続 "*しない*" ことをお勧めします。

* AWS アカウントやそれに含まれるロールの数が増えてもこのモデルを拡張できないことが原因で、AWS アカウントとロールの数を少なくしている場合にのみ、このアプローチを使用してください。 このアプローチでは、Azure AD ユーザー プロビジョニングを使用した AWS ロールのインポート機能が使用されないため、ロールを手動で追加、更新、削除する必要があります。 

* アプリにすべてのロールを適用するには、Microsoft Graph エクスプローラーを使用する必要があります。 マニフェスト ファイル アプローチの使用はお勧めしません。

* お客様からは、1 つの AWS アプリに対して約 1,200 のアプリ ロールを追加した後、そのアプリでさらに操作を行うと、サイズに関連したエラーがスローされるようになるという報告が寄せられています。 アプリケーション オブジェクトには厳密なサイズ制限があります。

* ロールは、アカウントのいずれかに追加されたときに、手動で更新する必要があります。 残念ながら、これは "*追加*" アプローチではなく、"*置換*" アプローチとなります。 また、アカウント数が増加している場合、これはアカウントとロールの *n* &times; *n* の関係になります。

* すべての AWS アカウントでは、同じフェデレーション メタデータ XML ファイルが使用されます。 証明書をロールオーバーする際、すべての AWS アカウントの証明書を同時に更新することは膨大な作業となる可能性があります。

## <a name="prerequisites"></a>前提条件

AWS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションがない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。
* AWS での SSO が有効なサブスクリプション。

> [!NOTE]
> 必要な場合を除き、このチュートリアルの手順を運用環境でテストしないことをお勧めします。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

AWS では、SP Initiated SSO と IDP-initiated SSO がサポートされます。

## <a name="add-aws-from-the-gallery"></a>ギャラリーから AWS を追加する

Azure AD への AWS の統合を構成するには、ギャラリーからマネージド SaaS (サービスとしてのソフトウェア) アプリの一覧に AWS を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、使用する Azure AD サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**アマゾン ウェブ サービス**」と入力します。
1. 結果リストで **[アマゾン ウェブ サービス]** を選択し、そのアプリを追加します。 数秒で、お使いのテナントにアプリが追加されます。

1. **[プロパティ]** ペインに移動し、 **[オブジェクト ID]** ボックスに表示されている値をコピーします。

    ![[プロパティ] ペインの [オブジェクト ID] ボックスのスクリーンショット。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、AWS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する AWS ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと AWS の同じユーザーの間で、リンク関係が確立されている必要があります。

AWS で、Azure AD の **[ユーザー名]** の値を AWS の **[Username]\(ユーザー名\)** の値として割り当てて、リンク関係を確立します。

AWS で Azure AD シングル サインオンを構成してテストするには、次を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
1. **[AWS SSO の構成](#configure-aws-sso)** - アプリケーション側で SSO 設定を構成します。
1. **[SSO をテスト](#test-sso)** して、構成が正しく機能することを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、次の手順を実行して Azure portal で Azure AD SSO を有効にし、AWS アプリケーションに SSO を構成します。

1. Azure portal の **アマゾン ウェブ サービス (AWS)** アプリケーション統合ページの左側ペインで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] コマンドのスクリーンショット。](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![[シングル サインオン方式の選択] ペインのスクリーンショット。](common/select-saml-option.png)

1. **[SAML によるシングル サインオンのセットアップ]** ペインで **[編集]** ボタン (鉛筆アイコン) を選択します。

    ![[SAML によるシングル サインオンのセットアップ] ペインの [編集] ボタンのスクリーンショット。](common/edit-urls.png)

1. **[基本的な SAML 構成]** ペインが開きます。 アプリはあらかじめ Azure と統合されているため、このセクションはスキップします。 **[保存]** を選択します。

   AWS アプリケーションは、特定の形式の SAML アサーションを受け入れます。 これらの属性の値は、 **[アプリケーション統合]** ページの **[ユーザー属性と要求]** セクションで管理できます。 
   
1. **[SAML によるシングル サインオンのセットアップ]** ペインで **[編集]** ボタンを選択します。

    ![[ユーザー属性] ペインの [編集] ボタンのスクリーンショット。](common/edit-attribute.png)

1. **[ユーザー属性]** ペインの **[ユーザー要求]** セクションで、次の表の値を使用して、SAML トークン属性を構成します。

    | 名前  | ソース属性  | 名前空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Role | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "900 秒 (15 分) から 43,200 秒 (12 時間) までの値を指定します" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. **[新しい要求の追加]** を選択した後、 **[ユーザー要求の管理]** ペインで、以下を実行します。

   ![[ユーザー要求] ペインの [新しい要求の追加] および [保存] ボタンのスクリーンショット。](common/new-save-attribute.png)

   ![[ユーザー要求の管理] ペインのスクリーンショット。](common/new-attribute-details.png)

   b. **[名前]** ボックスに、属性名を入力します。  

   c. **[名前空間]** ボックスに、名前空間の値を入力します。

   d. **[ソース]** で **[属性]** を選択します。

   e. **[ソース属性]** ボックスの一覧から、属性を選択します。

   f. **[OK]** を選択し、 **[保存]** を選択します。

   >[!NOTE]
   >Azure AD のロールの詳細については、[アプリケーションへのアプリ ロールの追加とトークンでの受け取り](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)に関する記事を参照してください。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択してフェデレーション メタデータ XML ファイルをダウンロードし、コンピューターに保存します。

   ![[SAML 署名証明書] ペインの [フェデレーション メタデータ XML] ダウンロード リンクのスクリーンショット。](common/metadataxml.png)

### <a name="configure-aws-sso"></a>AWS SSO の構成

1. 新しいブラウザー ウィンドウで、管理者として自分の AWS 企業サイトにサインインします。

1. **AWS ホーム** アイコンを選択します。

   !["AWS ホーム" アイコンのスクリーンショット。][11]

1. **[AWS services]\(AWS サービス\)** ペインの **[Security, Identity & Compliance]\(セキュリティ、ID、コンプライアンス\)** で、 **[IAM]** (ID およびアクセス管理) を選択します。

    ![[AWS services]\(AWS サービス\) ペインの [IAM] リンクのスクリーンショット。][12]

1. 左側のペインで **[Identity Providers]\(ID プロバイダー\)** を選択してから、 **[Create Provider]\(プロバイダーの作成\)** を選択します。

    ![[Create Provider]\(プロバイダーの作成\) ボタンのスクリーンショット。][13]

1. **[Configure Provider]\(プロバイダーの設定\)** ペインで、以下を実行します。

    ![[Configure Provider]\(プロバイダーの設定\) ペインのスクリーンショット。][14]

    a. **[Provider Type]\(プロバイダーの種類\)** ボックスの一覧から **[SAML]** を選択します。

    b. **[Provider Name]\(プロバイダー名\)** ボックスにプロバイダー名を入力します (例: *WAAD*)。

    c. **[Metadata Document]\(メタデータ ドキュメント\)** ボックスの横にある **[Choose File]\(ファイルの選択\)** を選択して、Azure portal にダウンロードしたフェデレーション メタデータ XML ファイルをアップロードします。

    d. **[Next Step]\(次のステップ\)** を選択します。

1. **[Verify Provider Information]\(プロバイダー情報の検証\)** ペインで **[Create]\(作成\)** を選択します。

    ![[Verify Provider Information]\(プロバイダー情報の検証\) ペインのスクリーンショット。][15]

1. 左側のペインで、 **[Roles]\(ロール\)** を選択してから、 **[Create role]\(ロールの作成\)** を選択します。

    ![[Roles]\(ロール\) ペインの [Create role]\(ロールの作成\) ボタンのスクリーンショット。][16]

    > [!NOTE]
    > そのロール Amazon Resource Name (ARN) とインポートされるロールの SAML プロバイダー ARN を合わせた長さが 240 文字以下になるようにしてください。

1. **[Create role]\(ロールの作成\)** ページで、以下を実行します。  

    ![[Create role]\(ロールの作成\) ページの信頼されたエンティティ [SAML 2.0 federation]\(SAML 2.0 フェデレーション\) ボタンのスクリーンショット。][19]

    a. **[信頼されたエンティティの種類を選択]** で、 **[SAML 2.0 フェデレーション]** を選択します。

    b. **[Choose a SAML 2.0 provider]\(SAML 2.0 プロバイダーを選択\)** で、先ほど作成した SAML プロバイダーを選択します (例: *WAAD*)。

    c. **[Allow programmatic and AWS Management Console access]** を選択します。

    d. **Permissions\(次へ: アクセス許可\)** をクリックします。

1. 検索ボックスに「**Administrator Access**」と入力し、 **[AdministratorAccess]** チェック ボックスをオンにして、 **[Next: Tags]\(次へ: タグ\)** を選択します。

    ![AdministratorAccess ポリシーが選択されている [Policy name]\(ポリシー名\) 一覧のスクリーンショット。](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. **[Add tags (optional)]\(タグの追加 (省略可能)\)** ペインで、以下を実行します。

    ![[Add tags (optional)]\(タグの追加 (省略可能)\) ペインのスクリーンショット。](./media/aws-multi-accounts-tutorial/config2.png)

    a. **[Key]\(キー\)** ボックスに、キー名を入力します (例: *Azureadtest*)。

    b. **[Value (optional)]\(値 (省略可能)\)** ボックスに、`<accountname-aws-admin>` の形式でキー値を入力します。 アカウント名はすべて小文字にする必要があります。

    c. **確認\)** をクリックします。

1. **[Review]\(確認\)** ペインで、以下を実行します。

    ![[Role name]\(ロール名\) および [Role description]\(ロールの説明\) ボックスが強調表示されている [Review]\(確認\) ペインのスクリーンショット。][34]

    a. **[Role name]\(ロール名\)** ボックスに、`<accountname-aws-admin>` の形式で値を入力します。

    b. **[Role description]\(ロールの説明\)** ボックスに、ロール名に使用した値を入力します。

    c. **[ロールの作成]** を選択します。

    d. 必要な数のロールを作成し、それらを ID プロバイダーにマップします。

    > [!NOTE]
    > 同様に、それぞれ異なるポリシーが関連付けられている、*accountname-finance-admin*、*accountname-read-only-user*、*accountname-devops-user*、*accountname-tpm-user* など、その他のロールも作成できます。 各 AWS アカウントの要件に応じて、これらのロール ポリシーは後で変更することができます。 AWS アカウント全体で、ロールごとに同じポリシーを維持することをお勧めします。

1. Amazon Elastic Compute Cloud (Amazon EC2) のプロパティ ペインまたは IAM ダッシュボード (次のスクリーンショットを参照) から、AWS アカウントのアカウント ID を必ずメモしておきます。

    ![[Identity and Access Management]\(ID 管理とアクセス管理\) ペインでアカウント ID が表示される場所を示すスクリーンショット。](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Azure portal にサインインし、 **[グループ]** に移動します。

1. 前に作成した IAM ロールと同じ名前で新しいグループを作成し、この各新しいグループの **[オブジェクト ID]** の値をメモします。

    ![新しいグループのアカウントの詳細のスクリーンショット。](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. 現在の AWS アカウントからサインアウトし、Azure AD による SSO を構成する別のアカウントにログインします。

1. そのアカウントに作成したすべてのロールは、これらのアカウントの **[Roles]\(ロール\)** の一覧に表示されます。

    ![各ロールの名前、説明、および信頼されたエンティティが表示されたロールの一覧のスクリーンショット。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

次に、すべてのアカウントで、すべてのロールに関するロール ARN と信頼されたエンティティをすべて取得する必要があります。 これらを手動で Azure AD アプリケーションにマップする必要があります。 そのためには次を行います。

1. 各ロールを選択して、そのロール ARN と信頼されたエンティティの値をコピーします。 これらは、Azure AD で作成するすべてのロールに必要になります。

    ![ロール ARN と信頼されたエンティティの [Summary]\(概要\) ペインのスクリーンショット。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. すべてのアカウントのすべてのロールに対して前の手順を繰り返し、これらを `<Role ARN>,<Trusted entities>` の形式でテキスト ファイルに保存します。

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開き、以下を実行します。

    a. テナントのグローバル管理者または共同管理者の資格情報を使用して、Microsoft Graph Explorer サイトにサインインします。

    b. ロールを作成するための十分なアクセス許可が必要です。 **[アクセス許可の変更]** を選択します。

      ![Microsoft Graph Explorer の [認証] ペインの [アクセス許可の変更] リンクのスクリーンショット。](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. アクセス許可の一覧に、次のスクリーンショットに表示されているアクセス許可がまだない場合は、それぞれ選択した後、 **[アクセス許可の変更]** を選択します。 

      ![該当するアクセス許可が強調表示されている、Microsoft Graph Explorer のアクセス許可の一覧のスクリーンショット。](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. 再度 Graph Explorer にサインインし、サイトの使用条件に同意します。 

    e. ペインの上部で、メソッドとして **[GET]** を、バージョンとして **[ベータ]** を選択し、クエリ ボックスに次のいずれかを入力します。 
    
    * テナントからすべてのサービス プリンシパルを取得するには、`https://graph.microsoft.com/beta/servicePrincipals` を使用します。 
    * 複数のディレクトリを使用している場合は、`https://graph.microsoft.com/beta/contoso.com/servicePrincipals` を使用します。これには、プライマリ ドメインが含まれます。

    ![Microsoft Graph Explorer のクエリの [要求本文] ペインのスクリーンショット。](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. サービス プリンシパルの一覧から、変更する必要があるものを取得します。 
    
    Ctrl + F キーを選択して、表示されているすべてのサービス プリンシパルについてアプリケーションを検索することもできます。 特定のサービス プリンシパルを取得するには、次に示すように、Azure AD プロパティ ペインから前にコピーしたサービス プリンシパル オブジェクト ID をクエリに含めます。

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![オブジェクト ID を含むサービス プリンシパル クエリのスクリーンショット。](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. サービス プリンシパル オブジェクトから appRoles プロパティを抽出します。

    ![サービス プリンシパル オブジェクトから appRoles プロパティを抽出するコードのスクリーンショット。](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. ここで、アプリケーションの新しいロールを生成する必要があります。 

    i. 次の JSON コードは、appRoles オブジェクトの例です。 同様のオブジェクトを作成して、アプリケーションに必要なロールを追加します。

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > パッチ操作の場合、新しいロールを追加できるのは、*msiam_access* を追加した後のみです。 また、組織のニーズに応じて、必要な数のロールを追加することもできます。 Azure AD では、SAML 応答の要求値として、これらのロールの "*値*" を送信します。

    j. Microsoft Graph Explorer で、メソッドを **GET** から **PATCH** に変更します。 前の例に示されているように appRoles プロパティを更新して、必要なロールが割り当てられるようにサービス プリンシパル オブジェクトを修正します。 **[クエリの実行]** をクリックして、パッチ操作を実行します。 成功メッセージによって、AWS アプリケーションのロールの作成を確認できます。

      ![メソッドが PATCH に変更された Microsoft Graph Explorer ペインのスクリーンショット。](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. サービス プリンシパルでさらに多くのロールを修正したら、対応するロールにユーザーおよびグループを割り当てることができます。 これは、Azure portal で AWS アプリケーションに移動し、上部にある **[ユーザーとグループ]** タブを選択することによって行います。

1. グループで特定のロールを割り当てることができるように、すべての AWS ロール用に新しいグループを作成することをお勧めします。 この 1 対 1 のマッピングは、1 つのグループが 1 つのロールに割り当てられることを意味します。 そのグループに属するメンバーを追加できます。

1. グループを作成したら、グループを選択してアプリケーションに割り当てます。

    ![[ユーザーとグループ] ペインのスクリーンショット。](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > グループを割り当てるときに、入れ子になったグループはサポートされていません。

1. グループにロールを割り当てるには、ロールを選択し、 **[割り当て]** を選択します。

    ![[割り当ての追加] ペインのスクリーンショット。](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > ロールを割り当てたら、Azure portal セッションを最新の情報に更新することによって、ロールを表示できます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、Microsoft マイ アプリを使用して、Azure AD のシングル サインオン構成をテストします。

マイ アプリで **[AWS]** タイルを選択すると、AWS アプリケーション ページが開き、ロールを選択するオプションが表示されます。

![SSO をテストする AWS ページのスクリーンショット。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

SAML 応答を確認し、クレームとして渡されるロールを参照することもできます。

![SAML 応答のスクリーンショット。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

マイ アプリの詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

AWS を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 詳細については、[Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)に関するページを参照してください。

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
