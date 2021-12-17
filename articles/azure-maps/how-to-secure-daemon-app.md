---
title: Microsoft Azure Maps でデーモン アプリケーションをセキュリティで保護する方法
titleSuffix: Azure Maps
description: この記事では、バックグラウンド プロセス、タイマー、ジョブなどのデーモン アプリケーションを Microsoft Azure Maps の信頼でき、セキュリティで保護された環境でホストする方法について説明します。
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
custom.ms: subject-rbac-steps
ms.openlocfilehash: e64645f7bdcbfb40cbee0fd29d1df2464a5d4f3e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450858"
---
# <a name="secure-a-daemon-application"></a>デーモン アプリケーションをセキュリティで保護する

この記事では、デーモン アプリケーションを Microsoft Azure Maps の信頼でき、セキュリティで保護された環境でホストする方法について説明します。

デーモン アプリケーションの例を次に示します。

- Azure Web ジョブ
- Azure Function App
- Windows サービス
- 実行中の信頼できるバックグラウンド サービス

## <a name="view-azure-maps-authentication-details"></a>Azure Maps の認証の詳細を表示する

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

>[!IMPORTANT]
>運用アプリケーションの場合、Azure AD と Azure ロールベースのアクセス制御 (Azure RBAC) を実装することをお勧めします。 Azure AD の概念の概要については、「[Azure Maps による認証](azure-maps-authentication.md)」を参照してください。

## <a name="scenario-shared-key-authentication-with-azure-key-vault"></a>シナリオ: Azure Key Vault を使用する共有キー認証

共有キー認証を使用するアプリケーションでは、キーを安全なストアに保管する必要があります。 このシナリオでは、アプリケーション キーをシークレットとして Azure Key Vault に安全に保管する方法について説明します。 アプリケーションでは、共有キーをアプリケーション構成に格納しないで、Azure Key Vault のシークレットとして取得できます。 キーの再生成を簡略化するために、アプリケーションでは一度に 1 つのキーを使用することをお勧めします。 アプリケーションでは、未使用のキーを再生成し、再生成されたキーを Azure Key Vault にデプロイして、引き続き 1 つのキーで現在の接続を維持することができます。 Azure Key Vault を構成する方法については、「[Azure Key Vault 開発者ガイド](../key-vault/general/developers-guide.md)」を参照してください。

>[!IMPORTANT]
>このシナリオでは、Azure Key Vault を介して Azure Active Directory に間接的にアクセスします。 ただし、Azure AD 認証を直接使用することをお勧めします。 Azure AD を直接使用すると、共有キー認証の使用と Key Vault の設定に伴う複雑さの増大と運用上の要件を回避できます。

以下の手順では、このプロセスの概要を示します。

1. [Azure Key Vault を作成します](../key-vault/general/quick-create-portal.md)。
2. アプリの登録またはマネージド ID を作成して、[Azure AD サービス プリンシパル](../active-directory/fundamentals/service-accounts-principal.md)を作成します。 作成されたプリンシパルは、Azure Key Vault へのアクセスを担当します。
3. サービス プリンシパルに、Azure キー シークレット `get` アクセス許可へのアクセス権を割り当てます。 アクセス許可を設定する方法の詳細については、「[Azure portal を使用して Key Vault アクセス ポリシーを割り当てる](../key-vault/general/assign-access-policy-portal.md)」を参照してください。
4. 開発者に対して、シークレット `set` アクセス許可へのアクセス権を一時的に割り当てます。
5. Key Vault シークレットに共有キーを設定し、デーモン アプリケーション用の構成としてシークレット ID を参照します。
6. シークレットの `set` アクセス許可を削除します。
7. Azure Key Vault から共有キー シークレットを取得するには、デーモン アプリケーションで Azure Active Directory 認証を実装します。
8. 共有キーを使用して、Azure Maps REST API 要求を作成します。
以上で、デーモン アプリケーションで Key Vault から共有キーを取得することができます。

> [!TIP]
> アプリが Azure 環境でホストされている場合、マネージド ID を使用して、認証用のシークレットを管理するコストと複雑さを軽減することをお勧めします。 マネージド ID を設定する方法については、「[チュートリアル: マネージド ID を使用して Key Vault を .NET の Azure Web アプリに接続する](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)」を参照してください。

