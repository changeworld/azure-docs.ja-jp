---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: kengaderdus
ms.openlocfilehash: e840d71b813432829a4eba4a33d50ff2b664222e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066325"
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



