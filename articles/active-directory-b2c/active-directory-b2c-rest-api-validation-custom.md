---
title: "Azure AD B2C によるカスタム ポリシーでの検証としての REST API 要求交換の統合 | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーに関するトピック"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 46abe48c3c9a7aab3fe013811d088a63957fe500
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-validation-on-user-input"></a>チュートリアル: REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合する

ID 開発者は、Azure AD B2C の基盤となる **Identity Experience Framework** (IEF) を使用して、RESTful API との対話をユーザー体験に統合できます。  

このチュートリアルの最後では、RESTful サービスと対話する Azure AD B2C ユーザー体験を作成することができます。

IEF では、要求を介してデータが送受信されます。  API との対話は、REST API 要求交換として、またはオーケストレーション手順内で使用される検証プロファイルとして設計できます。

- 通常は、これを使用してユーザーからの入力を検証します。
- ユーザーからの値が拒否された場合は、有効な値の入力を再度試みることができるように、エラー メッセージがユーザーに返されます。

対話はオーケストレーション手順として設計することもできます。 詳細については、「[チュートリアル: REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](active-directory-b2c-rest-api-step-custom.md)」を参照してください。

検証プロファイルの例として、スターター パック ファイル ProfileEdit.xml のプロファイルの編集ユーザー体験を使用します。

プロファイルの編集でユーザーによって指定された名前が除外リストに含まれていないことを確認できます。

## <a name="prerequisites"></a>前提条件

- [概要](active-directory-b2c-get-started-custom.md)に関するページに記載されたローカル アカウントのサインアップ/サインインを完了するための Azure AD B2C テナントの構成。
- 対話する REST API エンドポイント。 デモ サイト [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) には、このチュートリアルで使用する REST API サービスが設定されています。

## <a name="step-1---prepare-the-rest-api-function"></a>手順 1 - REST API 関数を準備する

> [!NOTE]
> REST API 関数の設定は、この記事の範囲外です。 [Azure Function App](https://docs.microsoft.com/azure/azure-functions/functions-reference) は、クラウドで RESTful サービスを作成する優れたツールキットを提供します。

"playerTag" として期待する要求を受け取り、この要求が存在するかどうかを検証する Azure 関数を既に作成してあります。 完全な Azure 関数のコードは、[GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples) から入手できます。

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

Azure 関数によって返される `userMessage` 要求は Identity Experience Framework によって期待され、検証が失敗した場合 (たとえば、上記の例の 409 競合状態が返されたとき) に文字列としてユーザーに表示されます。

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>手順 2 - RESTful API 要求交換を技術プロファイルとして TrustFrameworkExtensions.xml ファイルに構成する

技術プロファイルは、RESTful サービスで必要となる交換の完全な構成です。 `TrustFrameworkExtensions.xml` ファイルを開き、次の XML スニペットを `<ClaimsProviders>` 要素内に追加します。

> [!NOTE]
> 以下でプロトコルとして記述されている "Restful Provider, Version 1.0.0.0" は、外部サービスと対話する関数として考えてください。  スキーマの完全な定義の取得先: <!-- TODO: Link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims` 要素は、IEF から REST サービスに送信される要求を定義します。 上の例では、要求 `givenName` の内容が `playerTag` として REST サービスに送信されます。 この例では、IEF は要求が返されることを期待せず、代わりに REST サービスからの応答を待機し、受信した状態コードに基づいて動作します。

## <a name="step-3---include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-wish-to-validate-the-user-input"></a>手順 3 - ユーザー入力を検証するセルフアサート技術プロファイルに RESTful サービス要求交換を含める

検証手順は、ユーザーとの対話で使用するのが最も一般的です。  ユーザー入力が期待されるすべての対話は、**セルフアサート技術プロファイル**です。 この例では、この検証を **Self-Asserted-ProfileUpdate** 技術プロファイル (TP) に追加します。  これは、RP ポリシー ファイル `Profile Edit` によって使用される TP です。

要求交換をセルフアサート技術プロファイルに追加するには:

1. TrustFrameworkBase ファイルを開き、`<TechnicalProfile Id="SelfAsserted-ProfileUpdate">` を検索します。
2. この TP の構成を調べて、ユーザーとの交換が、ユーザーに対する要求 (入力要求) とセルフ アサート プロバイダーから返されることが期待される要求 (出力要求) として、どのように定義されているかを確認します。
3. `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate` を検索します。このプロファイルは、`<UserJourney Id="ProfileEdit">` のオーケストレーション手順 6. として起動されます。

## <a name="step-4---upload-and-test-the-profile-edit-rp-policy-file"></a>手順 4 - プロファイルの編集 RP ポリシー ファイルをアップロードし、テストする

1. `TrustframeworkExtensions` ファイルの新しいバージョンをアップロードします。
2. **[今すぐ実行]** を使用してプロファイルの編集 RP ポリシー ファイルをテストします。
3. **[Given Name]\(名\)** フィールドにいずれかの既存の名前 (たとえば、mcvinny) を入力して、検証機能をテストします。 すべてが適切に設定されている場合、`player tag` が既に使用されていることをユーザーに通知するメッセージが表示されます。

## <a name="next-steps"></a>次のステップ

[プロファイルの編集とユーザー登録を変更してユーザーから追加情報を収集する方法](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[チュートリアル: REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](active-directory-b2c-rest-api-step-custom.md)

