---
title: チュートリアル - Azure Active Directory を構成して検証可能な資格情報 (プレビュー) を発行する
description: このチュートリアルでは、検証可能な資格情報を自分のテナント内でデプロイするために必要な環境を構築します。
documentationCenter: ''
author: barclayn
manager: daveba
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 06/24/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 9867825794c363b9df53849ad3fd1cb644492677
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816281"
---
# <a name="tutorial---configure-azure-active-directory-to-issue-verifiable-credentials-preview"></a>チュートリアル - Azure Active Directory を構成して検証可能な資格情報 (プレビュー) を発行する

このチュートリアルでは、[作業の開始](get-started-verifiable-credentials.md)に関する記事の一環で行った作業を土台として、Azure Active Directory (Azure AD) とそれ独自の[分散化識別子](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID) を設定します。 サンプル アプリと発行者を使用し、DID を使って検証可能な資格情報を発行します。 このチュートリアルでは、引き続き認証にサンプル Azure B2C テナントを使用します。 次のチュートリアルでは、Azure AD と連携するようにアプリを構成するための手順を実行します。

> [!IMPORTANT]
> Azure Active Directory の検証可能な資格情報は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、次の内容について説明します。

> [!div class="checklist"]
> * 検証可能な資格情報に必要なサービスを作成して、Azure AD にオンボードする。
> * DID を作成する。
> * ルールと表示のファイルをカスタマイズする。
> * Azure AD で検証可能な資格情報を構成する。

## <a name="prerequisites"></a>[前提条件]

このチュートリアルを正常に完了するには、先に以下を実行する必要があります。

- [作業の開始](get-started-verifiable-credentials.md)に関するチュートリアルの手順を完了します。
- アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure AD の P2 [ライセンス](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)を用意します。 お持ちでない場合は、[無料の開発者アカウントの作成](how-to-create-a-free-developer-account.md)に関する記事の手順に従ってください。
- 自分がキーとシークレットを作成する権限を持つ [Azure Key Vault](../../key-vault/general/overview.md) のインスタンスを用意します。

## <a name="azure-active-directory"></a>Azure Active Directory

開始する前に、Azure AD テナントが必要です。 自分のテナントで検証可能な資格情報が有効になっている場合、DID が割り当てられます。 また、検証可能な資格情報を発行するための発行者サービスも有効になります。 発行する検証可能な資格情報はどれも、自分のテナントとその DID によって発行されます。 DID は、検証可能な資格情報を検証する際にも使用されます。

テスト Azure サブスクリプションを作成したばかりの場合、自分のテナントにユーザー アカウントを設定する必要はありません。 後のチュートリアルを完了するには、少なくとも 1 つのユーザー テスト アカウントが必要です。

## <a name="create-a-key-vault"></a>Key Vault を作成します

検証可能な資格情報を利用する場合、検証可能な資格情報にデジタル署名するために自分のテナントで使用される暗号化キーを完全に制御および管理できます。 資格情報を発行して検証するには、自分の Key Vault インスタンスへのアクセス権を Azure AD に付与する必要があります。

1. Azure portal またはホーム ページで、 **[リソースの作成]** を選択します。
1. 検索ボックスに「**key vault**」と入力します。
1. 結果の一覧から **[Key Vault]** を選択します。
1. **[Key Vault]** セクションで **[作成]** を選択します。
1. **[キー コンテナーの作成]** セクションで、次の情報を入力します。
    - **サブスクリプション**: サブスクリプションを選択します。
    - **リソース グループ**: **[新規作成]** を選択し、リソース グループ名 (**vc-resource-group** など) を入力します。 ここでは複数の記事にわたって同じリソース グループ名を使用します。
    - **Name**:一意の名前が必要です。 ここでは **woodgroup-vc-kv** を使用します。この値は、独自の一意の名前に置き換えてください。
    - **[場所]** :場所を選択します。
    - 他のオプションは既定値の設定のままにしておきます。
