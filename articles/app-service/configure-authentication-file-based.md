---
title: AuthN/AuthZ のファイルベースの構成
description: 構成ファイルを使用して App Service で認証と認可を構成し、特定のプレビュー機能を有効にします。
ms.topic: article
ms.date: 07/15/2021
ms.openlocfilehash: 990ce0caf05134d173afb7325682d05d9feb14ea
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047344"
---
# <a name="file-based-configuration-in-azure-app-service-authentication"></a>Azure App Service 認証でのファイルベースの構成

[App Service 認証](overview-authentication-authorization.md)では、ファイルを使用して認証設定を構成できます。 [Azure Resource Manager](../azure-resource-manager/management/overview.md) API を使用して公開する前に、ファイルベースの構成を使用して App Service 認証/認可の特定のプレビュー機能を使用することが必要になる場合があります。

> [!IMPORTANT]
> アプリのペイロード、つまりこのファイルは、[スロット](./deploy-staging-slots.md)と同様に環境間を移動する場合があることに注意してください。 各スロットに異なるアプリ登録を固定したい場合はよくありますが、このような場合は、構成ファイルを使用する代わりに、標準の構成方法を使用し続ける必要があります。

## <a name="enabling-file-based-configuration"></a>ファイルベースの構成の有効化

1. 構成用の新しい JSON ファイルをプロジェクトのルートに作成します (Web/関数アプリでは D:\home\site\wwwroot にデプロイされます)。 [ファイルベースの構成リファレンス](#configuration-file-reference)に従って、必要な構成を入力します。 既存の Azure Resource Manager 構成を変更する場合は、必ず、`authsettings` コレクションにキャプチャされたプロパティを構成ファイルに変換してください。

2. [Azure Resource Manager](../azure-resource-manager/management/overview.md) API で `Microsoft.Web/sites/<siteName>/config/authsettingsV2` にキャプチャされた既存の構成を変更します。 これを変更するには、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)や、[Azure Resource Explorer](https://resources.azure.com/) などのツールを使用できます。 authsettingsV2 コレクション内で、2 つのプロパティを設定する必要があります (他のものは削除してもかまいません)。

    1. `platform.enabled` を "true" に設定します
    2. `platform.configFilePath` をファイルの名前に設定します ("auth.json" など)

> [!NOTE]
> `platform.configFilePath` の形式は、プラットフォームによって異なります。 Windows では、相対パスと絶対パスの両方がサポートされます。 相対パスを使用することをお勧めします。 Linux では、絶対パスのみが現在サポートされているため、設定の値は "/home/site/wwwroot/auth.json" または同様の値にする必要があります。

この構成の更新を行うと、ファイルの内容を使用して、そのサイトでの App Service の認証または承認の動作が定義されます。 Azure Resource Manager 構成に戻りたい場合、設定 `platform.configFilePath` を null に変更して削除すると、戻ることができます。

## <a name="configuration-file-reference"></a>構成ファイル リファレンス

構成ファイルから参照されるシークレットがある場合、[アプリケーション設定](./configure-common.md#configure-app-settings)として保存する必要があります。 設定には任意の名前を付けることができます。 構成ファイルからの参照で同じキーを使用していることを確認してください。

次の例では、ファイル内の可能な構成オプションを使い果たしています。

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|RejectWith401|RejectWith404",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="more-resources"></a>その他のリソース

- [チュートリアル:エンドツーエンドでのユーザーの認証と承認](tutorial-auth-aad.md)
- [認証用の環境変数とアプリ設定](reference-app-settings.md#authentication--authorization)
