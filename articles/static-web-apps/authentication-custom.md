---
title: Azure Static Web Apps でのカスタム認証
description: Azure Static Web Apps のカスタム認証を構成する方法について説明します
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: e4583c6474872cc1de909d86d812aa9ac9630536
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854578"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Azure Static Web Apps でのカスタム認証

Azure Static Web Apps が提供する[マネージド認証](authentication-authorization.md)では、Azure が管理するプロバイダー登録が使用されます。 登録の柔軟性を高めるため、既定値をカスタム登録でオーバーライドできます。

- またカスタム認証により、[OpenID Connect](https://openid.net/connect/) をサポートする[カスタム プロバイダーを構成](#configure-a-custom-openid-connect-provider)できます。 この構成では、複数の外部プロバイダーを登録できます。

- カスタム登録を使用すると、事前構成済みのすべてのプロバイダーが無効になります。

- Azure Active Directory (AAD) 登録でのみ、テナントを指定するオプションがあります。これにより、グループ管理の[招待フロー](./authentication-authorization.md#role-management)をバイパスできます。

> [!NOTE]
> カスタム認証は、Azure Static Web Apps Standard プランでのみ使用できます。

## <a name="override-pre-configured-provider"></a>事前構成済みプロバイダーをオーバーライドする

プロバイダーのオーバーライドに使用する設定は、[構成ファイル](configuration.md)の `auth` セクションで構成されます。

ソース管理にシークレットが入り込まないようにするため、構成では[アプリケーション設定](application-settings.md)が調べられ、構成ファイルに一致する名前があるかどうかが確認されます。 また、[Azure Key Vault](./key-vault-secrets.md) にシークレットを格納することも選択できます。

### <a name="configuration"></a>構成

次の表に、プロバイダー別の各種構成オプションを示します。

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

| フィールド パス                             | 説明                                                                                                               |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `registration.openIdIssuer`            | AAD テナントの OpenID 構成のエンドポイント。                                                  |
| `registration.clientIdSettingName`     | Azure AD アプリ登録のアプリケーション (クライアント) ID を含むアプリケーション設定の名前。 |
| `registration.clientSecretSettingName` | Azure AD アプリ登録のクライアント シークレットを含むアプリケーション設定の名前。           |

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "<AAD_CLIENT_ID>",
          "clientSecretSettingName": "<AAD_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Azure Active Directory のバージョン管理されているエンドポイントは、登録の構成方法に影響します。 AAD v1 を使用している場合 (発行者エンドポイントの末尾が "/v2.0" ではない)、`"azureActiveDirectory"` オブジェクトの構成に次の `userDetailsClaim` エントリを追加する必要があります。

```json
"azureActiveDirectory": {
  "registration": { ... },
  "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" 
}
```

Azure Active Directory を構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-aad.md)に関する記事を参照してください。

# <a name="apple"></a>[Apple](#tab/apple)

| フィールド パス                             | 説明                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | クライアント ID を含むアプリケーション設定の名前。                                       |
| `registration.clientSecretSettingName` | クライアント シークレットを含むアプリケーション設定の名前。                                   |

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "<APPLE_CLIENT_ID>",
          "clientSecretSettingName": "<APPLE_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Apple を認証プロバイダーとしてを構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-apple.md)に関する記事を参照してください。

# <a name="facebook"></a>[Facebook](#tab/facebook)

| フィールド パス                          | 説明                                                                            |
| ----------------------------------- | -------------------------------------------------------------------------------------- |
| `registration.appIdSettingName`     | アプリ ID を含むアプリケーション設定の名前。                             |
| `registration.appSecretSettingName` | アプリ シークレットを含むアプリケーション設定の名前。                         |

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "<FACEBOOK_APP_ID>",
          "appSecretSettingName": "<FACEBOOK_APP_SECRET>"
        }
      }
    }
  }
}
```

Facebook を認証プロバイダーとしてを構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-facebook.md)に関する記事を参照してください。

# <a name="github"></a>[GitHub](#tab/github)

| フィールド パス                             | 説明                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | クライアント ID を含むアプリケーション設定の名前。                                |
| `registration.clientSecretSettingName` | クライアント シークレットを含むアプリケーション設定の名前。                            |

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "<GITHUB_CLIENT_ID>",
          "clientSecretSettingName": "<GITHUB_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)

| フィールド パス                             | 説明                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | クライアント ID を含むアプリケーション設定の名前。                                |
| `registration.clientSecretSettingName` | クライアント シークレットを含むアプリケーション設定の名前。                            |

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "<GOOGLE_CLIENT_ID>",
          "clientSecretSettingName": "<GOOGLE_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Google を認証プロバイダーとしてを構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-google.md)に関する記事を参照してください。

# <a name="twitter"></a>[Twitter](#tab/twitter)

| フィールド パス                               | 説明                                                                                        |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `registration.consumerKeySettingName`    | コンシューマー キーを含むアプリケーション設定の名前。                                   |
| `registration.consumerSecretSettingName` | コンシューマー シークレットを含むアプリケーション設定の名前。                                |

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "<TWITTER_CONSUMER_KEY>",
          "consumerSecretSettingName": "<TWITTER_CONSUMER_SECRET>"
        }
      }
    }
  }
}
```

