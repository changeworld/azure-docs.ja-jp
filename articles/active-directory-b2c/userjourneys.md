---
title: UserJourneys
description: Azure Active Directory B2C でカスタム ポリシーの UserJourneys 要素を指定します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/31/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d297f2c9e32de22beba8c8bf63d4b62e73e8690c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131057497"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ユーザー体験では、証明書利用者アプリケーションがユーザーの任意の要求を取得できるようにするポリシーの明示的なパスを指定します。 ユーザーはこれらのパスを通じて証明書利用者に提示される要求を検索します。 つまり、ユーザー体験では、Azure AD B2C Identity Experience Framework が要求をプロセスする際に、エンド ユーザーがたどるビジネス ロジックを定義します。

これらのユーザー体験は、関係者コミュニティのさまざまな証明書利用者の主要なニーズに応えるために使用できるテンプレートと見なすことができます。 ユーザー体験によって、ポリシーの証明書利用者パートの定義が容易になります。 ポリシーは複数のユーザー体験を定義できます。 各ユーザー体験は一連のオーケストレーション手順です。

ポリシーでサポートされているユーザー体験を定義するために、**UserJourneys** 要素がポリシー ファイルの最上位の要素の下に追加されます。

**UserJourneys** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | 完全なユーザー フローに必要な構成すべてを定義するユーザー体験。 |

**UserJourney** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | ポリシー内の他の要素から参照するために使用できるユーザー体験の識別子。 [証明書利用者ポリシー](relyingparty.md)の **DefaultUserJourney** 要素は、この属性をポイントします。 |
| DefaultCpimIssuerTechnicalProfileReferenceId| いいえ | 既定のトークン発行者の技術プロファイル参照 ID。 [JWT トークン発行者](userjourneys.md)、[SAML トークン発行者](saml-issuer-technical-profile.md)、[OAuth2 カスタム エラー](oauth2-error-technical-profile.md)などです。 ユーザー体験またはサブ体験に既に別の `SendClaims` オーケストレーション ステップがある場合は、`DefaultCpimIssuerTechnicalProfileReferenceId` 属性をユーザー体験のトークン発行者の技術プロファイルに設定します。 |

**UserJourney** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | 承認技術プロファイルの一覧。 | 
| OrchestrationSteps | 1:n | トランザクションを成功させるために従う必要のあるオーケストレーション シーケンス。 すべてのユーザー体験は、シーケンスで実行されるオーケストレーション手順の順序付きリストで構成されます。 いずれかの手順に失敗した場合、トランザクションは失敗します。 |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

ユーザーが UserJourney を完了し、アクセス トークンまたは ID トークンを取得したとします。 [UserInfo エンドポイント](userinfo-endpoint.md)などの追加リソースを管理するためには、ユーザーを識別する必要があります。 このプロセスを開始するには、有効な Azure AD B2C ポリシーによって元々認証されていたことを証明するものとして、以前に発行されたアクセス トークンを提示する必要があります。 ユーザーがこの要求を行うことができるようにするため、このプロセスの間、ユーザーの有効なトークンは常に存在していなければなりません。 承認技術プロファイルにより、受信トークンが検証され、トークンから要求が抽出されます。

**AuthorizationTechnicalProfiles** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | 承認技術プロファイルの一覧。 | 

**AuthorizationTechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Yes | 実行される技術プロファイルの識別子。 |

次の例は、承認技術プロファイルを持つユーザー体験要素を示しています。

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

ユーザー体験は、トランザクションを成功させるために従う必要のあるオーケストレーション シーケンスとして表されます。 いずれかの手順に失敗した場合、トランザクションは失敗します。 これらのオーケストレーション手順は、ポリシー ファイルで許可されているビルディング ブロックと要求プロバイダーの両方を参照します。 ユーザー体験を表示する、またはレンダリングするオーケストレーション手順は、対応するコンテンツ定義 ID も参照しています。

オーケストレーション手順は、オーケストレーション手順の要素で定義されている前提条件に基づいて、条件付きで実行できます。 たとえば、特定の要求が存在する場合のみ、あるいは要求が特定の値と等しい、または等しくない場合にのみオーケストレーション手順を実行するように確認できます。

オーケストレーション手順の順序付きリストを指定するために、**OrchestrationSteps** 要素がポリシーの一部として追加されます。 この要素は必須です。

**OrchestrationSteps** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | 順序付けされたオーケストレーション手順。 |

