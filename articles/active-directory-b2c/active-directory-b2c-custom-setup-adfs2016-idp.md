---
title: Azure Active Directory B2C のカスタム ポリシーを使って ADFS を SAML ID プロバイダーとして追加する | Microsoft Docs
description: Azure Active Directory B2C で SAML プロトコルとカスタム ポリシーを使用して ADFS 2016 を設定する
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669228"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して SAML ID プロバイダーとして ADFS を追加する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して ADFS ユーザー アカウントのサインインを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Azure AD B2C に ADFS アカウント アプリケーション キーを追加する

ADFS アカウントでのフェデレーションには、アプリケーションに代わって Azure AD B2C を信頼するために、そのアカウント用のクライアント シークレットが必要になります。 Azure AD B2C テナントに ADFS 証明書を格納する必要があります。 

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 **[ディレクトリの切り替え]** を選択し、作成したテナントを含むディレクトリを選択します。 このチュートリアルでは、*contoso0522Tenant.onmicrosoft.com* という名前のテナントを含む *contoso* ディレクトリが使用されています。

    ![ディレクトリの切り替え](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。 現在は自分のテナントを使用しているはずです。
4. [概要] ページで、**[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示し、**[追加]** をクリックします。
6. [オプション] として **[アップロード]** を選択します。
7. 名前に「`ADFSSamlCert`」と入力します。 プレフィックス `B2C_1A_` が自動的に追加される場合があります。
8. 秘密キーが含まれている証明書の .pfx ファイルを参照して選択します。 秘密キーが含まれているこの証明書は、ADFS 証明書利用者に発行され使用されているものと同一にする必要があります。
9. **[作成]** をクリックし、`B2C_1A_ADFSSamlCert` キーを作成したことを確認します。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>拡張ポリシーにクレーム プロバイダーを追加する

ユーザーが ADFS アカウントを使ってサインインできるようにするには、そのアカウントをクレーム プロバイダーとして定義する必要があります。 これを行うには、Azure AD B2C が通信するエンドポイントを指定します。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

拡張ポリシー ファイルに **ClaimsProvider** 要素を追加することで、ADFS をクレーム プロバイダーとして定義します。

1. 作業ディレクトリで、*TrustFrameworkExtensions.xml* ポリシー ファイルを開きます。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code を試してください](https://code.visualstudio.com/download)。
2. **ClaimsProviders** 要素の下に次の XML を追加し、**your-ADFS-domain** を実際の ADFS ドメイン名に置き換え、**identityProvider** 出力要求の値を実際の DNS (ドメインを示す任意の値) に置き換え、ファイルを保存します。 

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>サインアップとサインインのためのクレーム プロバイダーを登録する

サインアップ ページとサインイン ページで ADFS アカウント ID プロバイダーを使用できるようにするには、**SignUpOrSignIn** ユーザー体験に追加する必要があります。 

既存のテンプレートのユーザー体験のコピーを作成し、ADFS ID プロバイダーを含めるように変更します。

>[!NOTE]
>既に **UserJourneys** 要素をポリシーの基本ファイルから拡張ファイル (*TrustFrameworkExtensions.xml*) にコピーしている場合は、このセクションをスキップできます。

1. ポリシーの基本ファイルを開きます。 たとえば、*TrustFrameworkBase.xml* などです。
2. **UserJourneys** 要素の内容全体をコピーします。
3. 拡張ファイル (*TrustFrameworkExtensions.xml*) を開き、コピーした **UserJourneys** 要素の内容全体を拡張ファイルに貼り付けます。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelections** 要素は、クレーム プロバイダーの選択肢とその順序の一覧を定義します。  **ClaimsProviderSelection** 要素は、サインアップおよびサインイン ページの ID プロバイダーのボタンに類似しています。 ADFS アカウントに **ClaimsProviderSelection** 要素を追加すると、ユーザーがページを表示するときに新しいボタンが表示されます。 この要素を追加するには、次の手順を実行します。

1. コピーしたユーザー体験で識別子が `SignUpOrSignIn` の **UserJourney** 要素から、`Order="1"` の **OrchestrationStep** 要素を探します。
2. **ClaimsProviderSelections** 要素の下に次の **ClaimsProviderSelection** 要素を追加します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションとは、Azure AD B2C が ADFS アカウントと通信して SAML トークンを受信することです。 ADFS アカウント クレーム プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1. **UserJourney**  要素の下にある `Order="2"` の **OrchestrationStep** を探します。
2. **ClaimsExchanges** 要素の下に次の **ClaimsExchange** 要素を追加します。

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * `Id`が、前のセクションの `TargetClaimsExchangeId` と同じ値であることを確認します。
> * `TechnicalProfileReferenceId` が、前に作成した技術プロファイル (Contoso-SAML2) に設定されていることを確認します。


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[省略可能] プロファイル編集用のクレーム プロバイダーを登録する

ADFS アカウント ID プロバイダーをプロファイル編集ユーザー体験に追加することもできます。

### <a name="display-the-button"></a>ボタンを表示する

1. ポリシーの拡張ファイルを開きます。 たとえば、*TrustFrameworkExtensions.xml* です。
2. コピーしたユーザー体験で識別子が `ProfileEdit` の **UserJourney** 要素から、`Order="1"` の **OrchestrationStep** 要素を探します。
3. **ClaimsProviderSelections** 要素の下に次の **ClaimsProviderSelection** 要素を追加します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

1. **UserJourney**  要素の下にある `Order="2"` の **OrchestrationStep** を探します。
2. **ClaimsExchanges** 要素の下に次の **ClaimsExchange** 要素を追加します。

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>ポリシーをテナントにアップロードする

1. Azure portal で **[すべてのポリシー]** を選択します。
2. **[ポリシーのアップロード]** を選択します。
3. **[ポリシーが存在する場合は上書きする]** を有効にします。
4. *TrustFrameworkExtensions.xml* ポリシー ファイルを参照して選択し、**[アップロード]** を選択します。 検証が成功したことを確認します。


## <a name="configure-an-adfs-relying-party-trust"></a>ADFS 証明書利用者信頼を構成する

Azure AD B2C で ID プロバイダーとして ADFS を使用するには、Azure AD B2C SAML メタデータを使用して ADFS 証明書利用者信頼を作成する必要があります。 次の例は、Azure AD B2C テクニカル プロファイルの SAML メタデータへの URL アドレスを示しています。

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

次の値を置き換えます。

- **your-tenant** は、実際のテナント名 (your-tenant.onmicrosoft.com など) に置き換えます。
- **your-policy** は、実際のポリシー名に置き換えます。 SAML プロバイダーのテクノロジ プロファイルを構成するポリシー、またはそのポリシーから継承されるポリシーを使用します。
- **your-technical-profile** は、実際の SAML ID プロバイダー技術プロファイルの名前に置き換えます。
 
ブラウザーを開き、この URL に移動します。 正しい URL を入力し、XML メタデータ ファイルにアクセスできることを確認します。

ADFS 管理スナップインを使用して新しい証明書利用者信頼を追加するには、手動で設定を構成して、フェデレーション サーバーで次の手順を実行します。 この手順を完了するためには、ローカル コンピューター上の**管理者**のメンバーシップ、またはそれと同等であることが最低限求められます。 適切なアカウントとグループ メンバーシップの使用の詳細については、「[Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477)」(ローカルおよびドメインの既定のグループ) を参照してください。

1. [サーバー マネージャー] で、**[ツール]** を選択し、**[ADFS Management]\(ADFS 管理\)** を選択します。
2. **[証明書利用者信頼の追加]** を選択します。
3. **[ようこそ]** ページで **[要求に対応する]** を選択し、**[開始]** をクリックします。
4. **[データ ソースの選択]** ページで、**[オンラインまたはローカル ネットワークで公開されている証明書利用者についてのデータをインポートする]** を選択し、Azure AD B2C メタデータ URL を入力し、**[次へ]** をクリックします。
5. **[表示名の指定]** ページで、**[表示名]** を入力し、**[メモ]** にこの証明書利用者の説明を入力して、**[次へ]** をクリックします。
6. **[アクセス制御ポリシーの選択]** ページで、ポリシーを選択して、**[次へ]** をクリックします。
7. **[信頼の追加の準備完了]** ページで、設定を確認し、**[次へ]** をクリックして証明書利用者信頼の情報を保存します。
8. **[完了]** ページで、**[閉じる]** をクリックすると、この操作によって、**[要求規則の編集]** ダイアログ ボックスが自動的に表示されます。
9. **[規則の追加]** を選択します。  
10. **[要求規則テンプレート]** で、**[LDAP 属性を要求として送信]** を選択します。
11. **[要求規則名]** を指定します。 **[属性ストア]** で、**[Active Directory の選択]** を選択し、次の要求を追加し、**[完了]**、**[OK]** の順にクリックします。

    ![規則のプロパティを設定する](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  証明書の種類によっては、HASH アルゴリズムを設定する必要があります。 証明書利用者信頼 (B2C デモ) のプロパティ ウィンドウで、**[詳細]** タブを選択して、**[セキュア ハッシュ アルゴリズム]** を `SHA-1` または `SHA-256` に変更し、**[OK]** をクリックします。  

### <a name="update-the-relying-party-metadata"></a>証明書利用者メタデータを更新する

SAML 技術プロファイルを変更するには、更新されたメタデータ バージョンで ADFS を更新する必要があります。 証明書利用者アプリケーションを作成するときにメタデータを更新する必要はありませんが、変更を加えるときは、ADFS のメタデータを更新します。

1. [サーバー マネージャー] で、**[ツール]** を選択し、**[ADFS Management]\(ADFS 管理\)** を選択します。
2. 作成した証明書利用者信頼を選択し、**[フェデレーション メタデータから更新]** を選択し、**[更新]** をクリックします。 

### <a name="test-the-policy-by-using-run-now"></a>[今すぐ実行] を使用してポリシーをテストする

1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_ProfileEdit** を開きます。 **[今すぐ実行]** を選択します。 ADFS アカウントを使ってサインインすることができます。

## <a name="download-the-complete-policy-files"></a>完全なポリシー ファイルをダウンロードする

省略可能: 「[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」の手順を完了した後に独自のカスタム ポリシー ファイルを使ってシナリオを構築することができます。 例のファイルについては、[参照専用のポリシー サンプル ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)を参照してください。