## <a name="scenario-azure-ad-role-based-access-control"></a>シナリオ:Azure AD ロールベースのアクセス制御

Azure Maps アカウントが作成されると、Azure Maps の `Client ID` の値が Azure portal の [認証の詳細] ページに表示されます。 この値は、REST API 要求に使用されるアカウントを表します。 この値をアプリケーション構成に格納し、HTTP 要求を行う前に取得する必要があります。 このシナリオの目的は、デーモン アプリケーションで Azure AD に対して認証し、Azure Maps REST API を呼び出せるようにすることです。

> [!TIP]
>マネージド ID コンポーネントのベネフィットが得られるように、Azure Virtual Machines、Virtual Machine Scale Sets、または App Services でホストすることをお勧めします。

### <a name="host-a-daemon-on-azure-resources"></a>Azure リソースでデーモンをホストする

Azure リソースで実行する場合、Azure マネージド ID を構成して、資格情報管理作業を低コストで最小限に抑えることができます。

アプリケーションによるマネージド ID へのアクセスを有効にするには、[マネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。

マネージド ID のベネフィットの一部は、次のとおりです。

- Azure システムで管理される X509 証明書の公開キー暗号化認証。
- クライアント シークレットではなく、X509 証明書を使用する Azure AD セキュリティ。
- Azure によって、マネージド ID リソースに関するすべての証明書が管理および更新される。
- マネージド ID を使用すると、Azure Key Vault などのセキュリティで保護されたシークレット ストア サービスが不要になるため、資格情報の運用管理が簡略化される。

### <a name="host-a-daemon-on-non-azure-resources"></a>Azure 以外のリソースでデーモンをホストする

Azure 以外の環境で実行する場合、マネージド ID は使用できません。 そのため、デーモン アプリケーションの Azure AD アプリケーション登録を使用して、サービス プリンシパルを構成する必要があります。

#### <a name="create-new-application-registration"></a>新しいアプリケーションの登録を作成する

アプリケーションの登録を既に作成している場合は、「[委任された API アクセス許可を割り当てる](#assign-delegated-api-permissions)」に進みます。

新しいアプリケーションの登録を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択します。

3. 左側のウィンドウの **[管理]** で、 **[アプリの登録]** を選択します。

4. **[+ 新規登録]** タブを選択します。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/app-registration.png" alt-text="[アプリの登録] を表示する。":::

5. **[名前]** を入力し、 **[サポートされているアカウントの種類]** を選択します。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-create.png" alt-text="アプリの登録を作成する。":::

6. **[登録]** を選択します。  

#### <a name="assign-delegated-api-permissions"></a>委任された API アクセス許可を割り当てる

委任された API アクセス許可を Azure Maps に割り当てるには、次の手順に従います。

1. まだサインインしていない場合は、[Azure portal](https://portal.azure.com)にサインインします。

2. **[Azure Active Directory]** を選択します。

3. 左側のウィンドウの **[管理]** で、 **[アプリの登録]** を選択します。

4. アプリケーションを選択します。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-select.png" alt-text="[アプリの登録] を選択する。":::

5. 左側のメニューの **[管理]** で、 **[API のアクセス許可]** を選択します。

6. **[アクセス許可の追加]** を選択します。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-add-permissions.png" alt-text="アプリのアクセス許可を追加する。":::

7. **[所属する組織で使用している API]** タブを選択します。

8. 検索ボックスに「**Azure Maps**」と入力します。

9. **[Azure Maps]** を選択します。

   :::image type="content" border="true" source="./media/how-to-manage-authentication/app-permissions.png" alt-text="アプリのアクセス許可を要求する。":::

10. **[Access Azure Maps]\(Azure Maps へのアクセス\)** チェック ボックスをオンにします。

11. **[アクセス許可の追加]** を選択します.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/select-app-permissions.png" alt-text="アプリの [API のアクセス許可] を選択する。":::

#### <a name="create-a-client-secret-or-configure-certificate"></a>クライアント シークレットの作成または証明書の構成

サーバーまたはアプリケーションベースの認証をアプリケーションに実装するには、次の 2 つのオプションのいずれかを選択できます。

- 公開キー証明書をアップロードします。
- クライアント シークレットを作成します。

##### <a name="upload-a-public-key-certificate"></a>公開キー証明書のアップロード

公開キー証明書をアップロードするには、次の手順に従います。

1. 左側のウィンドウの **[管理]** で、 **[証明書とシークレット]** を選択します。

2. **[証明書のアップロード]** を選択します。
   :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate.png" alt-text="証明書をアップロードする。":::

3. テキスト ボックスの右側にあるファイル アイコンを選択します。

4. *.crt*、 *.cer*、または *.pem* ファイルを選択し、 **[追加]** を選択します。

    :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate-file.png" alt-text="証明書ファイルをアップロードする。":::

##### <a name="create-a-client-secret"></a>クライアント シークレットの作成

クライアント シークレットを作成するには、次の手順に従います。

1. 左側のウィンドウの **[管理]** で、 **[証明書とシークレット]** を選択します。

2. **[+ 新しいクライアント シークレット]** を選択します。

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret.png" alt-text="新しいクライアント シークレット。":::

3. クライアント シークレットの説明を入力します。

4. **[追加]** を選択します。

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret-add.png" alt-text="新しいクライアント シークレットを追加する。":::

5. シークレットをコピーして、Azure Key Vault などのサービスに安全に保管します。 シークレットは、この記事の「[マネージド ID を使用してトークンを要求する](#request-a-token-with-managed-identity)」セクションでも使用します。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/copy-client-secret.png" alt-text="クライアント シークレットをコピーする。":::

     >[!IMPORTANT]
     >証明書またはシークレットを安全に保管するには、「[Azure Key Vault 開発者ガイド](../key-vault/general/developers-guide.md)」を参照してください。 Azure AD からトークンを取得するには、このシークレットを使用します。

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

### <a name="request-a-token-with-managed-identity"></a>マネージド ID を使用してトークンを要求する

ホスト リソース用にマネージド ID を構成したら、Azure SDK または REST API を使用して Azure Maps のトークンを取得します。 アクセス トークンを取得する方法については、[アクセス トークンの取得](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)に関するページを参照してください。

### <a name="request-token-with-application-registration"></a>アプリケーションの登録を使用してトークンを要求する

アプリを登録して Azure Maps と関連付けた後、アクセス トークンを要求する必要があります。

アクセス トークンを要求するには、次の手順に従います。

1. まだサインインしていない場合は、[Azure portal](https://portal.azure.com)にサインインします。

2. **[Azure Active Directory]** を選択します。

3. 左側のウィンドウの **[管理]** で、 **[アプリの登録]** を選択します。

4. アプリケーションを選択します。

5. [概要] ページが表示されます。 [アプリケーション (クライアント) ID] と [ディレクトリ (テナント) ID] をコピーします。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/get-token-params.png" alt-text="トークン パラメーターをコピーする。":::

ここでは、[Postman](https://www.postman.com/) アプリケーションを使用してトークン要求を作成しますが、別の API 開発環境も使用できます。

1. Postman アプリ内で **[新規]** を選択します。

2. **[Create New]** ウィンドウで、 **[HTTP Request]** を選択します。

3. 要求の **[Request name]** (例: *POST Token Request*) を入力します。

4. HTTP メソッドの **[POST]** を選択します。

5. 次の URL をアドレス バーに入力し (`{Tenant-ID}` をディレクトリ (テナント) ID に、`{Client-ID}` をアプリケーション (クライアント) ID に置き換えます)、`{Client-Secret}` をお使いのクライアント シークレットに置き換えます。

    ```http
    https://login.microsoftonline.com/{Tenant-ID}/oauth2/v2.0/token?response_type=token&grant_type=client_credentials&client_id={Client-ID}&client_secret={Client-Secret}%3D&scope=api%3A%2F%2Fazmaps.fundamentals%2F.default
    ```

6. **[送信]** を選択します。

7. 次の JSON 応答が表示されます。

```json
{
    "token_type": "Bearer",
    "expires_in": 86399,
    "ext_expires_in": 86399,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFq..."
}
```

認証フローの詳細については、[Microsoft ID プラットフォーム上の OAuth 2.0 クライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)に関するページを参照してください

## <a name="next-steps"></a>次のステップ

詳細な例については、以下をご覧ください。
> [!div class="nextstepaction"]
> [Azure AD の認証シナリオ](../active-directory/develop/authentication-vs-authorization.md)

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示すサンプルを確認します。
> [!div class="nextstepaction"]
> [Azure Maps のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
