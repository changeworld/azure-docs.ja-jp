---
title: パスワードのリセット フローを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C (Azure AD B2C) でパスワードのリセット フローを設定する方法を説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/24/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2d1c0dfd5a5fdb9573859c61421513c4dc91eb8e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130251207"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でパスワードのリセット フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[サインアップとサインインの流れ](add-sign-up-and-sign-in-policy.md)では、ユーザーは **[パスワードを忘れた場合]** リンクを使用することによって自分のパスワードをリセットできます。 このセルフサービス パスワード リセット フローは、サインインに[メール アドレス](sign-in-options.md#email-sign-in)または[ユーザー名](sign-in-options.md#username-sign-in)とパスワードを使用する Azure Active Directory B2C (Azure AD B2C) のローカル アカウントに適用されます。

パスワードのリセット フローでは、次の手順を実行します。

1. サインアップとサインインのページで **[パスワードを忘れた場合]** リンクを選択します。 Azure AD B2C によってパスワードのリセット フローが開始されます。
1. 次のダイアログ ボックスが表示されたら、ユーザーはメール アドレスを入力して、 **[確認コードを送信する]** を選択します。 Azure AD B2C によってユーザーのメール アカウントに確認コードが送信されます。 ユーザーは、メールから確認コードをコピーし、Azure AD B2C のパスワードのリセット ダイアログにそのコードを入力して、 **[コードの確認]** を選択します。
1. ユーザーは次に、新しいパスワードを入力できます。 (メールの確認後も、ユーザーは **[電子メールの変更]** ボタンを選択できます。「[電子メールの変更ボタンを非表示にする](#hide-the-change-email-button)」を参照してください。)

   ![パスワード リセット フロー内の 3 つのダイアログを示す図。](./media/add-password-reset-policy/password-reset-flow.png)

> [!TIP]
> ユーザーは、自分のパスワードを忘れてリセットしたい場合は、セルフサービス パスワード リセット フローを使用してパスワードを変更できます。 次のいずれかのユーザー フロー オプションを選択します。
> - ユーザーが自分のパスワードを知っていて、それを変更したい場合は、[パスワード変更フロー](add-password-change-policy.md)を使用します。
> - ユーザーにパスワードの変更を強制する場合は (たとえば、はじめてサインインするとき、管理者がユーザーのパスワードをリセットしたとき、Azure AD B2C にユーザーを移行してランダムなパスワードを割り当てたとき)、[パスワードのリセットの強制](force-password-reset.md)フローを使用します。

### <a name="hide-the-change-email-button"></a>電子メールの変更ボタンを非表示にする

メールの検証が完了した後でも、ユーザーは **[電子メールの変更]** を選択し、別のメール アドレスを入力して、メールの検証を繰り返すことができます。 **[電子メールの変更]** ボタンを非表示にしたい場合は、CSS を変更して、ダイアログの関連付けられた HTML 要素を非表示にすることができます。 たとえば、次の CSS エントリを selfAsserted.html に追加し、[HTML テンプレートを使用してユーザー インターフェイスをカスタマイズする](customize-ui-with-html.md)ことができます。

```html
<style type="text/css">
   .changeClaims
   {
     visibility: hidden;
   }
</style>
```

*selfAsserted.html* での **[電子メールの変更]** ボタンの既定の名前は **changeclaims** です。 ボタンの名前を確認するには、サインアップ ページで、Inspect などのブラウザー ツールを使用してページのソースを調べます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>セルフサービス パスワード リセット (推奨)

新しいパスワード リセット エクスペリエンスが、サインアップやサインインのポリシーの一部になりました。 ユーザーが **[パスワードを忘れた場合]** リンクを選択すると、すぐにパスワードを忘れた場合のエクスペリエンスが表示されます。 アプリケーションでは、[AADB2C90118 エラー コード](#password-reset-policy-legacy)を処理する必要がなくなりました。また、パスワード リセット用の別個のポリシーは必要ありません。

::: zone pivot="b2c-user-flow"

セルフサービス パスワード リセットのエクスペリエンスは、サインイン (推奨) またはサインアップとサインイン (推奨) のユーザー フローで構成できます。 どちらのユーザー フローの設定もない場合は、[サインインとサインアップ](add-sign-up-and-sign-in-policy.md)のユーザー フローを作成します。

サインアップまたはサインインのユーザー フロー用のセルフサービス パスワード リセットを設定するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルのツール バーで、 **[ディレクトリとサブスクリプション]** アイコンを選択します。
1. **[ポータルの設定 | ディレクトリとサブスクリプション]** ペインの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. カスタマイズする (種類が **[推奨]** である) サインアップまたはサインインのユーザー フローを選択します。
1. メニューの **[設定]** で、 **[プロパティ]** を選択します。
1. **[Password configuration]\(パスワードの構成\)** で、 **[セルフサービス パスワード リセット]** を選択します。
1. **[保存]** を選択します。
1. 左側のメニューで、 **[カスタマイズ]** の **[ページ レイアウト]** を選択します。
1. **[Page Layout Version]\(ページ レイアウトのバージョン\)** で、**2.1.3** 以降を選択します。
1. **[保存]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

以降のセクションでは、セルフサービス パスワード エクスペリエンスをカスタム ポリシーに追加する方法について説明します。 このサンプルは、[カスタム ポリシー スターター パック](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)に含まれるポリシー ファイルに基づいています。

> [!TIP]
> パスワード リセットを含むサインアップまたはサインイン ポリシーの完全なサンプルは、[GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset) にあります。

### <a name="indicate-that-a-user-selected-the-forgot-your-password-link"></a>ユーザーが [パスワードを忘れた場合] リンクを選択したことを示す

ユーザーが **[パスワードを忘れた場合]** リンクを選択したことをポリシーに示すには、ブール値のクレームを定義します。 ユーザー体験を、**パスワードを忘れた場合** の技術プロファイルに移動させるには、クレームを使用します。 このクレームをトークンに対して発行し、ユーザーがパスワードを忘れた場合のユーザー フローを使用してサインインしたことが、アプリケーションで検出されるようにすることもできます。

クレームは、[クレーム スキーマ](claimsschema.md)内で宣言します。 ポリシーの拡張ファイル (例: *SocialAndLocalAccounts/TrustFrameworkExtensions.xml*) を開きます。

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. 次の要求を **ClaimsSchema** 要素に追加します。

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="isForgotPassword">
          <DisplayName>isForgotPassword</DisplayName>
          <DataType>boolean</DataType>
          <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
        </ClaimType>
      <!--
      </ClaimsSchema>
    </BuildingBlocks> -->
    ```

### <a name="upgrade-the-page-layout-version"></a>ページ レイアウト バージョンをアップグレードする

サインアップまたはサインインの体験でセルフサービス パスワード リセットのフローを有効にするには、[ページ レイアウト バージョン](contentdefinitions.md#migrating-to-page-layout) 2.1.2 が必要です。 ページ レイアウトのバージョンをアップグレードするには:

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ContentDefinitions](contentdefinitions.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. **ContentDefinition** 要素内の **DataURI** 要素を ID `api.signuporsignin` に変更します。

    ```xml
    <!-- 
    <BuildingBlocks>
      <ContentDefinitions> -->
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
        </ContentDefinition>
      <!-- 
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

クレーム変換技術プロファイルによって、**isForgotPassword** クレームが開始されます。 技術プロファイルについては後で参照します。 呼び出されると、**isForgotPassword** クレームの値が `true` に設定されます。 **ClaimsProviders** 要素を見つけます。 要素が存在しない場合は追加します。 次に、下のクレーム プロバイダーを追加します。  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

**SelfAsserted-LocalAccountSignin-Email** 技術プロファイルの **setting.forgotPasswordLinkOverride** によって、ユーザー体験で実行されるパスワード リセット クレームの交換が定義されています。

### <a name="add-the-password-reset-sub-journey"></a>パスワード リセットのサブ体験を追加する

ユーザーは、ユーザー体験でサインイン、サインアップ、パスワードのリセットを実行できるようになりました。 ユーザー体験をより適切にまとめるには、[サブ体験](subjourneys.md)を使用してパスワード リセット フローを処理できます。

ユーザー体験から呼び出されたサブ体験により、ユーザーにパスワード リセット エクスペリエンスを提供する特定の手順が実行されます。 サブ経験が完了したらそのサブ経験を開始したオーケストレーション ステップに制御が返されるように、`Call` 型のサブ体験を使用します。

**SubJourneys** 要素を見つけます。 この要素が存在しない場合は、**User Journeys** 要素の後に追加します。 次に、下記のサブ体験を追加します。

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>ユーザー体験を準備する

次に、 **[パスワードを忘れた場合]** リンクを、パスワードを忘れた場合のサブ体験に接続します。 **CombinedSignInAndSignUp** ステップの **ClaimsProviderSelection** 要素で、パスワードを忘れた場合のサブ体験の ID を参照します。

**CombinedSignInAndSignUp** ステップを含む独自のカスタム ユーザー体験を用意していない場合は、次の手順のようにして、既存のサインアップまたはサインインのユーザー体験を複製します。 そうでない場合は、次の手順に進みます。

1. スターター パックの *TrustFrameworkBase.xml* ファイルを開きます。
1. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
1. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
1. 手順 2. でコピーした **UserJourney** 要素の内容全体を貼り付けて、**UserJourneys** 要素の子要素を作成します。
1. ユーザー体験の ID の名前を変更します。 たとえば、「 `Id="CustomSignUpSignIn"` 」のように入力します。

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>パスワードを忘れた場合のリンクを、パスワードを忘れた場合のサブ体験に接続する

ユーザー体験内では、パスワードを忘れた場合のサブ体験を **ClaimsProviderSelection** として表現できます。 この要素を追加して、 **[パスワードを忘れた場合]** リンクを、パスワードを忘れた場合のサブ体験に接続させます。

1. ユーザー体験内で、`Type="CombinedSignInAndSignUp"` または `Type="ClaimsProviderSelection"` を含むオーケストレーション ステップ要素を見つけます。 これは通常、最初のオーケストレーション ステップです。 **ClaimsProviderSelections** 要素には、ユーザーがサインインに使用できる ID プロバイダーの一覧が含まれています。 次の行を追加します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. 次のオーケストレーション ステップで、**ClaimsExchange** 要素を追加します。 次の行を追加します。

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```

1. 現在のステップと次のステップの間に、次のオーケストレーション ステップを追加します。 追加した新しいオーケストレーション ステップによって、**isForgotPassword** クレームが存在するかどうかが確認されます。 要求が存在する場合は、[パスワード リセットのサブ体験](#add-the-password-reset-sub-journey)が呼び出されます。 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```

1. 新しいオーケストレーション ステップを追加した後、ステップの番号には、1 から N の整数がスキップされずに順番に付け替えられます。

### <a name="set-the-user-journey-to-be-executed"></a>実行するユーザー体験を設定する

これでユーザー体験を変更または作成したので、**証明書利用者** セクションで、このカスタム ポリシーに対して Azure AD B2C によって実行される体験を指定します。 [RelyingParty](relyingparty.md) 要素内で、**DefaultUserJourney** 要素を見つけます。 **ClaimsProviderSelections** を追加したユーザー体験の ID と一致するように、**DefaultUserJourney ReferenceId** を更新します。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>パスワードを忘れた場合のフローをアプリに示す

アプリケーションで、パスワードを忘れた場合のユーザー フローを使用して、ユーザーがサインインしたかどうかを検出する必要がある場合があります。 **isForgotPassword** クレームには、それが行われたことを示すブール値が含まれています。 このクレームは、アプリケーションに送信されるトークン内で発行できます。 必要に応じて、**証明書利用者** セクションで出力クレームに **isForgotPassword** を追加します。 アプリケーションで **isForgotPassword** クレームを調べて、ユーザーがパスワードをリセットしているかどうかを確認できます。

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>カスタム ポリシーをアップロードする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルのツール バーで、 **[ディレクトリとサブスクリプション]** アイコンを選択します。
1. **[ポータルの設定 | ディレクトリとサブスクリプション]** ペインの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. メニューの **[ポリシー]** で、 **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択します。 次の順序で、変更した 2 つのポリシー ファイルをアップロードします。
   1. 拡張ポリシー (例: *TrustFrameworkExtensions.xml*)。
   1. 証明書利用者ポリシー (例: *SignUpSignIn.xml*)。

::: zone-end

### <a name="test-the-password-reset-flow"></a>パスワード リセット フローをテストする

1. テストする (推奨の種類) サインアップまたはサインインのユーザー フローを選択します。
1. **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** で、前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** を選択します。
1. サインアップまたはサインインのページで、 **[パスワードを忘れた場合]** を選択します。
1. 前に作成したアカウントの電子メール アドレスを確認してから、 **[続行]** を選択します。
1. 表示されたダイアログで、ユーザーのパスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、ブラウザーにそれが表示されます。
1. 返されたトークンの **isForgotPassword** クレームの値を調べます。 それが存在していて `true` に設定されている場合、ユーザーはパスワードをリセットしています。

## <a name="password-reset-policy-legacy"></a>パスワード リセット ポリシー (レガシ)

[セルフサービス パスワード リセット](#self-service-password-reset-recommended)のエクスペリエンスが有効になっていない場合、このリンクを選択しても、パスワードのリセット ユーザー フローは自動的にトリガーされません。 代わりに、エラー コード `AADB2C90118` がアプリケーションに返されます。 アプリケーションで、認証ライブラリを再初期化して Azure AD B2C のパスワードのリセット ユーザー フローを認証することで、このエラー コードを処理する必要があります。

次の図は、このプロセスを示しています。

1. アプリケーションで、ユーザーが **[サインイン]** を選択します。 アプリによって承認要求が開始され、ユーザーがサインインを完了できるように Azure AD B2C にリダイレクトされます。 承認要求では、*B2C_1_signup_signin* などの、サインアップまたはサインインのポリシー名が指定されています。
1. ユーザーは **[パスワードを忘れた場合]** リンクを選択します。 Azure AD B2C によって、アプリケーションに `AADB2C90118` エラー コードが返されます。
1. アプリケーションはエラー コードを処理し、新しい承認要求を開始します。 承認要求では、*B2C_1_pwd_reset* などのパスワード リセット ポリシーの名前を指定します。

    ![従来のパスワード リセット ユーザー フローを示す図。](./media/add-password-reset-policy/password-reset-flow-legacy.png)

基本的な [ASP.NET サンプル](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)で、ユーザー フローのリンク方法を確認できます。

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>パスワードのリセット ユーザー フローを作成する

アプリケーションのユーザーが自分のパスワードをリセットできるようにするには、パスワード リセット ユーザー フローを作成します。

1. Azure portal で、Azure AD B2C テナントの概要に移動します。
1. 左側のメニューで、 **[ポリシー]** の下の **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
1. **[ユーザー フローの作成]** で、 **[パスワード リセット]** ユーザー フローを選択します。
1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。
1. **[名前]** に、ユーザー フローの名前を入力します。 たとえば、「*passwordreset1*」と入力します。
1. **[Identity providers]\(ID プロバイダー\)** で **[Reset password using username]\(ユーザー名を使用してパスワードをリセット\)** または **[Reset password using email address]\(メール アドレスを使用してパスワードをリセット\)** を有効にします。
1. 2 番目の認証方法を使用して ID 確認をユーザーに求める場合は、 **[多要素認証]** で、方法の種類と、多要素認証をいつ実施するかを選択します。 [詳細については、こちらを参照してください](multi-factor-authentication.md)。
1. Azure AD B2C テナントで条件付きアクセス ポリシーを構成してあり、それをこのユーザー フローで使用する場合は、 **[条件付きアクセス]** の **[Enforce conditional access policies]\(条件付きアクセス ポリシーを適用する\)** チェック ボックスをオンにします。 ポリシー名を指定する必要はありません。 [詳細については、こちらを参照してください](conditional-access-user-flow.md?pivots=b2c-user-flow)。
1. **[アプリケーション要求]** の **[さらに表示]** を選択します。 アプリケーションに送り返される承認トークンで返されるクレームを選択します。 たとえば、 **[User's Object ID] (ユーザーのオブジェクト ID)** を選択します。
1. **[OK]** を選択します。
1. **[作成]** を選択して、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

ユーザー フローをテストするには:

1. 作成したユーザー フローを選択します。 ユーザー フローの [概要] ページで、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** で、前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示される必要があります。
1. **[ユーザー フローを実行します]** を選択し、前に作成したアカウントのメール アドレスを確認してから、 **[続行]** を選択します。
1. パスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、ブラウザーにそれが表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する

カスタム ポリシーは、ユーザー体験を定義するために Azure AD B2C テナントにアップロードする XML ファイルのセットです。 サインアップとサインイン、パスワードのリセット、プロファイル編集ポリシーなど、いくつかの事前に構築されたポリシーを含むスターター パックが用意されています。 詳細については、「[Azure AD B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)」を参照してください。

::: zone-end

## <a name="troubleshoot-azure-ad-b2c-user-flows-and-custom-policies"></a>Azure AD B2C のユーザー フローとカスタム ポリシーのトラブルシューティング
アプリケーション側で、Azure B2C サービスから生成された特定のエラーを処理する必要があります。 [Azure AD B2C のユーザー フローとカスタム ポリシーのトラブルシューティング方法](troubleshoot.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[パスワードの強制リセット](force-password-reset.md)を設定します。
