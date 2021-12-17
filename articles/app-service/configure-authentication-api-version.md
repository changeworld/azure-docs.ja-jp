---
title: AuthN および AuthZ API のバージョンを管理する
description: 必要に応じて、App Service 認証 API を V2 にアップグレードするか、特定のバージョンにピン留めします。
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e526f4a56806fa41d8955337d7e8463afac80b21
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113095165"
---
# <a name="manage-the-api-and-runtime-versions-of-app-service-authentication"></a>App Service 認証の API とランタイムのバージョンを管理する

この記事では、[App Service での組み込み認証と承認](overview-authentication-authorization.md)の API とランタイムのバージョンをカスタマイズする方法について説明します。

App Service 認証用の管理 API には、2 つのバージョンがあります。 Azure portal での "認証" エクスペリエンスには、V2 バージョンが必要です。 V1 の API が既に使用されているアプリは、いくつかの変更が行われた後に、V2 バージョンにアップグレードできます。 具体的には、シークレット構成を、スロット固定のアプリケーション設定に移動する必要があります。 これは、アプリのポータルの [認証] セクションから自動的に実行されるようにすることができます。

## <a name="update-the-configuration-version"></a>構成バージョンを更新する

> [!WARNING]
> V2 に移行すると、Azure portal、Azure CLI、Azure PowerShell の既存のエクスペリエンスなど、一部のクライアントを介したアプリケーションに対する App Service の認証または承認機能の管理が無効になります。 これを元に戻すことはできません。

