---
title: Azure Active Directory B2C カスタム ポリシーでの言語カスタマイズ | Microsoft Docs
description: カスタム ポリシー内でローカライズ コンテンツを使用して、複数の言語に対応する方法について学習します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6269ac65e5db20521346d5312bcbadd0905c36e2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440566"
---
# <a name="language-customization-in-custom-policies"></a>カスタム ポリシーでの言語のカスタマイズ

> [!NOTE]
> この機能はパブリック プレビュー段階にあります。
> 

カスタム ポリシーでは、言語のカスタマイズは組み込みのポリシーと同様に動作します。  パラメーターとブラウザーの設定に基づいて言語がどのように選択されるかの動作については、組み込みの[ドキュメント](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization)を参照してください。

## <a name="enable-supported-languages"></a>サポートされている言語を有効化する
ui-locales が指定されなかった場合は、ユーザーのブラウザーで、これらの言語のうちどれを使用するかが尋ねられます (サポートされている言語がユーザーに表示されます)。  

サポートされている言語は、`<BuildingBlocks>` で次の形式で定義されます。

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

既定の言語とサポートされている言語は、組み込みのポリシーでの動作と同様に動作します。

## <a name="enable-custom-language-strings"></a>カスタム言語文字列を有効化する

カスタム言語文字列を作成するには、次の 2 つの手順が必要です。
1. ページの `<ContentDefinition>` を編集して、目的の言語のリソース ID を指定します
2. 対応する ID を指定した `<LocalizedResources>` を、`<BuildingBlocks>` 内に作成します

`<ContentDefinition>` と `<BuildingBlock>` は、継承するすべてのポリシーに変更を反映するかどうかに応じて、拡張ファイルと依存するポリシー ファイルの両方に配置できます。

### <a name="edit-the-contentdefinition-for-the-page"></a>ページの ContentDefinition を編集する

ローカライズするページごとに、各言語コードに対して参照する言語リソースを `<ContentDefinition>` で指定できます。

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

このサンプルでは、フランス語 (fr) と英語 (en) のカスタム文字列が、サインアップまたはサインインの統合ページに追加されています。  各 `LocalizedResourcesReference` の `LocalizedResourcesReferenceId` はそれらのロケールと同じですが、ID には任意の文字列を使用することができます。  ユーザーは、言語とページの組み合わせごとに、対応する `<LocalizedResources>` を作成する必要があります (下記を参照)。


### <a name="create-the-localizedresources"></a>LocalizedResources を作成する

オーバーライドは `<BuildingBlocks>` 内に含まれ、各ページの `<ContentDefinition>` で指定したページと言語のごとに、`<LocalizedResources>` が存在します。  各オーバーライドは、次のサンプルに示すように、`<LocalizedString>` として指定されます。

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

このページには、次の 4 つのタイプの文字列要素があります。

**ClaimsProvider** - ID プロバイダー (Facebook、Google、Azure AD など) のラベル**ClaimType** - 属性とそれに対応するヘルプ テキスト、またはフィールド検証エラーのラベル **UxElement** - ページに既定で存在するその他の文字列要素 (ボタン、リンク、テキストなど) **ErrorMessage** - フォーム検証エラー メッセージ

これらのオーバーライドを使用するページに対して、`StringId` が一致していることを確認してください。一致していないと、アップロード時にポリシー検証によってブロックされます。  