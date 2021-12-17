---
title: カスタム ポリシーを使用してパスワードの変更を設定する
titleSuffix: Azure AD B2C
description: ユーザーが Azure Active Directory B2C でパスワードを変更できるようカスタム ポリシーを設定する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 16295eb707968a606c74813f9f6b7585aaf59546
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570623"
---
# <a name="set-up-password-change-by-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの変更を設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) を構成して、ローカル アカウントでサインインしているユーザーが、本人証明のためのメール確認を使用せずにパスワードを変更できるようにすることができます。 

パスワード変更フローでは、次の手順を行います。

1. ユーザーはローカル アカウントにサインインします。 セッションがまだアクティブである場合、Azure AD B2C によってユーザーが認可され、次の手順にスキップします。
1. **[古いパスワード]** では、ユーザーは古いパスワードを確認します。 **[新しいパスワード]** では、新しいパスワードを作成して確認します。

   ![パスワードを変更する 2 つの番号付きダイアログを示すスクリーンショット。](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> ユーザーは、自分のパスワードを知っていて、パスワードを変更したい場合にのみ、この記事で説明されているパスワード変更フローを使用できます。 ユーザーがパスワードを忘れた場合に備えて、[セルフサービス パスワード リセット](add-password-reset-policy.md)を有効にしておくことをお勧めします。

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>前提条件

* 「[Active Directory B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)」にある手順を完了する。
* [Azure Active Directory B2C に Web アプリケーションを登録する](tutorial-register-applications.md)。

## <a name="add-the-elements"></a>要素を追加する

1. *TrustFrameworkExtensions.xml* ファイルを開きます。 次の **ClaimType** 要素を、[ClaimsSchema](claimsschema.md) 要素に追加します。識別子は `oldPassword` にします。

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

1. [ClaimsProvider](claimsproviders.md) 要素には、ユーザーを認証する技術プロファイルが含まれています。 **ClaimsProviders** 要素に次の要素プロバイダーを追加します。

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

1. [UserJourneys](userjourneys.md) 要素は、ユーザーがアプリケーションとやり取りするときにたどるパスを定義します。 **UserJourneys** 要素が存在しない場合は追加します。**UserJourney** 識別子は `PasswordChange` にします。

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

1. *TrustFrameworkExtensions.xml* ポリシー ファイルを保存します。
1. スターター パックと一緒にダウンロードした *ProfileEdit.xml* ファイルをコピーして、*ProfileEditPasswordChange.xml* という名前を付けます。
1. 新しいファイルを開き、**PolicyId** 属性を一意の値で更新します。 この値がポリシーの名前になります。 たとえば、*B2C_1A_profile_edit_password_change* です。
1. **DefaultUserJourney** の **ReferenceId** 属性を変更して、作成した新しいユーザー体験の ID と一致するようにします。 たとえば、*PasswordChange* です。
1. 変更を保存します。

## <a name="upload-and-test-the-policy"></a>ポリシーをアップロードしてテストします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ポータルのツールバーにある **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択して、Azure AD B2C テナントを含むディレクトリを使っていることを確認します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシー]** で、 **[ポリシーのアップロード]** 選択します。
1. **[ポリシーが存在する場合は上書きする]** を選択し、*TrustFrameworkExtensions.xml* ファイルを検索して選択します。
1. **[アップロード]** を選択します。
1. その証明書利用者ファイル (*ProfileEditPasswordChange.xml* など) で、手順 5 から 7 を繰り返します。

## <a name="run-the-policy"></a>ポリシーを実行する

1. 変更したポリシーを開きます。 たとえば、*B2C_1A_profile_edit_password_change* です。
1. **[アプリケーション]** で、前に登録したアプリケーションを選択します。 トークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
1. **[今すぐ実行]** を選択します。 開いた新しいタブで、URL から "&prompt=login" を削除し、タブを更新します。次に、前に作成したアカウントを使用してサインインします。 パスワード変更ダイアログには、パスワードを変更するオプションが表示されます。

## <a name="next-steps"></a>次のステップ

* [サンプル ポリシーは、GitHub で確認](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)できます。
* [Azure AD B2C で複雑なパスワードを構成する](password-complexity.md)方法について説明します。
* [パスワードのリセット フロー](add-password-reset-policy.md)を設定します。

::: zone-end
