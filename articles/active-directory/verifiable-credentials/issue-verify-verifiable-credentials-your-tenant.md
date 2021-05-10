---
title: チュートリアル - Azure テナントを使用して検証可能な資格情報を発行して検証する (プレビュー)
description: 検証可能な資格情報のコード サンプルを変更して、Azure テナントで使用する
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: e4772b6701065a44416d849faa9a501bd7895f27
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553381"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>チュートリアル - テナントを使用して検証可能な資格情報を発行して検証する (プレビュー)

> [!IMPORTANT]
> Azure Active Directory の検証可能な資格情報は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure テナントで検証可能な資格情報サービスを設定したので、サンプル アプリを使用して Azure Active Directory (Azure AD) によって検証可能な資格情報を発行できるようにするために必要な手順について説明します。

この記事では、次を行います。

> [!div class="checklist"]
> * Azure AD へサンプル アプリを登録する
> * ルールと表示ファイルを作成する
> * ルールと表示ファイルをアップロードする
> * Azure Key Vault を使用するよう検証可能な資格情報発行者サービスを設定する
> * サンプル コードをテナントの情報で更新する

このサンプル コードでは、検証済み資格情報エキスパート VC を発行する前に、ユーザーが ID ​プロバイダー (具体的には Azure AD B2C) に対して認証を行う必要があります。 検証可能な資格情報発行者の全員が、資格情報を発行する前に認証する必要があるわけではありません。

ID トークンを認証することで、ユーザーは自分の資格情報を受け取る前に誰であるかを証明できます。 ユーザーが正常にログインすると、ID プロバイダーからユーザーに関するクレームを含むセキュリティ トークンが返されます。 その後、発行者サービスによってこれらのセキュリティ トークンとクレームが検証可能な資格情報に変換されます。 検証可能な資格情報が、発行者の DID で署名されます。

OpenID Connect プロトコルをサポートするすべての ID プロバイダーがサポートされています。 サポートされる ID プロバイダーには、[Azure Active Directory](../fundamentals/active-directory-whatis.md) や [Azure AD B2C](../../active-directory-b2c/overview.md) などがあります。 このチュートリアルでは、ADD を使用します。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[前のチュートリアル](enable-your-tenant-verifiable-credentials.md)の手順をすでに完了しており、使用する環境にアクセスできることを前提としています。

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>アプリを登録して DID ウォレットでユーザーがサインインできるようにする

検証可能な資格情報を発行するには、アプリを登録して、Authenticator やその他の検証可能な資格情報ウォレットでユーザーのサインインができるようにする必要があります。  

Azure AD に ' VC ウォレット アプリ ' という名前のアプリケーションを登録し、クライアント ID を取得します。

