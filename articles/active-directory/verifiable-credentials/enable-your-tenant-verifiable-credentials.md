---
title: チュートリアル - Azure Active Directory を構成して検証可能な資格情報 (プレビュー) を発行する
description: このチュートリアルでは、検証可能な資格情報を自分のテナントでデプロイするために必要な環境を構築します
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: cd39f6c484ebe116918611bb1d543c1919a3cb0a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222947"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>チュートリアル - Azure Active Directory を構成して検証可能な資格情報 (プレビュー) を発行する

このチュートリアルでは、[スタート ガイド](get-started-verifiable-credentials.md)記事の一環で行った作業を土台として、Azure Active Directory (Azure AD) とそれ独自の[分散化識別子](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID) を設定します。 サンプル アプリと発行者を使用し、分散化識別子を使って検証可能な資格情報を発行します。ただし、このチュートリアルでは依然としてサンプル Azure B2C テナントを認証に使用します。  次のチュートリアルでは、追加のステップとして、Azure AD と連携するようにアプリを構成します。

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事の内容:

> [!div class="checklist"]
> * 検証可能な資格情報に必要なサービスを作成して、Azure AD にオンボードする 
> * DID を作成する
> * ルールと表示のファイルをカスタマイズする
> * Azure AD で検証可能な資格情報を構成する。


## <a name="prerequisites"></a>[前提条件]

このチュートリアルを正常に完了するには、先に以下を実行する必要があります。

- [スタート ガイド](get-started-verifiable-credentials.md)を完了します。
- アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- P2 [ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)付きの Azure AD。 無料の開発者アカウントをお持ちでない場合は、[その作成方法](how-to-create-a-free-developer-account.md)に従ってください。
- 自分がキーとシークレットを作成する権限を持つ [Azure Key Vault](../../key-vault/general/overview.md) のインスタンス。

## <a name="azure-active-directory"></a>Azure Active Directory

開始する前に、Azure AD テナントを用意する必要があります。 テナントは検証可能な資格情報に対して有効になると、分散化識別子 (DID) に割り当てられます。また、検証可能な資格情報を発行するための発行者サービスが有効になります。 発行する検証可能な資格情報はどれも、自分のテナントとその DID によって発行されます。 DID は、検証可能な資格情報を検証する際も使用されます。
テスト Azure サブスクリプションを作成したばかりの場合、自分のテナントにユーザー アカウントを設定する必要はありませんが、後でチュートリアルを完了するために少なくとも 1 つのユーザー テスト アカウントを用意する必要があります。

## <a name="create-a-key-vault"></a>Key Vault の作成

検証可能な資格情報を操作するとき、検証可能な資格情報にデジタル署名するために自分のテナントで使用される暗号化キーを完全に制御および管理できます。 資格情報を発行して検証するには、自分の Azure Key Vault インスタンスへのアクセス権を Azure AD に付与する必要があります。

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。
2. 検索ボックスに「**key vault**」と入力します。
3. 結果の一覧の **[Key Vault]** を選択します。
4. Key Vault のセクションで、 **[作成]** を選択します。
5. **[キー コンテナーの作成]** セクションで、次の情報を入力します。
    - **サブスクリプション**:サブスクリプションを選択します。
    - **[リソース グループ]** で、 **[新規作成]** を選択し、リソース グループ名 (**vc-resource-group** など) を入力します。 ここでは複数の記事にわたって同じリソース グループ名を使用しています。
    - **Name**:一意の名前が必要です。 ここでは **woodgroup-vc-kv** を使用します。この値は、独自の一意の名前に置き換えてください。
    - **[場所]** プルダウン メニューで場所を選択します。
    - 他のオプションは既定値のままにしておきます。
