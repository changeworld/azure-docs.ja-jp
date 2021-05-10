---
title: カスタム ポリシーを使用してパスワードの変更を構成する
titleSuffix: Azure AD B2C
description: ユーザーが Azure Active Directory B2C でカスタム ポリシーを使用してパスワードを変更できるようにする方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: cd63144074577d4ff3564da41e672dd1ca226dcb
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257162"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの変更を構成する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) では、ローカル アカウントでサインインしているユーザーが、電子メールでの確認による ID の証明なしでパスワードを変更できます。 パスワード変更フローでは、次の手順を行います。

1. ユーザーはローカル アカウントにサインインします。 セッションがまだアクティブである場合、Azure AD B2C によってユーザーが認可され、次の手順にスキップします。
1. ユーザーは **古いパスワード** を確認し、**新しいパスワード** を作成して確認します。

![パスワード変更フロー](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> パスワード変更フローを使用すると、ユーザーがパスワードを知っていて、パスワードの変更を望む場合にのみ、パスワードを変更できます。 ユーザーがパスワードを忘れた場合に備えて、[セルフサービス パスワード リセット](add-password-reset-policy.md)を有効にしておくことをお勧めします。

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>前提条件

* 「[Active Directory B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)」にある手順を完了する。
* まだそうしていない場合は、[Azure Active Directory B2C に Web アプリケーションを登録](tutorial-register-applications.md)します。

## <a name="add-the-elements"></a>要素を追加する

1. ご自身の *TrustframeworkExtensions.xml* ファイルを開き、識別子が `oldPassword` の次の **ClaimType** 要素を、[ClaimsSchema](claimsschema.md) 要素に追加します。

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. [ClaimsProvider](claimsproviders.md) 要素には、ユーザーを認証する技術プロファイルが含まれています。 **ClaimsProviders** 要素に次の要素プロバイダーを追加します。

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

3. [UserJourney](userjourneys.md) 要素は、ユーザーがアプリケーションとやり取りするときに取るパスを定義します。 `PasswordChange` として識別される **UserJourney** に存在していない場合は、**UserJourneys** 要素を追加します。

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. *TrustFrameworkExtensions.xml* ポリシー ファイルを保存します。
5. スターター パックと一緒にダウンロードした *ProfileEdit.xml* ファイルをコピーして、*ProfileEditPasswordChange.xml* という名前を付けます。
6. 新しいファイルを開き、**PolicyId** 属性を一意の値で更新します。 この値がポリシーの名前になります。 たとえば、*B2C_1A_profile_edit_password_change* です。
7. `<DefaultUserJourney>` の **ReferenceId** 属性を変更して、作成した新しいユーザー体験の ID と一致するようにします。 たとえば、*PasswordChange* です。
8. 変更を保存します。

## <a name="upload-and-test-the-policy"></a>ポリシーをアップロードしてテストします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. [カスタム ポリシー] ページで、 **[ポリシーのアップロード]** をクリックします。
6. **[ポリシーが存在する場合は上書きする]** を選択し、*TrustframeworkExtensions.xml* ファイルを検索して選択します。
7. **[アップロード]** をクリックします。
8. その証明書利用者ファイル (*ProfileEditPasswordChange.xml* など) で、手順 5 から 7 を繰り返します。

### <a name="run-the-policy"></a>ポリシーを実行する

1. 変更したポリシーを開きます。 たとえば、*B2C_1A_profile_edit_password_change* です。
2. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 トークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
3. **[今すぐ実行]** をクリックします。 前に作成したアカウントでサインインします。 これで、パスワードを変更することができます。

## <a name="next-steps"></a>次のステップ

- サンプル ポリシーは、[GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change) で確認できます。
- [Azure AD B2C で複雑なパスワードを構成する](password-complexity.md)方法について説明します。
- [パスワードのリセット フロー](add-password-reset-policy.md)を設定します。

::: zone-end
