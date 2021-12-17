---
title: OpenID Connect プロバイダーを構成する (プレビュー)
description: App Service または Azure Functions アプリの ID プロバイダーとして OpenID Connect プロバイダーを構成する方法について説明します。
ms.topic: article
ms.date: 10/20/2021
ms.reviewer: mahender
ms.openlocfilehash: 0536d28ec91ada939a1ee0b3b7ecf6bf5e621a7a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236502"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>OpenID Connect プロバイダーを使用してログインするように App Service または Azure Functions アプリを構成する (プレビュー)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、[OpenID Connect の仕様](https://openid.net/connect/)に準拠したカスタム認証プロバイダーを使用するように Azure App Service または Azure Functions を構成する方法について説明します。 OpenID Connect (OIDC) は、多くの ID プロバイダー (IDP) で使用されている業界標準です。 準拠した IDP を使用するようにアプリを構成するために、仕様の詳細を理解する必要はありません。

1 つ以上の OIDC プロバイダーを使用するようにアプリを構成できます。 構成でそれぞれに一意の英数字の名前を付ける必要があり、既定のリダイレクト ターゲットとして機能できるのは 1 つだけです。

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>アプリケーションを ID プロバイダーに登録する

プロバイダーにより、アプリケーションの詳細を登録することを求められます。 これらの手順の 1 つに、リダイレクト URI の指定が含まれます。 このリダイレクト URI は、`<app-url>/.auth/login/<provider-name>/callback` という形式です。 各 ID プロバイダーから、これらの手順を完了するための詳細な方法が提供されているはずです。

> [!NOTE]
> プロバイダーによっては、構成と提供する値の使用方法について追加の手順が必要になる場合があります。 たとえば、Apple が提供する秘密キーは、それ自体は OIDC クライアント シークレットとしては使用されませんが、代わりに、それを使用して、アプリ構成で指定したシークレットとして扱われる JWT を作成する必要があります ([Apple でサインインのドキュメント](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)の「Creating the Client Secret」 (クライアント シークレットの作成) セクションを参照)。
>

アプリケーションの **クライアント ID** と **クライアント シークレット** を収集する必要があります。

> [!IMPORTANT]
> クライアント シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
>

さらに、プロバイダーの OpenID Connect メタデータも必要になります。 これは、多くの場合、プロバイダーの発行者の URL の末尾に `/.well-known/openid-configuration` が付いた[構成メタデータ ドキュメント](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)を介して公開されます。 この構成 URL を収集します。

構成メタデータ ドキュメントを使用できない場合は、次の値を個別に収集する必要があります。

- 発行者の URL (`issuer` として表示されることがあります)
- [OAuth 2.0 認証エンドポイント](https://tools.ietf.org/html/rfc6749#section-3.1) (`authorization_endpoint` として表示されることがあります)
- [OAuth 2.0 トークン エンドポイント](https://tools.ietf.org/html/rfc6749#section-3.2) (`token_endpoint` として表示されることがあります)
- [OAuth 2.0 JSON Web Key Set](https://tools.ietf.org/html/rfc8414#section-2) ドキュメントの URL (`jwks_uri` として表示されることがあります)

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>アプリケーションにプロバイダー情報を追加する

1. [Azure portal] にサインインして、アプリに移動します。
1. 左側のメニューで **[認証]** を選択します。 **[ID プロバイダーの追加]** をクリックします。
1. ID プロバイダーのドロップダウンから **[OpenID Connect]** を選びます。
1. 前に **OpenID プロバイダー名** で選んだ一意の英数字名を指定します。
1. **メタデータ ドキュメント** の URL が ID プロバイダーから提供されている場合は、 **[メタデータ URL]** にその値を指定します。 そうでない場合は、 **[エンドポイントを個別に指定する]** オプションを選んで、ID プロバイダーから集められた各 URL を適切なフィールドに入力します。
1. 前に収集した **クライアント ID** と **クライアント シークレット** を適切なフィールドに入力します。
1. クライアント シークレットのアプリケーション設定名を指定します。 クライアント シークレットはアプリ設定として保存され、シークレットは安全な方法で確実に格納されます。 Azure Key Vault でシークレットを管理する場合は、[Key Vault 参照](./app-service-key-vault-references.md)を使用するように、この設定を後で更新することができます。
1. **[追加]** ボタンを選んで、ID プロバイダーの設定を完了します。 

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
