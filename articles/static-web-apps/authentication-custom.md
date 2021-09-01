---
title: Azure Static Web Apps でのカスタム認証
description: Azure Static Web Apps のカスタム認証を構成する方法について説明します
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: b09d1f6d6cdd5838f4c43e7cb05f63d8efd3e7f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723407"
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

カスタム認証を設定するには、[アプリケーション設定](./application-settings.md)として格納されているいくつかのシークレットを参照する必要があります。 

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

Azure Active Directory プロバイダーは、2 つの異なるバージョンで提供されています。 バージョン 1 では、ペイロードでユーザー情報を返すことができると、`userDetailsClaim` が明示的に定義されています。 これに対してバージョン 2 では、既定でユーザー情報が返されます。それは `openIdIssuer` URL 内の `v2.0` によって指定されます。

登録を作成するには、以下のアプリケーション設定の作成から始めます。

| 設定名 | 値 |
| --- | --- |
| `AAD_CLIENT_ID` | Azure AD アプリ登録用のアプリケーション (クライアント) ID。 |
| `AAD_CLIENT_SECRET` | Azure AD アプリ登録用のクライアント シークレット。 |

#### <a name="azure-active-directory-version-1"></a>Azure Active Directory バージョン 1

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

`<TENANT_ID>` は、必ず Azure Active Directory テナント ID に置き換えます。

#### <a name="azure-active-directory-version-2"></a>Azure Active Directory バージョン 2

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>/v2.0",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

`<TENANT_ID>` は、必ず Azure Active Directory テナント ID に置き換えます。

Azure Active Directory を構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-aad.md)に関する記事を参照してください。

> [!NOTE]
> Azure Active Directory の構成セクションは `azureActiveDirectory` ですが、プラットフォームでは、ログイン、ログアウト、ユーザー情報の消去のために、これを URL 内の `aad` にエイリアス化します。 詳細については、[認証と承認](authentication-authorization.md)に関するセクションを参照してください。

# <a name="apple"></a>[Apple](#tab/apple)

登録を作成するには、以下のアプリケーション設定の作成から始めます。

| 設定名 | 値 |
| --- | --- |
| `APPLE_CLIENT_ID` | Apple クライアント ID。 |
| `APPLE_CLIENT_SECRET` | Apple クライアントのシークレット。 |

次に、下記のサンプルを使用してプロバイダーを構成します。

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "APPLE_CLIENT_ID",
          "clientSecretSettingName": "APPLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Apple を認証プロバイダーとしてを構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-apple.md)に関する記事を参照してください。

# <a name="facebook"></a>[Facebook](#tab/facebook)

登録を作成するには、以下のアプリケーション設定の作成から始めます。

| 設定名 | 値 |
| --- | --- |
| `FACEBOOK_APP_ID` | Facebook アプリケーション ID。 |
| `FACEBOOK_APP_SECRET` | Facebook アプリケーションのシークレット。 |