1. [Azure AD](../develop/quickstart-register-app.md) にアプリケーションを登録するための手順に従ってください。登録時には、以下の値を使用してください。

   - 名前: "VC ウォレット アプリ"
   - サポートされているアカウントの種類: この組織のディレクトリ内のアカウントのみ
   - リダイレクト URI: vcclient://openid/

   ![アプリケーションを登録する](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. アプリケーションを登録したら、アプリケーション (クライアント) ID を書き留めます。 この値は、後で必要になります。

   ![アプリケーション クライアント ID](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. **[エンドポイント]** ボタンを選択し、OpenID Connect メタデータ ドキュメントの URI をコピーします。 この情報は、次のセクションで必要になります。 

   ![発行者のエンドポイント](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Azure Key Vault にアクセスできるようにノード アプリを設定する

ユーザーの資格情報発行要求を認証する際、発行者の Web サイトでは Azure Key Vault の暗号化キーが使用されます。 Azure Key Vault にアクセスするには、Web サイトには、Azure Key Vault の認証に使用できるクライアント ID とクライアント シークレットが必要です。

1. VC ウォレット アプリの概要ページを表示しているときに、 **[証明書とシークレット]** を選択します。
    ![証明書とシークレット](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** を選択します。
    1. "Node VC クライアント シークレット" のような説明を追加します。
    1. 有効期限: 1 年。
  ![1 年間の有効期限が設定されたアプリケーション シークレット](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. シークレットをコピーします。 サンプルのノード アプリを更新するには、この情報が必要です。

>[!WARNING]
> シークレットをコピーできるのは 1 回だけです。 この後、シークレットは一方向にハッシュされます。 ID をコピーしないでください。 

Azure AD でアプリケーションとクライアント シークレットを作成した後、Key Vault で操作を実行するために必要なアクセス許可をアプリケーションに付与する必要があります。 Web サイトからそこに格納されている秘密キーにアクセスして使用できるようにするには、これらのアクセス許可の変更を行う必要があります。

1. Key Vault に移動します。
2. これらのチュートリアルで使用している Key Vault を選択します。
3. 左側のナビゲーションで **[アクセス ポリシー]** を選択します
4. **[アクセス ポリシーの追加]** をクリックします。
5. **[キーのアクセス許可]** セクションで **[取得]** 、 **[署名]** の順に選択します。
6. **[プリンシパル]** を選択し、アプリケーション ID を使用して以前に登録したアプリケーションを検索します。 それを選択します。
7. **[追加]** を選択します。
8. **[保存]** を選択します。

Key Vault のアクセス許可とアクセス制御の詳細については、[Key Vault RBAC のガイド](../../key-vault/general/rbac-guide.md)を参照してください。

![Key Vault アクセス許可の付与](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>環境に合わせて変更を加える

ここまでは、サンプル アプリを使用してきました。 このアプリでは [Azure Active Directory B2C](../../active-directory-b2c/overview.md) を使用し、Azure AD を使用するように切り替えているため、実際の環境に合わせて変更を加えるだけでなく、以前は使用していなかった追加のクレームをサポートする必要があります。

1. 以下のルール ファイルをコピーして、**modified-expertRules.json** に保存します。 

    > [!NOTE]
    > このルール ファイルには、 **"scope": "openid profile"** が含まれていますが、サンプル アプリのルール ファイルには含まれていませんでした。 次のセクションでは、Azure Active Directory テナントでオプションのクレームを有効にする方法について説明します。 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. ファイルを開き、**client_id** と **configuration** の値を、前のセクションでコピーした 2 つの値に置き換えます。

    ![この手順の一部として変更する必要がある 2 つの値が強調表示されています](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  **configuration** の値は、OpenID Connect メタデータ ドキュメントの URI です。

  サンプル コードでは Azure Active Directory B2C が使用されており、ここでは Azure Active Directory を使用しているため、これらのクレームを ID トークンに含めて検証可能な資格情報に書き込むには、スコープを介してオプションのクレームを追加する必要があります。 

3. Azure portal に戻り、Azure Active Directory を開きます。
4. **[アプリの登録]** を選択します。
5. 先ほど作成した VC ウォレット アプリを開きます。
6. **[トークン構成]** を選択します。
7. **[オプションの要求の追加]** を選択します。

     ![[トークン構成] で、オプションの要求を追加します](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. **[トークンの種類]** から **[ID]** を選択し、使用可能なクレームの一覧から **given_name** と **family_name** を選択します

     ![オプションのクレームの追加](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. **[追加]** をクリックします。
10. 以下のようなアクセス許可の警告が表示された場合は、チェックボックスをオンにして **[追加]** を選択します。

     ![オプションのクレームを追加するための許可](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

これにより、ユーザーの検証可能な資格情報を発行するための "サインイン" を行う際に、VC ウォレット アプリは、検証可能な資格情報に書き込まれるスコープのパラメーターを介して特定のクレームを含めることを認識します。

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>このルール ファイルと古い表示ファイルを使用して新しい VC を作成します

1. 新しいルール ファイルをコンテナーにアップロードします
1. [検証可能な資格情報] ページで、古い表示ファイルと新しいルール ファイル (**modified-credentialExpert.json**) を使用して、**modifiedCredentialExpert** という名前の新しい資格情報を作成します。
1. **[概要]** ページで資格情報の作成プロセスが完了したら、次のセクションで必要になる **資格情報の発行 URL** をコピーして保存します。

## <a name="before-we-continue"></a>続行する前に

必要なコード変更を行う前に、いくつかの値をまとめておく必要があります。 次のセクションではこれらの値を使用して、サンプル コードでコンテナーに格納されている独自のキーを使用するようにします。 ここまでで、次の値の準備が整っているはずです。

- 先ほど作成した資格情報からの **コントラクト URI** (資格情報 URL の発行)
- **アプリケーション クライアント ID** ノード アプリを登録したときに取得しました。
- **クライアント シークレット** アプリに Key Vault へのアクセス許可を付与したときに作成しました。

サンプル アプリで一度変更を行う前に、上記以外のいくつかの値を取得する必要があります。 それでは、これらを入手してみましょう。

### <a name="verifiable-credentials-settings"></a>検証可能な資格情報の設定

1. [検証可能な資格情報] ページに移動し、 **[設定]** を選択します。  
1. 次の値をコピーします。

    - テナント識別子 
    - 発行者識別子 (DID)
    - キー コンテナー (URI)

1. 署名キー識別子の下には URI がありますが、必要なのはその一部のみです。 下の画像で赤い四角形で強調表示されている **issuerSigningKeyION** という部分からコピーします。

   ![サインイン キー識別子](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>DID ドキュメント

1. [DIF ION ネットワーク エクスプローラー](https://identity.foundation/ion/explorer/)を開きます

2. 検索バーに DID を貼り付けます。

4. 書式設定された応答から **verificationMethod** というセクションを探します。
5. "VerificationMethod" の配下にある ID をコピーし、kvSigningKeyId というラベルを付けます
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

これで、サンプル コードを変更するために必要なものがすべて揃いました。

- **発行者:** app.js。const 資格情報を新しいコントラクト URI で更新します
- **検証ツール:** app.js。issuerDid を実際の発行者識別子で更新します
- **発行者と検証ツール** didconfig.json を次の値で更新します。


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>これはデモ アプリケーションです。通常はアプリケーションにシークレットを直接付与することは絶対にしないでください。


これで、独自のキーを使用して Azure Active Directory テナントから独自の検証可能な資格情報を発行して検証するための準備がすべて整いました。 

## <a name="issue-and-verify-the-vc"></a>VC を発行して検証する

前のチュートリアルで行った手順に従って、検証可能な資格情報を発行し、アプリで検証します。 検証プロセスが正常に完了したら、検証可能な資格情報について学習を続ける準備が整います。

1. コマンド プロンプトを開いて、発行者フォルダーを開きます。
2. 更新されたノード アプリを実行します。

    ```terminal
    node app.js
    ```

3. 別のコマンド プロンプトを使用して ngrok を実行し、8081 に URL を設定します

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > このアプリまたは Web サイトが危険である可能性があるという警告が表示される場合があります。 自分の DID をドメインにまだリンクしていないため、ここでメッセージが表示されるのは想定通りです。 [DNS バインド](how-to-dnsbind.md)の手順に従って、これを構成します。

    
4. ngrok によって生成された HTTPS URL を開きます。

    ![NGROK 転送エンドポイント](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. **[資格情報の取得]** を選択します
6. Authenticator で QR コードをスキャンします。
7. **[このアプリまたは Web サイトは危険であるおそれがあります]** という警告メッセージが表示されたら **[詳細設定]** を選択します。

  ![最初の警告](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. 危険な Web サイトの警告で、 **[このまま続行 (安全ではありません)]** を選択します

  ![発行者に関する 2 番目の警告](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. **[視覚情報の追加]** 画面に以下が表示されます。 
    1. 画面上部に、赤色で **[未確認]** というメッセージが表示されます。
    1. 資格情報は、表示ファイルに加えた変更に基づいてカスタマイズされます。
    1. **[アカウントにサインインする]** オプションは、Azure AD のサインイン ページを指しています。
    
   ![警告が表示されている資格情報の追加画面](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. **[アカウントにサインインする]** を選択し、Azure AD テナントのユーザーを使用して認証します。
11. 正常に認証されると、 **[追加]** ボタンの灰色表示が解除されます。 **[追加]** を選択します。

  ![認証後の資格情報の追加画面](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

これで、認証に B2C テナントを使用しながら、テナントを使用して VC を生成する検証可能な資格情報を発行しました。

  ![Azure AD によって発行され、Azure B2C インスタンスによって認証された VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>サンプル アプリを使用して VC の検証をテストする

Azure AD からのクレームを使用して独自のテナントから検証可能な資格情報を発行したので、サンプル アプリを使用して検証してみましょう。

1. 発行者 ngrok サービスの実行を停止します。

    ```cmd
    control-c
    ```

2. 次に、検証ツール ポート 8082 を使用して ngrok を実行します。

    ```cmd
    ngrok http 8082
    ```

3. 別のターミナル ウィンドウで検証ツール アプリに移動し、発行者アプリを実行したときと同じように実行します。

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. ブラウザーで ngrok URL を開き、モバイル デバイスの Authenticator を使用して QR コードをスキャンします。
5. モバイル デバイスで、 **[新しいアクセス許可の要求]** 画面の **[許可]** を選択します。

   >[!IMPORTANT]
    > サンプル アプリも DID を使用してプレゼンテーション要求に署名しているため、このアプリまたは Web サイトは危険であるおそれがあるという警告が表示されます。 自分の DID をドメインにまだリンクしていないため、ここでメッセージが表示されるのは想定通りです。 [DNS バインド](how-to-dnsbind.md)の手順に従って、これを構成します。
    
   ![新しいアクセス許可の要求](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. これで資格情報が正常に検証され、Web サイトに Azure AD のユーザー アカウントの姓と名が表示されます。 

これでチュートリアルが完了し、正式に検証済み資格情報のエキスパートになりました。 サンプル アプリでは発行と検証の両方に対して DID を使用し、Azure AD から検証可能な資格情報にクレームを書き込んでいます。 

## <a name="next-steps"></a>次の手順

- [カスタム資格情報](credential-design.md)の作成方法について確認する
- 発行者サービスの通信[例](issuer-openid.md)