**OrchestrationStep** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| `Order` | はい | オーケストレーション手順の順序。 |
| `Type` | はい | オーケストレーション手順の種類。 指定できる値 <ul><li>**ClaimsProviderSelection** -オーケストレーション手順が、様々な要求プロバイダーをユーザーに選択肢として表示することを示します。</li><li>**CombinedSignInAndSignUp** - オーケストレーション手順がサインインとローカル アカウントのサインアップ ページを結合ソーシャル プロバイダーに提示することを示します。</li><li>**ClaimsExchange** - オーケストレーション手順が要求プロバイダーと要求を交換することを示します。</li><li>**GetClaims** - オーケストレーション手順で、`InputClaims` 構成を使用して、証明書利用者から Azure AD B2C に送信される要求データを処理するように指定します。</li><li>**InvokeSubJourney** - オーケストレーション手順によって要求が [サブ体験](subjourneys.md)と交換されることを示します (パブリック プレビュー段階)。</li><li>**SendClaims** - オーケストレーション手順が証明書利用者に対して、要求発行者によって発行されたトークンを使用して要求を送信することを示します。</li></ul> |
| ContentDefinitionReferenceId | No | このオーケストレーション手順に関連付けられた[コンテンツ定義](contentdefinitions.md)の識別子。 通常、コンテンツ定義参照識別子は、セルフアサート技術プロファイルで定義されます。 ただし、Azure AD B2C が技術プロファイルなしで何かを表示することが必要となる場合もあります。 2 つの例があります。オーケストレーション手順の種類が `ClaimsProviderSelection` または `CombinedSignInAndSignUp` の場合、Azure AD B2C は技術的なプロファイルを持たずに ID プロバイダーの選択を表示する必要があります。 |
| CpimIssuerTechnicalProfileReferenceId | No | オーケストレーション手順の種類は `SendClaims` です。 このプロパティは、証明書利用者のトークンを発行する要求プロバイダーの技術プロファイル識別子を定義します。  省略すると、証明書利用者のトークンは作成されません。 |

**OrchestrationStep** 要素には、次の要素を含めることができます。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | オーケストレーション手順を実行するために満たす必要がある前提条件の一覧。 |
| ClaimsProviderSelections | 0:n | オーケストレーション手順のための要求プロバイダーの選定一覧。 |
| ClaimsExchanges | 0:n | オーケストレーション手順のための要求交換一覧。 |
| JourneyList | 0:1 | オーケストレーション手順でのサブ体験候補の一覧。 |

### <a name="preconditions"></a>Preconditions

オーケストレーション ステップは、オーケストレーション ステップで定義されている前提条件に基づいて、条件付きで実行できます。 `Preconditions` 要素には、評価する前提条件の一覧が含まれています。 前提条件の評価が満たされると、関連付けられているオーケストレーション ステップが次のオーケストレーション ステップに進みます。 

Azure AD B2C では、一覧の順序で前提条件を評価します。 順序に基づく前提条件を使用すると、前提条件を適用する順序を設定できます。 満たされた最初の前提条件によって、後続のすべての前提条件がオーバーライドされます。 オーケストレーション ステップは、すべての前提条件が満たされていない場合にのみ実行されます。 

**Preconditions** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Precondition | 1:n | 評価する前提条件。 |

#### <a name="precondition"></a>Precondition

**Precondition** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| `Type` | はい | この前提条件に対して実行するチェックまたはクエリの種類。 値に **ClaimsExist** を指定すると、指定した要求がユーザーの現在の要求セット内に存在する場合にアクションが実行されます。または、**ClaimEquals** を指定すると、指定した要求が存在し、その値が指定値と等しい場合にアクションが実行されます。 |
| `ExecuteActionsIf` | Yes | 前提条件が満たされていると見なす方法を決定します。 指定できる値は `true`(既定値) または`false`です。 値が `true` に設定されている場合は、要求が前提条件に一致するときに、その条件が満たされていると見なされます。  値が `false` に設定されている場合は、要求が前提条件に一致しないときに、その条件が満たされていると見なされます。  |

**Precondition** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| 値 | 1:2 | 要求の種類の識別子。 要求は、ポリシー ファイルまたは親ポリシー ファイルの要求スキーマ セクションで、既に定義されています。 前提条件の種類が `ClaimEquals` の場合、2 番目の `Value` 要素には、チェックする値が含まれます。 |
| アクション | 1:1 | 前提条件の評価が満たされた場合に実行する必要のあるアクション。 指定できる値: `SkipThisOrchestrationStep`。 関連付けられているオーケストレーション ステップが次のオーケストレーション ステップに進みます。 |
  
それぞれの前提条件では 1 つの要求が評価されます。 2 種類の前提条件があります。
 
- **ClaimsExist** - 指定した要求がユーザーの現在の要求バッグに存在する場合に、アクションを実行することが指定されます。
- **ClaimEquals** - 指定した要求が存在し、その値が指定した値と等しい場合に、アクションを実行することが指定されます。 このチェックでは、大文字と小文字を区別する順序比較が実行されます。 要求の型がブール値の場合は、`True` または `False` を使用します。 

    要求が null または未初期化の場合は、`ExecuteActionsIf` が `true` か `false` かにかかわらず、前提条件は無視されます。 ベスト プラクティスとして、要求が存在することと、値が等しいことの両方を確認します。

ユーザーが `MfaPreference` を `Phone` に設定している場合に、ユーザーの MFA にチャレンジするというシナリオ例を考えます。 この条件付きロジックを実行するには、`MfaPreference` の要求が存在するかどうかを確認し、また要求の値が `Phone` に等しいかどうかを確認します。 次の XML は、このロジックを事前条件付きで実装する方法を示しています。 
  