1. 情報を指定した後に、 **[アクセス ポリシー]** を選択します。

    ![[キー コンテナーの作成] 画面を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

1. **[アクセス ポリシー]** 画面で、 **[アクセス ポリシーの追加]** を選択します。

    >[!NOTE]
    > 既定では、キー コンテナーを作成したアカウントのみにアクセス権があります。 検証可能な資格情報サービスはキー コンテナーにアクセスする必要があります。 キー コンテナーのアクセス ポリシーで、キーを作成すること、オプトアウト時にキーを削除できること、検証可能な資格情報のドメイン バインドを作成する目的で署名するためのアクセス許可を持つことが管理者に許可されている必要があります。 テスト中に同じアカウントを使用する場合は、コンテナーの作成者に既定で付与されるアクセス許可のほかに、**署名** するためのアクセス許可をそのアカウントに付与するように既定のポリシーを変更してください。

1. ユーザー管理者の場合は、[キーのアクセス許可] セクションで **[作成]** 、 **[削除]** 、 **[署名]** が有効になっていることを確認します。 既定では、 **[作成]** と **[削除]** は既に有効になっています。 更新する必要があるキーのアクセス許可は、 **[署名]** のみです。

    ![キー コンテナーのアクセス許可を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **[作成]** を選択します。
1. コンテナーに移動し、コンテナー名と URI をメモします。

次の 2 つのプロパティをメモします。

- **コンテナー名**: この例では、キー コンテナー名は **woodgrove-vc-kv** です。 この名前は他の手順で使用します。
- **Vault URI (コンテナー URI)** : 例では、この値は https://woodgrove-vc-kv.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

>[!NOTE]
> キー コンテナーの各トランザクションでは、追加の Azure サブスクリプション コストが発生します。 詳細については、 [Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

>[!IMPORTANT]
> Azure Active Directory の検証可能な資格情報のプレビュー期間中は、コンテナー内に作成したキーとシークレットを作成後に変更しないでください。 キーとシークレットを削除、無効化、または更新すると、発行された資格情報は無効になります。

## <a name="create-modified-rules-and-display-files"></a>変更されたルールと表示のファイルの作成

このセクションでは、自分のテナントで最初の検証可能な資格情報を作成するために、[サンプル発行者アプリ](https://github.com/Azure-Samples/active-directory-verifiable-credentials/)のルールと表示のファイルを使用してそれらをわずかに変更します。

1. ルールと表示の JSON ファイルをどちらも一時フォルダーにコピーします。 それらの名前をそれぞれ **MyFirstVC-display.json** と **MyFirstVC-rules.json** に変更します。 どちらのファイルも **issuer\issuer_config** にあります。

   ![サンプル アプリ ディレクトリに含まれているルールと表示のファイルを示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![一時フォルダー内のルールと表示のファイルを示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

1. お使いのコード エディターで **MyFirstVC-rules.json** ファイルを開きます。

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

   次に、"type" フィールドを **"MyFirstVC"** に変更します。

   ```json
    "type": ["MyFirstVC"]
  
   ```

   その変更を保存します。

   >[!NOTE]
   > チュートリアルのこの時点では、"configuration" と "client_id" の値は変更しないでください。 [作業の開始](get-started-verifiable-credentials.md)に関するチュートリアルで使用したサンプルの Azure テナントを引き続き使用します。 次のチュートリアルでは、Azure AD を使用します。

1. お使いのコード エディターで **MyFirstVC-display.json** ファイルを開きます。

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

   この検証可能な資格情報がサンプル コードのバージョンとは明らかに異なって見えるように変更を加えます。

    ```json
         "card": {
            "title": "My First VC",
            "issuedBy": "Your Issuer Name",
            "backgroundColor": "#ffffff",
            "textColor": "#000000",
          }
    ```
 
   >[!NOTE]
   > 資格情報が確実に読み取り可能かつアクセス可能になるように、少なくとも 4.5:1 の[コントラスト比](https://www.w3.org/WAI/WCAG21/Techniques/general/G18)でテキストと背景の色を選択します。

   これらの変更を保存します。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

最初の検証可能な資格情報を作成する前に、構成とルールのファイルを保持できる Azure Blob Storage コンテナーを作成します。

1. 記載されているオプションを使用してストレージ アカウントを作成します。 詳細な手順については、「[ストレージ アカウントを作成する](../../storage/common/storage-account-create.md?tabs=azure-portal)」を参照してください。

   - **サブスクリプション:** これらのチュートリアルに使用しているサブスクリプション
   - **リソース グループ**: 前のチュートリアルで使用したのと同じリソース グループ (**vc-resource-group**)
   - **名前**: 一意の名前
   - **場所**: (米国) 米国東部
   - **パフォーマンス**: 標準
   - **アカウントの種類**: Storage V2
   - **レプリケーション**: ローカル冗長
 
   ![[ストレージ アカウントの作成] 画面を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

1. ストレージ アカウントを作成したら、コンテナーを作成します。 **[Blob ストレージ]** で **[コンテナー]** を選択します。 これらの値を使用してコンテナーを作成します。

    - **名前**: vc-container
    - **パブリック アクセス レベル**: プライベート (匿名アクセスなし)
  
   **[作成]** を選択します。

   ![コンテナーの作成を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/new-container.png)

1. 次に、新しいコンテナーを選択して、前に作成した新しいルールと表示のファイル (**MyFirstVC-display.json** と **MyFirstVC-rules.json**) をどちらもアップロードします。

   ![ルール ファイルのアップロードを示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-a-blob-role"></a>BLOB ロールの割り当て

資格情報を作成する前に、サインインしたユーザーがストレージ BLOB 内のファイルにアクセスできるように、適切なロール割り当てを付与しておく必要があります。

1. **[ストレージ]**  >  **[コンテナー]** の順に移動します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[追加]**  >  **[ロールの割り当ての追加]** の順に選択して、 **[ロールの割り当ての追加]** ページを開きます。
1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | ストレージ BLOB データ閲覧者 |
    | アクセスの割り当て先 | ユーザー、グループ、またはサービス プリンシパル |
    | 選択 | &lt;これらの手順を実行するために使用しているアカウント&gt; |

    ![Azure portal の [ロールの割り当ての追加] ページを示すスクリーンショット。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

  >[!IMPORTANT]
  >既定では、コンテナーの作成者には所有者ロールが割り当てられます。 所有者ロールのままでは不十分です。 アカウントにはストレージ BLOB データ閲覧者ロールが必要です。 詳細については、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../../storage/blobs/assign-azure-role-data-access.md)」を参照してください。

## <a name="set-up-verifiable-credentials-preview"></a>検証可能な資格情報 (プレビュー) の設定

次に、最後の手順として、検証可能な資格情報用にテナントを設定します。

1. Azure portal で、**検証可能な資格情報** を検索します。
1. **[Verifiable Credentials (Preview)]\(検証可能な資格情報 (プレビュー)\)** を選択します。
1. **[Get started]\(作業を開始する\)** を選択します。
1. 次の情報を指定して、組織を設定します。

      - **組織名**: 検証可能な資格情報内で自分のビジネスを参照する名前を入力します。 この値は顧客には公開されません。
      - **ドメイン:** DID ドキュメントにサービス エンドポイントとして追加されるドメインを入力します。 [Microsoft Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a) とその他のウォレットでは、DID が[ドメインにリンク](how-to-dnsbind.md)されていることを検証するために、この情報を使用します。 DID を検証できる場合、ウォレットには検証済み記号が表示されます。 ウォレットが DID を検証できない場合、検証できない資格情報が組織によって発行されたことがユーザーに通知されます。 ドメインは、ユーザーがお客様のビジネスについて知っている具体的な何かに DID をバインドするものです。
      - **キー コンテナー:** 前に作成したキー コンテナーの名前を入力します。

   >[!IMPORTANT]
   > ドメインはリダイレクトしないようにします。 そうしないと、DID とドメインはリンクできません。 https://www.domain.com という形式を使用するようにしてください。
  
1. **[Save and create credential]\(保存して資格情報を作成\)** を選択します。

    ![組織 ID の設定を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/save-create.png)

おめでとうございます。これで、検証可能な資格情報 (プレビュー) がテナントで有効になりました。

## <a name="create-your-verifiable-credential-in-the-portal"></a>ポータルでの検証可能な資格情報の作成

前の手順で、 **[Create a new credential]\(新しい資格情報の作成\)** ページが表示されています。

   ![開始画面を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. 資格情報名に「**MyFirstVC**」と入力します。 この名前は検証可能な資格情報を識別するためにポータル内で使用されます。 これは、検証可能な資格情報のコントラクトの一部として含まれます。
1. **[Display file]\(表示ファイル\)** セクションで、 **[Configure display file]\(表示ファイルの構成\)** を選択します。
1. **[ストレージ アカウント]** セクションで、**woodgrovevcstorage** を選択します。
1. 使用可能なコンテナーの一覧から、**vc-container** を選択します。
1. 前に作成した **MyFirstVC-display.json** ファイルを選択します。
1. **[Create a new credential]\(新しい資格情報の作成\)** 画面の **[Rules file]\(ルール ファイル\)** セクションで、 **[Configure rules file]\(ルール ファイルの構成\)** を選択します。
1. **[ストレージ アカウント]** セクションで、**woodgrovecstorage** を選択します。
1. **vc-container** を選択します。
1. **MyFirstVC-rules.json** を選択します。
1. **[Create a new credential]\(新しい資格情報の作成\)** 画面で、 **[作成]** を選択します。

   ![新しい資格情報の作成を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>資格情報 URL

新しい資格情報ができたところで、資格情報 URL をコピーします。

   ![資格情報 URL を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>資格情報 URL はルールと表示のファイルを組み合わせたものです。 これは、検証可能な資格情報の発行要件がユーザーに表示される前に Authenticator によって評価される URL です。

## <a name="update-the-sample-app"></a>サンプル アプリの更新

次に、サンプル アプリの発行者コードに変更を加えて、検証可能な資格情報の URL でそれを更新します。 この手順により、独自のテナントを使用して、検証可能な資格情報を発行できます。

1. サンプル アプリのファイルを置き換えるフォルダーに移動します。
1. issuer フォルダーを開いて、Visual Studio Code で app.js を開きます。
1. 定数 **credential** を新しい資格情報 URL で更新します。 定数 **credentialType** を **'MyFirstVC'** に設定し、ファイルを保存します。

    ![関連領域が強調表示された Visual Studio Code を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

1. コマンド プロンプトを開いて、issuer フォルダーを開きます。
1. 更新されたノード アプリを実行します。

    ```terminal
    node app.js
    ```

1. 別のコマンド プロンプトを使用して ngrok を実行し、8081 に URL を設定します。 [ngrok npm パッケージ](https://www.npmjs.com/package/ngrok/)を使用して、ngrok をグローバルにインストールできます。

    ```terminal
    ngrok http 8081
    ```

    >[!IMPORTANT]
    > [This app or website may be risky]\(このアプリまたは Web サイトが危険である可能性があります\) という警告が表示される場合があります。 自分の DID をドメインにまだリンクしていないため、ここでメッセージが表示されるのは想定どおりです。 構成手順については、[DNS バインド](how-to-dnsbind.md)に関する記事を参照してください。

1. ngrok によって生成された HTTPS URL を開きます。

    ![NGROK 転送エンドポイントを示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

1. **[GET CREDENTIAL]\(資格情報の取得\)** を選択します。
1. Authenticator で QR コードをスキャンします。
1. **[This app or website may be risky]\(このアプリまたは Web サイトが危険である可能性があります\)** 画面で **[Advanced]\(詳細設定\)** を選択します。

   ![最初の警告を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/site-warning.png)

1. 次の **[This app or website may be risky]\(このアプリまたは Web サイトが危険である可能性があります\)** 画面で **[Proceed anyways (unsafe)]\(このまま続行 (安全ではありません)\)** を選択します。

   ![発行者に関する 2 番目の警告を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)

1. **[Add a credential]\(資格情報の追加\)** 画面に以下が表示されます。
    1. 画面上部に、赤色で **[Not verified]\(未検証\)** というメッセージが表示されます。
    1. 資格情報は、表示ファイルに加えた変更に基づいてカスタマイズされます。
    1. **[Sign in to your account]\(アカウントにサインインする\)** オプションでは、didplayground.b2clogin.com が参照されています。
    
      ![警告が表示された [Add a credential]\(資格情報の追加\) 画面を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

1. **[Sign in to your account]\(アカウントにサインインする\)** を選択し、[作業の開始](get-started-verifiable-credentials.md)に関するチュートリアルで指定した資格情報を使用して認証します。
1. 認証が成功すると、 **[Add]\(追加\)** ボタンがアクティブになります。 **[追加]** を選択します。

    ![認証後の [Add a credential]\(資格情報の追加\) 画面を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

   これで、認証にサンプルの Azure B2C テナントを使用しながら、自分のテナントを使用して検証可能な資格情報を生成することで、検証可能な資格情報が発行されました。

     ![Azure AD によって発行され、サンプルの Azure B2C テナントによって認証された検証可能な資格情報を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)

## <a name="verify-the-verifiable-credential-by-using-the-sample-app"></a>サンプル アプリを使用した検証可能な資格情報の検証

自分のテナントから検証可能な資格情報を発行できたところで、サンプル アプリを使用してそれを検証してみましょう。

>[!IMPORTANT]
> テスト時には、[作業の開始](get-started-verifiable-credentials.md)に関するチュートリアルで使用したのと同じメールとパスワードを使用します。 検証可能な資格情報を発行しているのは自分のテナントですが、入力は、サンプルの Azure B2C テナントによって発行された ID トークンから送られてきています。 2 番目のチュートリアルでは、トークンの発行元を自分のテナントに切り替えます。

1. Azure portal の **検証可能な資格情報** ペインで、 **[設定]** を選択します。 DID をコピーします。

   ![DID のコピーを示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

1. 次に、サンプル アプリ ファイルの verifier フォルダー部分を開きます。 検証者のサンプル コードの app.js ファイルを更新し、次の変更を行う必要があります。

    - **credential**: 実際の資格情報 URL に変更します。
    - **credentialType**: 「 **'MyFirstVC'** 」と入力します。
    - **issuerDid**: Azure portal で **[Verifiable Credentials (Preview)]\(検証可能な資格情報 (プレビュー)\)**  >  **[設定]**  >  **[Decentralized identifier (DID)]\(分散化識別子 (DID)\)** の順に移動し、この値をコピーします。
    
   ![発行者 ID と一致する定数発行者 DID の更新を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

1. 発行者 ngrok サービスの実行を停止します。

    ```cmd
    control-c
    ```

1. 次に、検証ツール ポート 8082 を使用して ngrok を実行します。

    ```cmd
    ngrok http 8082
    ```

1. 別のターミナル ウィンドウで検証ツール アプリに移動し、発行者アプリを実行したときと同じようにそれを実行します。

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

1. お使いのブラウザーで ngrok URL を開き、自分のモバイル デバイスで Authenticator を使用して QR コードをスキャンします。
1. 自分のモバイル デバイスで、 **[New permission request]\(新しいアクセス許可の要求\)** 画面の **[Allow]\(許可\)** を選択します。

    >[!NOTE]
    > この検証可能な資格情報に署名している DID は、まだ Azure B2C です。 検証者の DID は、依然として Microsoft のサンプル アプリ テナントのものです。 Microsoft の DID は Microsoft が所有するドメインにリンクされているため、発行フロー中に出たような警告は表示されません。 この状況は次のセクションで更新されます。
    
   ![[New permission request]\(新しいアクセス許可の要求\) 画面を示すスクリーンショット。](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

1. 自分の資格情報が正常に検証されました。

## <a name="next-steps"></a>次のステップ

サンプル コードで自分の発行者から検証可能な資格情報を発行できるようになったところで、次のセクションに進みましょう。 検証可能な資格情報を取得しようとするユーザーの認証に独自の ID プロバイダーを使用します。 また、自分の DID を使って表示要求に署名します。

> [!div class="nextstepaction"]
> [チュートリアル - 自分のテナントを使用して検証可能な資格情報を発行して検証する](issue-verify-verifiable-credentials-your-tenant.md)