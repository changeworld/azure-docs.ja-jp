---
title: Azure Active Directory B2C でのカスタム ポリシーのトラブルシューティング
description: Azure Active Directory B2C でカスタム ポリシーを使って作業するときに発生するエラーを解決する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103967"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Azure AD B2C カスタム ポリシーのトラブルシューティング

Azure Active Directory B2C (Azure AD B2C) [カスタム ポリシー](custom-policy-overview.md)を使用すると、ポリシー言語の XML 形式またはランタイムの問題が発生する可能性があります。 この記事では、問題を発見して解決するうえで役立つツールとヒントについていくつか説明します。 

この記事では、Azure AD B2C のカスタム ポリシーの構成のトラブルシューティングについて主に説明します。 証明書利用者アプリケーションまたはその ID ライブラリのトラブルシューティングは取り扱いません。

## <a name="azure-ad-b2c-correlation-id-overview"></a>Azure AD B2C 関連付け ID の概要

Azure AD B2C 関連付け ID は、認可要求に関連付けられている一意の識別子の値です。 これは、ユーザーが実行するすべてのオーケストレーション手順を通過します。 関連付け ID を使用すると、次のことができます。

- アプリケーションのサインイン アクティビティを特定し、ポリシーのパフォーマンスを追跡します。
- サインイン要求の Azure Application Insights ログを検索します。
- REST API に関連付け ID を渡し、それを使用してサインイン フローを識別します。 

関連付け ID は、新しいセッションが確立されるたびに変更されます。 ポリシーをデバッグする場合は、既存のブラウザー タブを閉じてください。 または、新しいプライベート モードのブラウザーを開きます。

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Azure AD B2C 関連付け ID の取得

関連付け ID は、Azure AD B2C のサインアップ ページまたはサインイン ページで確認できます。 ブラウザーで **[ソースの表示]** を選択します。 関連付けは、ページの上部にコメントとして表示されます。