次に、下記のサンプルを使用してプロバイダーを構成します。

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "FACEBOOK_APP_ID",
          "appSecretSettingName": "FACEBOOK_APP_SECRET"
        }
      }
    }
  }
}
```

Facebook を認証プロバイダーとしてを構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-facebook.md)に関する記事を参照してください。

# <a name="github"></a>[GitHub](#tab/github)


登録を作成するには、以下のアプリケーション設定の作成から始めます。

| 設定名 | 値 |
| --- | --- |
| `GITHUB_CLIENT_ID` | GitHub クライアント ID。 |
| `GITHUB_CLIENT_SECRET` | GitHub クライアントのシークレット。 |

次に、下記のサンプルを使用してプロバイダーを構成します。

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "GITHUB_CLIENT_ID",
          "clientSecretSettingName": "GITHUB_CLIENT_SECRET"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)


登録を作成するには、以下のアプリケーション設定の作成から始めます。

| 設定名 | 値 |
| --- | --- |
| `GOOGLE_CLIENT_ID` | Google クライアント ID。 |
| `GOOGLE_CLIENT_SECRET` | Google クライアントのシークレット。 |

次に、下記のサンプルを使用してプロバイダーを構成します。

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "GOOGLE_CLIENT_ID",
          "clientSecretSettingName": "GOOGLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Google を認証プロバイダーとしてを構成する方法の詳細については、[App Service の認証/認可](../app-service/configure-authentication-provider-google.md)に関する記事を参照してください。

# <a name="twitter"></a>[Twitter](#tab/twitter)

登録を作成するには、以下のアプリケーション設定の作成から始めます。

| 設定名 | 値 |
| --- | --- |
| `TWITTER_CONSUMER_KEY` | Twitter のコンシューマー キー。 |
| `TWITTER_CONSUMER_SECRET` | Twitter のコンシューマー シークレット。 |

次に、下記のサンプルを使用してプロバイダーを構成します。

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "TWITTER_CONSUMER_KEY",
          "consumerSecretSettingName": "TWITTER_CONSUMER_SECRET"
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

アプリケーションが ID プロバイダーに登録されたら、静的 Web アプリの[アプリケーション設定](application-settings.md)内に、以下のアプリケーション シークレットを作成します。

| 設定名 | 値 |
| --- | --- |
| `MY_PROVIDER_CLIENT_ID` | お使いの静的 Web アプリの認証プロバイダーによって生成されたクライアント ID。 |
| `MY_PROVIDER_CLIENT_SECRET` | お使いの静的 Web アプリの認証プロバイダーのカスタム登録によって生成されたクライアント シークレット。 |

追加のプロバイダーを登録する場合は、それぞれのアプリケーション設定内に、関連付けられるクライアント ID とクライアント シークレット ストアが必要です。

> [!IMPORTANT]
> アプリケーション シークレットは機密性の高いセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配布したり、ソース管理にチェックインしたりしないでください。

登録資格情報を取得したら、次の手順に従ってカスタム登録を作成します。

1. プロバイダーの OpenID Connect メタデータが必要になります。 この情報は、多くの場合、プロバイダーの "_発行者の URL_" の末尾に `/.well-known/openid-configuration` が付いた [構成メタデータ ドキュメント](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)を介して公開されます。 この構成 URL を収集します。

1. [構成ファイル](configuration.md)の `auth` セクションに、OICD プロバイダーの構成ブロックとプロバイダー定義を追加します。

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "MY_PROVIDER_CLIENT_ID",
               "clientCredential": {
                 "clientSecretSettingName": "MY_PROVIDER_CLIENT_SECRET"
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

  - プロバイダー名 (この例では `myProvider`) は、Azure Static Web Apps によって使用される一意の識別子です。
  - `<PROVIDER_ISSUER_URL>` は、必ず、プロバイダーの "_発行者 URL_" のパスに置き換えます。
  - `login` オブジェクトを使用すると、カスタム スコープ、ログイン パラメーター、またはカスタム要求の値を指定できます。

### <a name="login-logout-and-purging-user-details"></a>ログイン、ログアウト、およびユーザーの詳細の削除

カスタム OIDC プロバイダーを使用するには、次の URL パターンを使用します。

| アクション             | [パターン]                                  |
| ------------------ | ---------------------------------------- |
| ログイン              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| ユーザーの詳細の削除 | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

Azure Active Directory を使おうとしている場合は、`<AUTHENTICATION_PROVIDER_NAME>` プレースホルダーの値として `aad` を使用します。

### <a name="authentication-callbacks"></a>認証コールバック

カスタム OIDC プロバイダーでは、ログインまたはログアウトの要求を完了するためにリダイレクト URL が必要です。 次のエンドポイントは、リダイレクト先として使用できます。

| 種類   | URL パターン                                                 |
| ------ | ----------------------------------------------------------- |
| ログイン  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

Azure Active Directory を使おうとしている場合は、`<AUTHENTICATION_PROVIDER_NAME>` プレースホルダーの値として `aad` を使用します。

> [!Note]
> これらの URL は、認証プロバイダーからの応答を受信するために Azure Static Web Apps により提供されます。これらのルートでページを作成する必要はありません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Key Vault 内の認証シークレットの保護](./key-vault-secrets.md)