Twitter を認証プロバイダーとしてを構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-twitter.md)に関する記事を参照してください。

---

## <a name="configure-a-custom-openid-connect-provider"></a>カスタム OpenID Connect プロバイダーを構成する

このセクションでは、[OpenID Connect (OIDC) 仕様](https://openid.net/connect/)に準拠するカスタム認証プロバイダーを使用するように Azure Static Web Apps を構成する方法について説明します。 カスタム OIDC プロバイダーを使用するには、次の手順が必要です。

- 1 つ以上の OIDC プロバイダーを使用できます。
- 各プロバイダーには、構成内で一意の名前が必要です。
- 既定のリダイレクト ターゲットとして使用できるプロバイダーは 1 つのみです。

### <a name="register-your-application-with-the-identity-provider"></a>アプリケーションを ID プロバイダーに登録する

アプリケーションの詳細を ID プロバイダーに登録する必要があります。 アプリケーションの **クライアント ID** と **クライアント シークレット** を生成するために必要な手順については、プロバイダーにお問い合わせください。

> [!IMPORTANT]
> アプリケーション シークレットは機密性の高いセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配布したり、ソース管理にチェックインしたりしないでください。

登録資格情報を取得したら、次の手順に従ってカスタム登録を作成します。

1. 任意の設定名を使用して、クライアント ID とクライアント シークレットをアプリの[アプリケーション設定](application-settings.md)として追加します。 後で使用するために、これらの名前をメモします。 あるいは、構成ファイルにクライアント ID を含めることができます。

1. プロバイダーの OpenID Connect メタデータが必要になります。 この情報は、多くの場合、プロバイダーの "_発行者の URL_" の末尾に `/.well-known/openid-configuration` が付いた[構成メタデータ ドキュメント](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)を介して公開されます。 この構成 URL を収集します。

1. [構成ファイル](configuration.md)の `auth` セクションに、OICD プロバイダーの構成ブロックとプロバイダー定義を追加します。

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "<MY_PROVIDER_CLIENT_ID_SETTING_NAME>",
               "clientCredential": {
                 "clientSecretSettingName": "<MY_PROVIDER_CLIENT_SECRET_SETTING_NAME>"
               },
               "openIdConnectConfiguration": {
                 "wellKnownOpenIdConfiguration": "https://<PROVIDER_ISSUER_URL>/.well-known/openid-configuration"
               }
             },
             "login": {
               "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
               "scopes": [],
               "loginParameterNames": []
             }
           }
         }
       }
     }
   }
   ```

  コード内の次の置換トークンを実際の値に変更します。

  | 置換するトークン | 置換後の値 |
  | --- | --- |
  | `<MY_PROVIDER_CLIENT_ID_SETTING_NAME>` | カスタム登録から生成されたクライアント ID に関連付けられているアプリケーション設定名。 |
  | `<MY_PROVIDER_CLIENT_SECRET_SETTING_NAME>` | カスタム登録から生成されたクライアント シークレットに関連付けられているアプリケーション設定名。 |
  | `<PROVIDER_ISSUER_URL>` | プロバイダーの "_発行者 URL_" のパス。 |

- プロバイダー名 (この例では `myProvider`) は、Azure Static Web Apps によって使用される一意の識別子です。
- `login` オブジェクトを使用すると、カスタム スコープ、ログイン パラメーター、またはカスタム要求の値を指定できます。

### <a name="login-logout-and-purging-user-details"></a>ログイン、ログアウト、およびユーザーの詳細の削除

カスタム OIDC プロバイダーを使用するには、次の URL パターンを使用します。

| アクション             | [パターン]                                  |
| ------------------ | ---------------------------------------- |
| ログイン              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| ユーザーの詳細の削除 | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

### <a name="authentication-callbacks"></a>認証コールバック

認証プロバイダーでは、ログインまたはログアウトの要求を完了するためにリダイレクト URL が必要です。 次のエンドポイントは、リダイレクト先として使用できます。

| 種類   | URL パターン                                                 |
| ------ | ----------------------------------------------------------- |
| ログイン  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

> [!Note]
> これらの URL は、認証プロバイダーからの応答を受信するために Azure Static Web Apps により提供されます。これらのルートでページを作成する必要はありません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Key Vault 内の認証シークレットの保護](./key-vault-secrets.md)
