---
title: Azure Active Directory B2C を使用して Python Web アプリケーション オプションを有効にする
description: この記事では、Python Web アプリケーション オプションの使用を有効にする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 155e09cc0aeda6e9a93358f25ca44d852fd34ea7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041065"
---
# <a name="enable-authentication-options-in-a-python-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して Python Web アプリの認証オプションを有効にする 

この記事では、Python Web アプリケーションの Azure Active Directory B2C (Azure AD B2C) 認証エクスペリエンスの有効化、カスタマイズ、および拡張を行う方法について説明します。 

開始する前に、[Azure AD B2C を使用してサンプル Python Web アプリで認証を構成する](configure-authentication-sample-python-web-app.md)方法について理解しておくことが重要です。

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

認証 URL でカスタム ドメインとテナント ID を使用するには、次の操作を行います。 

1. [カスタム ドメインの有効化](custom-domain.md)に関する記事のガイダンスに従います。
1. *app_config.py* ファイル内の `authority_template` クラス メンバーをカスタム ドメインで更新します。

次の Python コードは、変更前のアプリ設定を示しています。

```python
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"
```

次の Python コードは、変更後のアプリ設定を示しています。

```python
authority_template = "https://custom.domain.com/00000000-0000-0000-0000-000000000000/{user_flow}" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. カスタム ポリシーを使用している場合は、[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)に関する記事の説明に従って、必要な入力要求を追加します。 
1. `initiate_auth_code_flow` メソッドを見つけてから、ID プロバイダーのドメイン名 (*facebook.com* など) を指定した `login_hint` パラメーターを追加します。

```python
def _build_auth_code_flow(authority=None, scopes=None):
    return _build_msal_app(authority=authority).initiate_auth_code_flow(
        scopes or [],
        redirect_uri=url_for("authorized", _external=True),
        login_hint="bob@contoso.com")
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 外部 ID プロバイダーのドメイン名を確認します。 詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 
1. `initiate_auth_code_flow` メソッドを見つけてから、ログイン ヒントを指定した `domain_hint` パラメーターを追加します。

    ```python
    def _build_auth_code_flow(authority=None, scopes=None):
        return _build_msal_app(authority=authority).initiate_auth_code_flow(
            scopes or [],
            redirect_uri=url_for("authorized", _external=True),
            domain_hint="facebook.com")
    ```


## <a name="next-steps"></a>次のステップ

- 詳細については、[Python 用 MSAL の構成オプション](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki)に関するページを参照してください。