V2 API では、V1 で行われていたように、個別のプロバイダーとして Microsoft アカウントを作成したり編集したりすることはできません。 むしろ、集中型 [Microsoft ID プラットフォーム](../active-directory/develop/v2-overview.md)を活用して、Azure AD と個人の両方の Microsoft アカウントを使用してユーザーをサインインさせます。 V2 API に切り替えるときは、V1 の Azure Active Directory 構成を使用して Microsoft ID プラットフォーム プロバイダーが構成されます。 V1 Microsoft アカウント プロバイダーは移行プロセスで持ち越されて、引き続き通常どおり動作しますが、新しい Microsoft ID プラットフォーム モデルに移行することをお勧めします。 詳細については、「[Microsoft アカウント プロバイダーの登録のサポート](#support-for-microsoft-account-provider-registrations)」を参照してください。

自動化された移行プロセスでは、プロバイダーのシークレットがアプリケーション設定内に移動され、構成の残りの部分は新しい形式に変換されます。 自動移行を使用するには:

1. ポータル内でアプリに移動し、 **[認証]** メニュー オプションを選択します。
1. アプリが V1 モデルを使用して構成されている場合は、 **[アップグレード]** ボタンが表示されます。
1. 確認プロンプトで説明を確認します。 移行を実行する準備ができたら、プロンプトで **[アップグレード]** をクリックします。

### <a name="manually-managing-the-migration"></a>移行を手動で管理する

次の手順では、上記で説明した自動バージョンを使用しない場合に、アプリケーションを V2 API に手動で移行することができます。

#### <a name="moving-secrets-to-application-settings"></a>シークレットをアプリケーション設定に移動

1. V1 API を使用して既存の構成を取得します。

   ```azurecli
   az webapp auth show -g <group_name> -n <site_name>
   ```

   結果として取得した JSON ペイロードで、構成した各プロバイダーに使用されているシークレット値をメモします。

   * AAD: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Microsoft アカウント: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > シークレット値は重要なセキュリティ資格情報であり、慎重に扱う必要があります。 これらの値を共有したり、ローカル マシン上に保持したりしないでください。

1. 各シークレット値のスロット固定アプリケーション設定を作成します。 各アプリケーション設定の名前を選択できます。 この値は、前の手順で取得した値と一致しているか、その値を使用して作成した [Key Vault シークレットを参照](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json)している必要があります。

   この設定を作成するには、Azure portal を使用するか、プロバイダーごとに次の方法を実行できます。

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > この構成のアプリケーション設定はスロット固定としてマークする必要があります。つまり、それらは、[スロット スワップ操作](./deploy-staging-slots.md)中に環境間を移動しません。 これは、認証構成自体が環境に関連付けられているからです。 

1. `authsettings.json` という名前の新しい JSON ファイルを作成します。前に受け取った出力から各シークレット値を削除します。 シークレットが含まれていないことを確認して、残りの出力をファイルに書き込みます。 場合によっては、空の文字列を含む配列が構成に含まれていることがあります。 `microsoftAccountOAuthScopes` がそうでないことを確認し、そうである場合は、その値を `null` に切り替えます。

1. 前の手順で作成した各プロバイダーのアプリケーション設定名を指すプロパティを `authsettings.json` に追加します。
 
   * AAD: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Microsoft アカウント: `microsoftAccountClientSecretSettingName`

   この操作後のサンプル ファイルは、次のようになります。この場合は、AAD 用にのみ構成されています。

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. このファイルをアプリの新しい認証および承認構成として送信します。

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. この後もアプリが想定どおりに動作していることを確認します。

1. 前の手順で使用したファイルを削除します。

これで、ID プロバイダーのシークレットをアプリケーション設定として格納するためにアプリが移行されました。

#### <a name="support-for-microsoft-account-provider-registrations"></a>Microsoft アカウント プロバイダーの登録のサポート

既存の構成に Microsoft アカウント プロバイダーが含まれており、Azure Active Directory プロバイダーが含まれていない場合は、構成を Azure Active Directory プロバイダーに切り替えてから、移行を実行できます。 これを行うには、次の手順を実行します。

1. Azure portal で [ **[アプリの登録]**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) に移動して、お使いの Microsoft アカウント プロバイダーに関連付けられている登録を見つけます。 [個人用アカウントからのアプリケーション] という見出しの下に表示されている場合があります。
1. その登録の [認証] ページに移動します。 [リダイレクト URI] の下に、末尾が `/.auth/login/microsoftaccount/callback` のエントリが表示されます。 この URI をコピーします。
1. コピーしたものと一致する新しい URI を追加します。ただし、末尾は `/.auth/login/aad/callback` にする必要があります。 これにより、App Service の認証および承認構成でこの登録を使用できるようになります。
1. アプリの App Service 認証および承認構成に移動します。
1. Microsoft アカウント プロバイダーの構成を収集します。
1. 前の手順で収集したクライアント ID とクライアント シークレットの値を指定し、"詳細" 管理モードを使用して Azure Active Directory プロバイダーを構成します。 発行者の URL については、`<authentication-endpoint>/<tenant-id>/v2.0` を使用し、 *\<authentication-endpoint>* を [クラウド環境の認証エンドポイント](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (たとえば、グローバル Azure の場合、"https://login.microsoftonline.com") に置き換え、さらに、 *\<tenant-id>* を **ディレクトリ (テナント) ID** に置き換えます。
1. 構成を保存したら、ブラウザーでサイトの `/.auth/login/aad` エンドポイントに移動し、サインイン フローを完了して、ログイン フローをテストします。
1. この時点で、構成は正常にコピーされましたが、既存の Microsoft アカウント プロバイダーの構成はそのまま残っています。 それを削除する前に、アプリのすべての部分で、ログイン リンクなどを使用して Azure Active Directory プロバイダーが参照されていることを確認してください。アプリのすべての部分が想定どおりに動作することを確認します。
1. AAD Azure Active Directory プロバイダーに対して機能することを検証したら、Microsoft アカウント プロバイダーの構成を削除できます。

> [!WARNING]
> AAD アプリの登録で[サポートされているアカウントの種類](../active-directory/develop/supported-accounts-validation.md)を変更することにより、この 2 つの登録を集中させることができます。 ただし、これにより、Microsoft アカウント ユーザーに対して新しい同意プロンプトが強制され、それらのユーザーの ID 要求の構造が異なるものになる場合があります。特に、新しいアプリ ID が使用されてから、`sub` で値が変化します。 この方法は、十分に理解している場合を除き、推奨されません。 代わりに、V2 API でこの 2 つの登録に対するサポートが提供されるのを待つことをお勧めします。

#### <a name="switching-to-v2"></a>V2 への切り替え

上記の手順を実行したら、Azure portal でアプリに移動します。 [認証 (プレビュー)] セクションを選択します。 

または、サイト リソースの下にある `config/authsettingsv2` リソースに対して PUT 要求を行うこともできます。 ペイロードのスキーマは、[ファイルベースの構成](configure-authentication-file-based.md)で取り込まれたものと同じです。

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>特定の認証ランタイム バージョンにアプリをピン留めする

認証/承認を有効にすると、[機能の概要](overview-authentication-authorization.md#how-it-works)で説明されているように、プラットフォーム ミドルウェアが HTTP 要求パイプラインに挿入されます。 このプラットフォーム ミドルウェアは、定期的なプラットフォームの更新の一環として、新機能と機能強化で定期的に更新されます。 既定では、Web アプリまたは関数アプリは、このプラットフォーム ミドルウェアの最新バージョンで実行されます。 これらの自動更新は、常に下位互換性があります。 ただし、まれに、この自動更新によって Web アプリまたは関数アプリにランタイムの問題が発生することがあります。その場合は、以前のミドルウェア バージョンに一時的にロールバックすることができます。 この記事では、特定のバージョンの認証ミドルウェアにアプリを一時的にピン留めする方法について説明します。

### <a name="automatic-and-manual-version-updates"></a>自動および手動でのバージョンの更新 

アプリの `runtimeVersion` 設定を設定することで、プラットフォーム ミドルウェアの特定のバージョンにアプリをピン留めすることができます。 特定のバージョンに明示的にピン留めし直さない限り、アプリは常に最新バージョンで実行されます。 一度にサポートされるバージョンはいくつかあります。 サポートされなくなった無効なバージョンにピン留めすると、アプリでは代わりに最新バージョンが使用されます。 常に最新バージョンを実行するには、`runtimeVersion` を ~1 に設定します。 

### <a name="view-and-update-the-current-runtime-version"></a>現在のランタイム バージョンの表示と更新

アプリで使用されるランタイム バージョンを変更できます。 新しいランタイム バージョンは、アプリを再起動した後に有効になります。 

#### <a name="view-the-current-runtime-version"></a>現在のランタイム バージョンの表示

プラットフォーム認証ミドルウェアの現在のバージョンを表示するには、Azure CLI を使用するか、アプリ内にある組み込みバージョン HTTP エンドポイントのいずれか 1 つを経由します。

##### <a name="from-the-azure-cli"></a>Azure CLI から

Azure CLI を使用して、[az webapp auth show](/cli/azure/webapp/auth#az_webapp_auth_show) コマンドで現在のミドルウェア バージョンを表示します。

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

このコードでは、`<my_app_name>` をアプリの名前に置き換えます。 また、`<my_resource_group>` をアプリのリソース グループの名前に置き換えます。

CLI 出力に `runtimeVersion` フィールドが表示されます。 次の出力例のようになります。ここでは、わかりやすくするために抜粋されています。 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>バージョン エンドポイントから

アプリで /.auth/version エンドポイントをクリックして、アプリが実行されている現在のミドルウェア バージョンを表示することもできます。 次の出力例のようになります。
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>現在のランタイム バージョンの更新

Azure CLI を使用すると、[az webapp auth update](/cli/azure/webapp/auth#az_webapp_auth_update) コマンドでアプリの `runtimeVersion` 設定を更新できます。

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

`<my_app_name>` をご自分のアプリの名前に置き換えます。 また、`<my_resource_group>` をアプリのリソース グループの名前に置き換えます。 また、`<version>` を 1.x ランタイムの有効なバージョン、または最新バージョンの `~1` に置き換えます。 [さまざまなランタイム バージョンのリリース ノート](https://github.com/Azure/app-service-announcements)を参照して、ピン留めするバージョンの決定に役立ててください。

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure/reference-index#az_login)を実行してサインインした後に、このコマンドを実行することもできます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:エンドツーエンドでのユーザーの認証と承認](tutorial-auth-aad.md)
