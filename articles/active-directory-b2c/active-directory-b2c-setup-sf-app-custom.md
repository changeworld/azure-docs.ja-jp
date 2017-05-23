---
title: "Azure Active Directory B2C: カスタム ポリシーを使用した Salesforce SAML プロバイダーの追加 | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーに関するトピック"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: gsacavdm
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/30/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: dee24deacbe69ada64519802c0eb1b83f565f57e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: SAML を利用した、Salesforce アカウントでのログイン

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して特定の Salesforce 組織のユーザーのログインを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="azure-ad-b2c-setup"></a>Azure AD B2C のセットアップ
[カスタム ポリシーを使い始める](active-directory-b2c-get-started-custom.md)にあたっての手順がすべて完了していることを確認します。

次のトピックがあります。

1. Azure AD B2C テナントの作成。
1. Azure AD B2C アプリケーションの作成。
1. 2 つのポリシー エンジン アプリケーションの登録。
1. キーのセットアップ。
1. スターター パックのセットアップ。

### <a name="salesforce-setup"></a>Salesforce のセットアップ
このチュートリアルでは、既に以下が完了していることを前提としています。
1. Salesforce アカウントへのサインアップ。 [無料の Developer Edition](https://developer.salesforce.com/signup) にサインアップできます。 
1. Salesforce 組織用の ["私のドメイン" のセットアップ](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

## <a name="get-the-salesforce-saml-metadata"></a>Salesforce SAML メタデータの取得
>[!NOTE]
> このチュートリアルでは、[Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ) を使用していることを前提としています。

1. [Salesforce にログインします。](https://login.salesforce.com/) 
1. 左側のメニューで、**[Settings]\(設定\)** の下の **[Identity]\(ID\)** を展開し、**[Identity Provider]\(ID プロバイダー\)** をクリックします。
1. **[Enable Identity Provider]\(ID プロバイダーを有効にする\)** をクリックします。
1. Azure AD B2C と通信するときに Salesforce で使用する**証明書を選択**し、**[Save]\(保存\)** をクリックします。 既定の証明書を使用できます。
1. 使用できるようになった **[Download Metadata]\(メタデータのダウンロード\)** ボタンをクリックして、後の手順で使用するメタデータ ファイルを保存します。

## <a name="add-a-saml-signing-certificate-to-azure-ad-b2c"></a>Azure AD B2C への SAML 署名証明書の追加
Azure AD B2C テナントに Salesforce の証明書を格納する必要があります。 これを行うには、次の手順を実行します。

1. PowerShell を開き、作業ディレクトリ `active-directory-b2c-advanced-policies` に移動します。
1. ExploreAdmin ツールのあるフォルダーに切り替えます。

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. ExploreAdmin ツールを PowerShell にインポートします。

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. 次のコマンドで、`tenantName` を Azure AD B2C テナントの名前 (例: fabrikamb2c.onmicrosoft.com) に、`certificateId` を後でポリシーで参照するために使用する証明書の名前 (例: ContosoSalesforceCert) に置き換えます。さらに、`pathToCert` と `password` を証明書のパスとパスワードに置き換えます。 コマンドを実行します。

    ```PowerShell
    Set-CpimCertificate -TenantId {tenantName} -CertificateId {certificateId} -CertificateFileName {pathToCert} - CertificatePassword {password}
    ```

    コマンドを実行する際、Azure AD B2C テナントに対してローカルな onmicrosoft.com 管理者アカウントでサインインするようにします。 

1. PowerShell を閉じます。

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>基本ポリシーでの Salesforce SAML 要求プロバイダーの作成

ユーザーが Salesforce を使用してログインできるようにするためには、Salesforce を要求プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用する一連の "*要求*" を "*提供*" します。 これを行うには、ポリシーの拡張ファイルで Salesforce の `<ClaimsProvider>` を追加します。

1. 作業ディレクトリ (TrustFrameworkExtensions.xml) から拡張ファイルを開きます。
1. セクション `<ClaimsProviders>` を探します。 存在しない場合は、ルート ノードの下に追加します。
1. 新しい `<ClaimsProvider>` を次のとおり追加します。

    ```XML
    <ClaimsProvider>
      <Domain>contoso</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">
    <![CDATA[ <md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://contoso.com" validUntil="2026-10-05T23:57:13.854Z" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"><md:IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"><md:KeyDescriptor use="signing"><ds:KeyInfo><ds:X509Data><ds:X509Certificate>MIIErDCCA….qY9SjVXdu7zy8tZ+LqnwFSYIJ4VkE9UR1vvvnzO</ds:X509Certificate></ds:X509Data></ds:KeyInfo></md:KeyDescriptor><md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://contoso.com/idp/endpoint/HttpPost"/><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://contoso.com/idp/endpoint/HttpRedirect"/></md:IDPSSODescriptor></md:EntityDescriptor>]]>
            </Item>
          </Metadata>       
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="ContosoIdpSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="ContosoIdpSamlCert "/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. `<ClaimsProvider>` ノードの下で、`<Domain>` の値を、他の ID プロバイダーと区別するために使用できる一意の値に更新します。
1. `<ClaimsProvider>` ノードの下で、`<DisplayName>` の値を、要求プロバイダーを表すわかりやすい名前に更新します。 この値は現在使用されていません。

### <a name="update-the-technical-profile"></a>技術プロファイルの更新

Salesforce から SAML トークンを取得するためには、Azure AD B2C が Azure AD との通信に使用するプロトコルを定義する必要があります。 これは、`<ClaimsProvider>` の `<TechnicalProfile>` 要素内で行います。

1. `<TechnicalProfile>` ノードの ID を更新します。 この ID は、ポリシーの他の部分からこの技術プロファイルを参照するために使用します。
1. `<DisplayName>` の値を更新します。 この値は、ログイン画面のログイン ボタンに表示されます。
1. `<Description>` の値を更新します。
1. Azure AD では OpenID Connect プロトコルを使用するので、`<Protocol>` が "SAML2" になっていることを確認してください。

特定の Azure AD テナントの構成設定を反映するように、上の XML の `<Metadata>` セクションを更新する必要があります。 XML で、次のようにメタデータ値を更新します。

1. `<Item Key="PartnerEntity">` の値を、Salesforce からダウンロードした Metadata.xml のコンテンツで更新します。 **必ず <![CDATA[ …metadata… ]]> でカプセル化してください**。

1. 上の XML の `<CryptographicKeys>` セクションで、両方の `StorageReferenceId` の値を、定義した証明書 ID に更新します (例: ContosoSalesforceCert)。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

これで、Azure AD B2C が Azure AD ディレクトリと通信する方法を認識するようにポリシーを構成できました。 ポリシーの拡張ファイルをアップロードしてみて、現時点で問題がないことを確認します。 そのためには、次の手順を実行します。

1. Azure AD B2C テナントの **[すべてのポリシー]** ブレードに移動します。
1. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
1. 拡張ファイル (TrustFrameworkExtensions.xml) をアップロードし、検証に失敗しないことを確認します。

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>ユーザー体験への Salesforce SAML 要求プロバイダーの登録

ここで、Salesforce SAML の ID プロバイダーをいずれかのユーザー体験に追加する必要があります。 この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから変更し、Azure AD の ID プロバイダーも含まれるようにします。

1. ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
1. `<UserJourneys>` 要素を探し、Id ="SignUpOrSignIn" を含む `<UserJourney>` 全体をコピーします。
1. 拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
1. コピーした `<UserJourney>` 全体を `<UserJourneys>` 要素の子として貼り付けます。
1. 新しい `<UserJourney>` (つまり  SignUpOrSignUsingContoso) の ID の名前を変更します。

### <a name="display-the-button"></a>"ボタン" の表示

`<ClaimsProviderSelection>` 要素は、サインアップ/サインイン画面の ID プロバイダー ボタンに類似しています。 Salesforce の `<ClaimsProviderSelection>` 要素を追加することで、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 これを行うには、次の手順を実行します。

1. 作成した `<UserJourney>` で `Order="1"` になっている `<OrchestrationStep>` を見つけます。
1. 以下を追加します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. `TargetClaimsExchangeId` を適切な値に設定します。 他の場合と同じ規則に従うことをお勧めします (*\[ClaimProviderName\]Exchange*)。

### <a name="link-the-button-to-an-action"></a>アクションへの "ボタン" のリンク

"ボタン" が所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Salesforce と通信して SAML トークンを受信します。 これを行うには、Salesforce SAML 要求プロバイダーの技術プロファイルをリンクします。

1. `<UserJourney>` ノードで `Order="2"` になっている `<OrchestrationStep>` を見つけます。
1. 以下を追加します。

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. `Id` を上の `TargetClaimsExchangeId` の値と同じ値に更新します。
1. `TechnicalProfileReferenceId` を、前に作成した技術プロファイルの `Id` に更新します (例: ContosoProfile)。

### <a name="upload-the-updated-extension-file"></a>更新済み拡張ファイルのアップロード

拡張ファイルの変更が終了しました。 このファイルを保存してアップロードし、すべての検証が正常に行われることを確認します。

### <a name="update-the-rp-file"></a>RP ファイルの更新

ここで、作成したユーザー体験を開始する RP ファイルを更新する必要があります。

1. 作業ディレクトリに SignUpOrSignIn.xml のコピーを作成してその名前を変更します (例: SignUpOrSignInWithAAD.xml)。
1. 新しいファイルを開き、`<TrustFrameworkPolicy>` の `PolicyId` 属性を一意の値で更新します。 これがポリシーの名前になります (例: SignUpOrSignInWithAAD)。
1. `<DefaultUserJourney>` の `ReferenceId` 属性を変更して、作成した新しいユーザー体験の ID と一致するようにします (例: SignUpOrSignUsingContoso)。
1. 変更内容を保存し、ファイルをアップロードします。

## <a name="create-a-connected-app-in-salesforce"></a>Salesforce での接続されたアプリの作成
Azure AD B2C を接続されたアプリとして Salesforce に登録する必要があります。

1. [Salesforce にログインします。](https://login.salesforce.com/) 
1. 左側のメニューで、**[Settings]\(設定\)** の下の **[Identity]\(ID\)** を展開し、**[Identity Provider]\(ID プロバイダー\)** をクリックします。
1. 下部の **[Service Providers]\(サービス プロバイダー\)** セクションで、**[Service Providers are now created via Connected Apps. Click here.]\(接続されたアプリでサービス プロバイダーが作成されました。ここをクリックしてください。\)** をクリックします。
1. 接続されたアプリの必須の**基本情報**を提供します。
1. **[Web App Settings]\(Web アプリの設定\)** セクションで次の手順を実施します。
    1. **[Enable SAML]\(SAML を有効にする\)** チェック ボックスをオンにします。
    1. **[Entity ID]\(エンティティ ID\)** フィールドに次の URL を入力します。必ず `tenantName` を置き換えてください。 
    
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base
        ```

    1. **[ACS URL]** フィールドに次の URL を入力します。必ず `tenantName` を置き換えてください。 
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base/samlp/sso/assertionconsumer
        ```

    1. それ以外の設定はすべて既定のままにします。
1. 一番下までスクロールし、**[Save]\(保存\)** ボタンをクリックします。


## <a name="troubleshooting"></a>トラブルシューティング

アップロードしたカスタム ポリシーのブレードを開いて [Run now]\(今すぐ実行\) をクリックし、カスタム ポリシーをテストします。 エラーが発生した場合は、[トラブルシューティング](active-directory-b2c-troubleshoot-custom.md)の方法を参照してください。

## <a name="next-steps"></a>次のステップ
 
[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) にフィードバックを提供します。