![Azure AD B2C サインイン ページの [ソースの表示] のスクリーンショット。](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

関連付け ID をコピーしてから、サインイン フローを続行します。 関連付け ID を使用して、サインインの動作を確認します。 詳細については、「[Application Insights によるトラブルシューティング](#troubleshooting-with-application-insights)」を参照してください。

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Azure AD B2C 関連付け ID のエコー

Azure AD B2C トークンに関連付け ID を含めることができます。 関連付け ID を含めるには、次の操作を行います。

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. **ClaimsSchema** 要素に city 要求を追加します。  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. ポリシーの証明書利用者ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ファイルです。 ユーザー体験が成功した後、出力要求がトークンに追加され、アプリケーションに送信されます。 証明書利用者セクション内の技術プロファイル要素を変更して、city を出力要求として追加します。
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Application Insights によるトラブルシューティング

カスタム ポリシーに関する問題を診断するには、[Application Insights](troubleshoot-with-application-insights.md) を使用します。 Application Insights では、カスタム ポリシー ユーザー体験のアクティビティをトレースします。 これにより、例外を診断し、Azure AD B2C と、技術プロファイルで定義されているさまざまなクレーム プロバイダー (ID プロバイダー、API ベースのサービス、Azure AD B2C ユーザー ディレクトリ、その他のサービスなど) との間の要求の交換を確認することができます。  

[VS Code](https://code.visualstudio.com/) 用の [Azure AD B2C 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)をインストールすることをお勧めします。 Azure AD B2C 拡張機能を使用すると、ポリシー名、関連付け ID (Application Insights では関連付け ID の最初の桁が示されます)、ログのタイムスタンプによってログが整理されます。 この機能を使用すると、ローカル タイムスタンプに基づいて関連するログを検索し、Azure AD B2C によって実行されるユーザー体験を確認することができます。 

> [!NOTE]
> - Application Insights で新しいログが確認できるようになるまで、少し時間がかかります (通常は 5 分以内)。
> - コミュニティでは、ID 開発者を支援するために、Azure AD B2C 用の Visual Studio Code 拡張機能が開発されました。 Microsoft はこの拡張機能をサポートしておらず、厳密に現状のまま利用可能です。

シングル サインイン フローでは、複数の Azure Application Insights トレースを発行できます。 次のスクリーンショットでは、*B2C_1A_signup_signin* ポリシーに 3 つのログがあります。 各ログは、サインイン フローの一部を表します。

次のスクリーンショットは、Azure Application Insights トレース エクスプローラーを使用した VS Code 用の Azure AD B2C 拡張機能を示しています。

![Azure Application Insights トレースによる VS Code 用の Azure AD B2C 拡張機能のスクリーンショット。](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>トレース ログのフィルター処理

Azure AD B2C トレース エクスプローラーにフォーカスを移動し、検索する関連付け ID または時間の最初の桁の入力を開始します。 Azure AD B2C トレース エクスプローラーの右上のフィルター ボックスにここまでに入力した内容が表示され、一致するトレース ログが強調表示されます。  

![Azure AD B2C 拡張機能の Azure AD B2C トレース エクスプローラーのフィルターによる強調表示のスクリーンショット。](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

フィルター ボックスの上にマウス ポインターを移動し、 **[型のフィルターを有効にする]** を選択すると、一致するトレース ログのみが表示されます。 **[X] クリア ボタン** を使用してフィルターをクリアします。

![Azure AD B2C 拡張機能の Azure AD B2C トレース エクスプローラーのフィルターのスクリーンショット。](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Application Insights トレース ログの詳細

Azure Application Insights トレースを選択すると、拡張機能によって **[Application Insights の詳細]** ウィンドウが開き、次の情報が表示されます。

- **Application Insights** - トレース ログに関する一般的な情報 (ポリシー名、関連付け ID、Azure Application Insights トレース ID、トレース タイムスタンプなど)。
- **技術プロファイル** - トレース ログに表示される技術プロファイルの一覧。
- **要求** - トレース ログに表示される要求とその値のアルファベット順の一覧。 トレース ログに要求が異なる値で複数回出現する場合、最新の値は `=>` 記号で示されます。 これらの要求を確認して、予期される要求値が正しく設定されているかどうかを判断できます。 たとえば、要求値を確認する前提条件がある場合、要求セクションは、予期されるフローの動作が異なる理由を判断するのに役立ちます。
- **要求の変換** - トレース ログに表示される要求の変換の一覧。 それぞれの要求の変換には、入力要求、入力パラメーター、出力要求が含まれています。 要求の変換セクションでは、送信されるデータと、要求の変換の結果についての洞察が得られます。
- **トークン** - トレース ログに表示されるトークンの一覧。 トークンには、基になるフェデレーション OAuth および OpenId Connect ID プロバイダーのトークンが含まれます。 フェデレーション ID プロバイダーのトークンからは、ID プロバイダーが Azure AD B2C に要求を返す方法についての詳細が提供されます。これにより、ID プロバイダーの技術プロファイル出力要求をマップできます。 
- **例外** - トレース ログに表示される例外または致命的なエラーの一覧。
- **Application Insights JSON** - Application Insights から返される生データ。

次のスクリーンショットは、[Application Insights トレース ログの詳細] ウィンドウの例を示しています。  

![Azure AD B2C 拡張機能の Azure AD B2C トレース レポートのスクリーンショット。](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>JWT トークンのトラブルシューティング

JWT トークンの検証とデバッグを目的として、[https://jwt.ms](https://jwt.ms) などのサイトを使用して、JWT をデコードすることができます。 トークン検査のために `https://jwt.ms` にリダイレクトできるテスト アプリケーションを作成します。 [Web アプリケーションを登録](tutorial-register-applications.md)し、[ID トークンの暗黙的な許可を有効化](tutorial-register-applications.md#enable-id-token-implicit-grant)します (まだこれらを行っていない場合)。 

![JWT トークンのプレビューのスクリーンショット。](./media/troubleshoot-custom-policies/jwt-token-preview.png)

**[今すぐ実行]** と `https://jwt.ms` を使用して、Web アプリケーションまたはモバイル アプリケーションとは別にポリシーをテストします。 この Web サイトは、証明書利用者アプリケーションと同様に動作します。 このサイトには、Azure AD B2C ポリシーによって生成される JSON Web トークン (JWT) の内容が表示されます。

## <a name="troubleshoot-saml-protocol"></a>SAML プロトコルのトラブルシューティング

サービス プロバイダーとの統合の構成とデバッグを支援するために、SAML プロトコル用のブラウザー拡張機能を使用できます。たとえば、Chrome 用の [SAML DevTools 拡張機能](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio)、FireFox 用の [SAML トレーサー](https://addons.mozilla.org/es/firefox/addon/saml-tracer/)、[Edge または IE 開発者ツール](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)などです。

次のスクリーンショットでは、Azure AD B2C が ID プロバイダーに送信する SAML 要求と SAML 応答が SAML DevTools 拡張機能でどのように表示されるかを示します。

![SAML プロトコルのトレース ログのスクリーンショット。](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

これらのツールを使用すると、アプリケーションと Azure AD B2C の統合を確認できます。 次に例を示します。

- SAML 要求に署名が含まれているかどうかを確認し、認可要求へのサインインに使用するアルゴリズムを決定します。
- Azure AD B2C からエラー メッセージが返されるかどうかを確認します。
- アサーション セクションが暗号化されているかどうかを確認します。
- 要求の名前を取得すると、ID プロバイダーが返されます。

[Fiddler](https://www.telerik.com/fiddler) を使用して、クライアント ブラウザーと Azure AD B2C の間でのメッセージ交換をトレースすることもできます。 これにより、オーケストレーション手順のどこでユーザー体験が失敗するか、ヒントを得ることができます。

## <a name="troubleshoot-policy-validity"></a>ポリシーの有効性のトラブルシューティング

ポリシーの開発が完了したら、ポリシーを Azure AD B2C にアップロードします。 ポリシーに問題がある可能性があります。 ポリシーの整合性/有効性を確保するには、次の方法を使用します。

カスタム ポリシーの設定における最も一般的なエラーが、不適切な形式の XML です。 そのため、優れた XML エディターがほぼ不可欠です。 XML のネイティブな表示、コンテンツの色設定、一般的な用語の事前入力、XML 要素の持続的なインデックス作成、XML スキーマに対する検証を行うものです。

[Visual Studio Code](https://code.visualstudio.com/) を使用することをお勧めします。 次に、XML 拡張機能 ([Red Hat による XML 言語サポート](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml)など) をインストールします。 XML 拡張機能を使用すると、カスタム ポリシー [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) スキーマ定義を使用して、XML ファイルをアップロードする前に XML スキーマを検証できます。

XML ファイルの関連付け方法を使用すると、VS Code の `settings.json` ファイルに次の設定を追加して、XML ファイルを XSD にバインドできます。 これを行うには、次の手順を実行します。

1. VS Code で、 **[設定]** をクリックします。 詳細については、「[ユーザーとワークスペースの設定](https://code.visualstudio.com/docs/getstarted/settings)」を参照してください。
1. **fileAssociations** を検索してから、 **[拡張機能]** の下で **[XML]** を選択します。
1. **[settings.json で編集]** を選択します。

    ![VS Code XML スキーマ検証のスクリーンショット。](./media/troubleshoot-custom-policies/xml-validation.png)
1. settings.json に、次の JSON コードを追加します。

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

XML 検証エラーの例を次に示します。 要素名の上にマウス ポインターを置くと、予期される要素が拡張機能によって一覧表示されます。

![VS Code XML スキーマ検証のエラー インジケーターのスクリーンショット。](./media/troubleshoot-custom-policies/xml-validation-error.png)

次の例では、`DisplayName` 要素は適正です。 ただし、順序が間違っています。 `DisplayName` は、`Protocol` 要素の前に配置する必要があります。 この問題を修正するには、`DisplayName` 要素の上にマウス ポインターを合わせて、要素の順序を修正します。

![VS Code XML スキーマ検証の順序エラーのスクリーンショット。](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>アップロード ポリシーとポリシー検証

XML ポリシー ファイルの検証は、アップロード時に自動的に実行されます。 ほとんどのエラーによって、アップロードが失敗します。 検証には、アップロードするポリシー ファイルが含まれます。 アップロード ファイルが参照する一連のファイル (証明書利用者のポリシー ファイル、拡張ファイル、およびベース ファイル) も含まれます。

> [!TIP]
> Azure AD B2C では、証明書利用者ポリシーの追加の検証が実行されます。 ポリシーに問題がある場合は、拡張機能ポリシーのみを編集する場合でも、証明書利用者ポリシーをアップロードすることをお勧めします。 

ここでは、一般的な検証エラーと考えられる解決策について説明します。

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>スキーマ検証エラーが見つかりました...無効な子要素 '{name}' があります

ポリシーに無効な XML 要素が含まれているか、XML 要素は有効ですが、順序が間違っています。 この種類のエラーを修正するには、「[ポリシーの有効性のトラブルシューティング](#troubleshoot-policy-validity)」セクションを参照してください。

### <a name="there-is-a-duplicate-key-sequence-number"></a>重複するキー シーケンス '{number}' があります 

ユーザー[体験](userjourneys.md)または[サブ体験](subjourneys.md)は、シーケンスで実行されるオーケストレーション手順の順序付きリストで構成されます。 体験を変更すると、手順の番号は、1 から N の整数がスキップされずに順番に付け替えられます。

> [!TIP]
> [VS Code](https://code.visualstudio.com/) 用の [Azure AD B2C 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) `(Shift+Ctrl+r)` コマンドを使用すると、ポリシー内のすべてのユーザー体験とサブ体験のオーケストレーション手順の番号を付け替えることができます。

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>...順序が "{number}" の手順が想定されていますが、見つかりませんでした...

前のエラーを確認してください。

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>ユーザー体験 "{name}" のオーケストレーション手順の順序 "{number}"...は、後にクレーム プロバイダー選択手順が続き、要求の交換である必要がありますが、型は...

`ClaimsProviderSelection` と `CombinedSignInAndSignUp` の型のオーケストレーション手順には、ユーザーが選択できるオプションの一覧が含まれています。 この後には、1 つまたは複数の要求の交換を行う `ClaimsExchange` 型が続く必要があります。

次のオーケストレーション手順では、この種類のエラーが発生します。 2 番目のオーケストレーション手順は、`ClaimsProviderSelection` ではなく、`ClaimsExchange` 型である必要があります。

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>...手順 {number} で 2 つの要求の交換を行います。 どの要求の交換を使用できるかを判断するには、その前にクレーム プロバイダーを選択する必要があります

`ClaimsExchange` 型のオーケストレーション手順は、前の手順が `ClaimsProviderSelection` または `CombinedSignInAndSignUp` の型でない限り、単一の `ClaimsExchange` である必要があります。 次のオーケストレーション手順では、この種類のエラーが発生します。 6 番目の手順には、2 つの要求の交換が含まれています。 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

この種類のエラーを修正するには、2 つのオーケストレーション手順を使用します。 各オーケストレーション手順には、1 つの要求の交換を使用します。

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>重複するキー シーケンス '{name}' があります

ユーザー体験に同じ `Id` を持つ複数の `ClaimsExchange` があります。 次の手順では、この種類のエラーが発生します。 ID *AADUserWrite* がユーザー体験に 2 回出現しています。

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

この種類のエラーを修正するには、8 番目のオーケストレーション手順である要求の交換を、*Call-REST-API* などの一意の名前に変更します。

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>...ID "{claim name}" の ClaimType への参照が作成されますが、ポリシーとその基本ポリシーのいずれにもこのような要素が含まれていません

この種類のエラーは、ポリシーによって[要求スキーマ](claimsschema.md)で宣言されていない要求への参照が作成された場合に発生します。 要求は、ポリシー内の少なくとも 1 つのファイルで定義されている必要があります。 

たとえば、技術プロファイルに *schoolId* 出力要求が含まれています。 しかし、出力要求 *schoolId* は、ポリシーまたは祖先のポリシーで宣言されていません。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

この種類のエラーを修正するには、`ClaimTypeReferenceId` 値のスペルが間違っていないか、またはスキーマに存在しているかを確認します。 要求が拡張機能ポリシーで定義されているが、基本ポリシーでも使用されている場合。 要求が、使用されているポリシーまたは上位レベルのポリシーで定義されていることを確認します。

要求を要求スキーマに追加すると、この種類のエラーが解決されます。

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>ID... を持つ ClaimsTransformation への参照が作成されます...

このエラーの原因は、要求エラーの原因と同様のものです。 前のエラーを確認してください。

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>現在、ユーザーは 'yourtenant.onmicrosoft.com' テナントのユーザーとしてログに記録されています...

アップロードしようとしたポリシーとは異なるテナントのアカウントを使用してログインします。 たとえば、ポリシー `TenantId` が `fabrikam.onmicrosoft.com` に設定されている状態で、admin@contoso.onmicrosoft.com にサインインします。

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- `<TrustFrameworkPolicy\>` および `<BasePolicy\>` 要素の `TenantId` の値が、ターゲットの Azure AD B2C テナントと一致していることを確認します。

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>要求の種類 "{name}" は証明書利用者の技術プロファイルの出力要求ですが、ユーザー体験のどの手順でも出力要求ではありません...

証明書利用者ポリシーで出力要求を追加しましたが、出力要求はユーザー体験のどの手順でも出力要求ではありません。 Azure AD B2C では要求バッグから要求値を読み取ることができません。

次の例では、*schoolId* 要求は証明書利用者の技術プロファイルの出力要求ですが、*SignUpOrSignIn* ユーザー体験のどの手順でも出力要求ではありません。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

この種類のエラーを修正するには、出力要求が少なくとも 1 つのオーケストレーション手順の技術プロファイル出力要求コレクションに出現していることを確認します。 ユーザー体験で要求を出力できない場合は、証明書利用者の技術プロファイルで、空の文字列などの既定値を設定します。  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>入力文字列の形式が正しくありません

別の型の要求に無効な値の型を設定しています。 たとえば、整数の要求を定義します。

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

次に、文字列値を設定しようとします。

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

この種類のエラーを修正するには、`DefaultValue="0"` のように、正しい値を設定していることを確認してください。


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>テナント "{name}" には既に ID "{name}" のポリシーがあります。 同じ ID を持つ別のポリシーを格納することはできません

テナントにポリシーをアップロードしようとしましたが、同じ名前のポリシーが既にテナントにアップロードされています。 

この種類のエラーを修正するには、ポリシーをアップロードするときに、 **[カスタム ポリシーが既に存在する場合は上書きします]** チェックボックスをオンにします。

![カスタム ポリシーが既に存在する場合に上書きする方法を示すスクリーンショット。](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>次のステップ

- [Application Insights を使用して Azure Active Directory B2C のログを収集する](troubleshoot-with-application-insights.md)方法を確認します。

