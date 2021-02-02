---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674239"
---
## <a name="configure-the-relying-party-policy"></a>証明書利用者ポリシーを構成する

証明書利用者ポリシー (例 [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)) は、Azure AD B2C が実行されるユーザー体験を指定します。 [証明書利用者](../articles/active-directory-b2c/relyingparty.md)内の **DefaultUserJourney** 要素を検索します。 ID プロバイダーを追加したユーザー体験 ID と一致するように **ReferenceId** を更新します。 

次の例では、`CustomSignUpOrSignIn` ユーザー体験について、**ReferenceId** を `CustomSignUpOrSignIn` に設定しています。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>カスタム ポリシーをアップロードする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択し、変更した 2 つのポリシー ファイルを拡張ポリシー (`TrustFrameworkExtensions.xml` など)、証明書利用者ポリシー (`SignUpSignIn.xml`など) の順序でアップロードします。

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します
1. **[アプリケーション]** には、前に登録した Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