6. 上記の情報を指定した後に、 **[アクセス ポリシー]** を選択します

    ![キー コンテナーの作成ページ](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. [アクセス ポリシー] 画面で、 **[アクセス ポリシーを追加します]** を選択します

    >[!NOTE]
    > 既定では、キー コンテナーを作成したアカウントのみにアクセス権があります。 検証可能な資格情報サービスはキー コンテナーにアクセスする必要があります。 キー コンテナーのアクセス ポリシーで、**キーの作成**、オプトアウト時に **キーを削除** する能力、検証可能な資格情報のドメイン バインドを作成するための **署名** が、管理者に許可されている必要があります。 テスト中に同じアカウントを使用している場合は、コンテナーの作成者に既定で付与されるアクセス許可のほかに、そのアカウントに **署名** を許可するように既定のポリシーを変更してください。

8. ユーザー管理者の場合は、[キーのアクセス許可] セクションで **[作成]** 、 **[削除]** 、 **[署名]** が有効になっていることを確認します。 既定では [作成] と [削除] が元から有効になっているので、[キーのアクセス許可] で更新が必要なのは [署名] のみです。 

    ![キー コンテナーのアクセス許可](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. **[確認および作成]** を選択します。
10. **[作成]** を選択します。
11. コンテナーに移動して、コンテナー名と URI をメモします

次の 2 つのプロパティをメモしておきます。

- **コンテナー名**: 例の値の名前は **woodgrove-vc-kv** です。 この名前は他の手順で使用します。
- **Vault URI (コンテナー URI)** : 例では、この値は https://woodgrove-vc-kv.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

>[!NOTE]
> キー コンテナーの各トランザクションでは、追加の Azure サブスクリプション コストが発生します。 詳細については、[「Key Vault の価格」ページ](https://azure.microsoft.com/pricing/details/key-vault/)を確認してください。

>[!IMPORTANT]
> Azure Active Directory Verifiable Credentials のプレビュー期間中は、コンテナー内に作成したキーとシークレットを作成後に変更すべきではありません。 キーとシークレットを削除、無効化、または更新すると、発行された資格情報は無効になります。 プレビュー期間中はキーまたはシークレットを変更しないようにしてください。

## <a name="create-a-modified-rules-and-display-file"></a>変更されたルールおよび表示ファイルの作成

このセクションでは、自分のテナントで最初の検証可能な資格情報を作成するために、サンプル発行者アプリでルールと表示のファイルを使用してそれらをわずかに変更します。

1. ルールと表示、両方の json ファイルを一時フォルダーにコピーし、それらの名前をそれぞれ **MyFirstVC-display.json** と **MyFirstVC-rules.json** に変更します。 どちらのファイルも **issuer\issuer_config** にあります

   ![サンプル アプリのディレクトリに含まれている表示とルールのファイル](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![一時フォルダー内の表示とルールのファイル](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. コード エディターで MyFirstVC-rules.json ファイルを開きます。 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

次に、type フィールドを "MyFirstVC" に変更します。 

  ```json
   "type": ["MyFirstVC"]
  
  ```

その変更を保存します。

 >[!NOTE]
   > チュートリアルのこの時点では、 **"configuration"** または **"client_id"** を変更しません。 [スタート ガイド](get-started-verifiable-credentials.md)で使用した Microsoft B2C テナントをここでも使用します。 次のチュートリアルでは自分の Azure AD を使用します。

3. コード エディターで MyFirstVC-display.json ファイルを開きます。

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

この検証可能な資格情報がサンプル コードのバージョンとは明らかに異なって見えるように、いくつかの変更を加えます。 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

これらの変更を保存します。
## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

最初の検証可能な資格情報を作成した後、構成およびルール ファイルを保持できる BLOB ストレージ コンテナーを作成する必要があります。

1. 下記のオプションを使用してストレージ アカウントを作成します。 詳細な手順については、記事「[ストレージ アカウントを作成する](../../storage/common/storage-account-create.md?tabs=azure-portal)」をご覧ください。

   - **サブスクリプション:** これらのチュートリアルに使用しているサブスクリプションを選択します。
   - **リソース グループ:** 前のチュートリアルで使用したのと同じリソース グループを選択します (**vc-resource-group**)。
   - **名前:** 一意の名前。
   - **場所:** (米国) 米国東部。
   - **パフォーマンス:** 標準。
   - **アカウントの種類:** StorageV2。
   - **レプリケーション:** ローカル冗長。
 
   ![新しいストレージ アカウントの作成](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. ストレージ アカウントの作成後、コンテナーを作成する必要があります。 **[Blob ストレージ]** で **[コンテナー]** を選択し、以下の値を使用してコンテナーを作成します。

    - **名前:** vc-container
    - **パブリック アクセス レベル:** プライベート (匿名アクセスなし)

   ![コンテナーを作成する](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. 次に、新しいコンテナーを選択して、前に作成した新しいルールおよび表示ファイル (**MyFirstVC-display.json** および **MyFirstVC-rules.json**) をどちらもアップロードします。

   ![ルール ファイルのアップロード](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>BLOB ロールの割り当て

資格情報を作成する前に、サインインしたユーザーがストレージ BLOB 内のファイルにアクセスできるように、適切なロール割り当てを付与しておく必要があります。

1. **[ストレージ]**  >  **[コンテナー]** に移動します。
2. 左側にあるメニューから **[アクセス制御 (IAM)]** を選択します。
3. **[ロールの割り当て]** を選択します。
4. **[追加]** を選択します。
5. **[ロール]** セクションで、 **[ストレージ BLOB データ閲覧者]** を選択します。
6. **[アクセスの割り当て先]** で、 **[ユーザー、グループ、またはサービス プリンシパル]** を選択します。
7. **[選択]** で、これらの手順を実行するために使用しているアカウントを選択します。
8. **[保存]** を選択してロールの割り当てを完了します。


   ![ロールの割り当てを追加する](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >既定では、コンテナーの作成者には **所有者** ロールが割り当てられます。 **所有者** ロールのままでは不十分です。 アカウントには **ストレージ BLOB データ閲覧者** ロールが必要です。 詳細については、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../../storage/common/storage-auth-aad-rbac-portal.md)」を確認してください

## <a name="set-up-verifiable-credentials-preview"></a>検証可能な資格情報 (プレビュー) の設定

次に、最後の手順として、検証可能な資格情報用にテナントを設定します。

1. Azure portal で、**検証可能な資格情報** を検索します。
2. **[検証可能な資格情報 (プレビュー)]** を選択します。
3. **[作業の開始]** を選択します
4. 自分の組織を設定し、組織名、ドメイン、キー コンテナーを指定する必要があります。 値を 1 つずつ見てみましょう。

      - **組織名**: この名前は、Verifiable Credential サービス内で自分のビジネスを参照するために使用されます。 この値は顧客には公開されません。
      - **ドメイン:** 入力したドメインは、DID ドキュメントにサービス エンドポイントとして追加されます。 [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) とその他のウォレットでは、DID が[ドメインにリンク](how-to-dnsbind.md)されていることを検証するために、この情報を使用します。 DID を検証できる場合、ウォレットには検証済み記号が表示されます。 ウォレットが DID を検証できない場合、検証できない資格情報が組織によって発行されたことがユーザーに通知されます。 ドメインは、ユーザーがお客様のビジネスについて知っている具体的な何かに DID をバインドするものです。
      - **キー コンテナー:** 前に作成したキー コンテナーの名前を指定します。
 
   >[!IMPORTANT]
   > ドメインはリダイレクトしないようにします。そうしないと、DID とドメインはリンクできません。 https://www.domain.com という形式を使用するようにしてください。
    
5. **[Save and create credential]\(保存して資格情報を作成\)** を選択します

    ![自分の組織 ID の設定](media/enable-your-tenant-verifiable-credentials/save-create.png)

おめでとうございます。これで、検証可能な資格情報 (プレビュー) がテナントで有効になりました。

## <a name="create-your-vc-in-the-portal"></a>ポータルでの VC の作成

前の手順で、 **[Create a new credential]\(新しい資格情報の作成\)** ページに留まっています。 

   ![検証可能な資格情報の作成開始](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. [資格情報名] に、「**MyFirstVC**」という名前を入力します。 この名前は検証可能な資格情報を識別するためにポータルで使用されます。これは、検証可能な資格情報のコントラクトの一部として含まれます。
2. [Display file]\(表示ファイル\) セクションで、 **[Configure display file]\(表示ファイルの構成\)** を選択します
3. **[ストレージ アカウント]** セクションで、**woodgrovevcstorage** を選択します。
4. 使用可能なコンテナーの一覧から、**vc-container** を選択します。
5. 前に作成した **MyFirstVC-display.json** ファイルを選択します。
6. **[Create a new credential]\(新しい資格情報の作成\)** の **[Rules file]\(ルール ファイル\)** セクションで、 **[Configure rules file]\(ルール ファイルの構成\)** を選択します
7. **[ストレージ アカウント]** セクションで、**woodgrovecstorage** を選択します
8. **vc-container** を選択します。
9. **MyFirstVC-rules.json** を選択します
10. **[Create a new credential]\(新しい資格情報の作成\)** 画面で、 **[作成]** を選択します。

   ![新しい資格情報の作成](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>資格情報 URL

新しい資格情報ができたところで、資格情報 URL をコピーします。

   ![資格情報 URL の発行](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>資格情報 URL はルールと表示のファイルを組み合わせたものです。 これは、検証可能な資格情報の発行要件がユーザーに表示される前に Authenticator によって評価される URL です。  

## <a name="update-the-sample-app"></a>サンプル アプリの更新

次に、サンプル アプリの発行者コードに変更を加えて、検証可能な資格情報の URL でそれを更新します。 これにより、独自のテナントを使用して、検証可能な資格情報を発行できます。

1. サンプル アプリのファイルを置き換えるフォルダーに移動します。
2. 発行者フォルダーを開いて、Visual Studio Code で app.js を開きます。
3. 定数 "credential" を新しい資格情報 URL で更新し、credentialType 定数を "MyFirstVC" に設定して、ファイルを保存します。

    ![関連領域が強調表示された Visual Studio Code の画像](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. コマンド プロンプトを開いて、発行者フォルダーを開きます。
5. 更新されたノード アプリを実行します。

    ```terminal
    node app.js
    ```

6. 別のコマンド プロンプトを使用して ngrok を実行し、URL を 8081 で設定します

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > [このアプリまたは Web サイトは危険であるおそれがあります] という警告にお気づきになるかもしれません。 ここでメッセージの表示が予想されるのは、自分の DID をドメインにまだリンクしていないためです。 [DNS バインド](how-to-dnsbind.md)の手順に従って、それを構成してください。

    
7. ngrok によって生成された HTTPS URL を開きます。

    ![NGROK 転送エンドポイント](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. **[GET CREDENTIAL]\(資格情報の取得\)** を選択します
9. Authenticator で、QR コードをスキャンします。
10. **[このアプリまたは Web サイトは危険であるおそれがあります]** という警告メッセージで、 **[詳細設定]** を選択します。

  ![最初の警告](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. 危険な Web サイトの警告で、 **[このまま続行 (安全ではありません)]** を選択します

  ![発行者についての 2 つ目の警告](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. **[資格情報の追加]** 画面で、いくつかの点に注目します。 
    1. 画面の上部に、赤い **[未検証]** メッセージが見えます
    1. 資格情報は、表示ファイルに加えた変更に基づいてカスタマイズされています。
    1. **[アカウントにサインインする]** オプションでは、**didplayground.b2clogin.com** が参照されています。
    
   ![警告が示されている資格情報の追加画面](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. **[アカウントにサインインする]** を選択し、[スタート ガイドのチュートリアル](get-started-verifiable-credentials.md)で指定した資格情報を使用して認証します。
14. 認証が成功した後、 **[追加]** ボタンの灰色表示が解除されます。 **[追加]** を選択します。

  ![認証後の資格情報の追加画面](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

これで、認証には B2C テナントを使用したまま、検証可能な資格情報の生成に自分たちのテナントを使用して VC を発行することができました。

  ![自分の Azure AD によって発行され、Azure B2C インスタンスによって認証された VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>サンプル アプリを使用した VC の検証のテスト

独自のテナントから検証可能な資格情報を発行できたところで、サンプル アプリを使用してそれを検証してみましょう。

>[!IMPORTANT]
> テスト時には、[スタート ガイド](get-started-verifiable-credentials.md)記事で使用したのと同じメールとパスワードを使用します。 VC を発行しているのは自分のテナントですが、入力は、Microsoft B2C テナントによって発行された ID トークンから送られてきています。 チュートリアル 2 では、トークンの発行元を自分のテナントに切り替えます

1. Azure portal の検証可能な資格情報ブレードで、 **[設定]** を開きます。 分散化識別子 (DID) をコピーします。

   ![DID のコピー](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. 次に、サンプル アプリ ファイルの検証者フォルダー部分を開きます。 検証者のサンプル コードで app.js ファイルを更新し、次の変更を行う必要があります。

    - **credential**: 実際の資格情報 URL に変更します
    - **credentialType**: 'MyFirstVC'
    - **issuerDid**: Azure portal で [検証可能な資格情報 (プレビュー)]、[設定]、[Decentralized identifier (DID)]\(分散化識別子 (DID)\) の順に移動し、この値をコピーします
    
   ![自分の発行者識別子に一致するように定数 issuerDid を更新](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. ngrok 発行者サービスの実行を停止します。

    ```cmd
    control-c
    ```

4. 次に、検証者のポートを 8082 にして ngrok を実行します。

    ```cmd
    ngrok http 8082
    ```

5. 次のターミナル ウィンドウで、検証者アプリに移動して、発行者アプリを実行したのと同様の方法で、それを実行します。

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. ブラウザーで ngrok URL を開き、モバイル デバイスで Authenticator を使用して QR コードをスキャンします。
7. 自分のモバイル デバイスで、 **[新しいアクセス許可の要求]** 画面の **[許可]** を選択します。

    >[!NOTE]
    > この VC に署名している DID は、まだ Microsoft B2C です。 検証者の DID は、依然として Microsoft のサンプル アプリ テナントのものです。 Microsoft の DID は自分が所有するドメインにリンクされているため、発行フロー中に出たような警告は表示されません。 これは次のセクションで更新されます。
    
   ![新しいアクセス許可の要求](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. 正常に検証される自分の資格情報はありません。

## <a name="next-steps"></a>次のステップ

サンプル コードで自分の発行者から VC を発行できるようになったところで、次のセクションに進みましょう。そこでは、検証可能な資格情報を取得しようとするユーザーの認証に独自の ID プロバイダーを使用し、自分の DID を使って表示要求を署名します。

> [!div class="nextstepaction"]
> [チュートリアル - 自分のテナントを使用して検証可能な資格情報を発行して検証する](issue-verify-verifiable-credentials-your-tenant.md)