```xml
<Preconditions>
  <!-- Skip this orchestration step if MfaPreference doesn't exist. -->
  <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
    <Value>MfaPreference</Value>
    <Action>SkipThisOrchestrationStep</Action>
  </Precondition>
  <!-- Skip this orchestration step if MfaPreference doesn't equal to Phone. -->
  <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
    <Value>MfaPreference</Value>
    <Value>Phone</Value>
    <Action>SkipThisOrchestrationStep</Action>
  </Precondition>
</Preconditions>
```

#### <a name="preconditions-examples"></a>前提条件の例

以下の前提条件は、ユーザーの objectId が存在するかどうかを確認します。 ユーザー体験では、ユーザーはローカル アカウントを使用してサインインすることを選択しています。 objectId が存在する場合は、このオーケストレーション手順をスキップしてください。

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

以下の前提条件は、ユーザーがソーシャル アカウントを使用してサインインしているかどうかを確認します。 ディレクトリ内のユーザー アカウント検索の試行が行われます。 ユーザーがローカル アカウントを使用してサインインまたはサインアップする場合は、このオーケストレーション手順をスキップしてください。

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Preconditions では複数の前提条件を確認できます。 次の例では、「objectId」または「email」が存在するかどうかを確認します。 最初の条件が true の場合、journey は次のオーケストレーション手順をスキップします。

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claims-provider-selection"></a>要求プロバイダーの選択

ID プロバイダーの選択一覧では、ユーザーはオプションの一覧からアクションを選択できます。 ID プロバイダーの選択一覧は、次の 2 つのオーケストレーション ステップのペアで構成されます。 

1. **ボタン** - ユーザーが選択可能なオプション一覧を含む `ClaimsProviderSelection` または `CombinedSignInAndSignUp` の種類から始まります。 `ClaimsProviderSelections` 要素コントロール内のオプションの順序は、ユーザーに表示されるボタンの順序です。
2. **アクション** - `ClaimsExchange` の種類が続きます。 ClaimsExchange にはアクション一覧が含まれます。 アクションは、[OAuth2](oauth2-technical-profile.md)、[OpenID Connect](openid-connect-technical-profile.md)、[claims transformation](claims-transformation-technical-profile.md)、[self-asserted](self-asserted-technical-profile.md) などの技術プロファイルへの参照です。 ユーザーがいずれかのボタンをクリックすると、対応するアクションが実行されます。

**ClaimsProviderSelections** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | 選択できる要求プロバイダーの一覧を提供します。|

**ClaimsProviderSelections** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| DisplayOption| No | 単一の要求プロバイダーの選択が可能な場合の動作を制御します。 使用可能な値: `DoNotShowSingleProvider` (既定値)。ユーザーはフェデレーション ID プロバイダーに直ちにリダイレクトされます。 または、`ShowSingleProvider`。Azure AD B2C では、単一の ID プロバイダーの選択があるサインイン ページが表示されます。 この属性を使用するには、[コンテンツ定義のバージョン](page-layout.md)が `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` 以上になっている必要があります。|

**ClaimsProviderSelection** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | いいえ | 要求プロバイダー選定の次のオーケストレーション手順で実行される、要求交換の識別子。 この属性、または ValidationClaimsExchangeId 属性を指定する必要がありますが、両方を指定することはできません。 |
| ValidationClaimsExchangeId | No | 要求プロバイダー選定を検証するために現在のオーケストレーション手順で実行される、要求交換の識別子。 この属性、または TargetClaimsExchangeId 属性を指定する必要がありますが、両方を指定することはできません。 |

### <a name="claims-provider-selection-example"></a>要求プロバイダーの選択の例

次のオーケストレーション手順では、ユーザーはFacebook、LinkedIn、Twitter、Google、またはローカル アカウントでサインインすることを選択できます。 ユーザーがいずれかのソーシャル ID プロバイダーを選択すると、`TargetClaimsExchangeId` 属性で指定された選定要求交換を使用して 2 番目のオーケストレーション手順が実行されます。 2 番目のオーケストレーション手順は、ユーザーをソーシャル ID プロバイダーにリダイレクトしてサインイン プロセスを完了します。 ユーザーがローカル アカウントを使用してサインインすることを選択した場合、Azure AD B2C は同じオーケストレーション手順にとどまり (同じサインアップ ページまたはサインイン ページ)、2 番目の手順をスキップします。

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | 使用される技術プロファイルによって、選択された ClaimsProviderSelection に従ってクライアントをリダイレクトするか、サーバーの呼び出しで要求を交換します。 |

**ClaimsExchange** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 要求交換手順の識別子。 この識別子は、ポリシーの要求プロバイダー選定手順から要求交換を参照するために使用されます。 |
| TechnicalProfileReferenceId | Yes | 実行される技術プロファイルの識別子。 |

## <a name="journeylist"></a>JourneyList

**JourneyList** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| 候補 | 1:1 | 呼び出されるサブ体験への参照。 |

### <a name="candidate"></a>候補

**Candidate** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Yes | 実行される[サブ体験](subjourneys.md)の識別子。 |
