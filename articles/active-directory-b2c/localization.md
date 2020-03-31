---
title: ローカライズ - Azure Active Directory B2C
description: Azure Active Directory B2C でカスタム ポリシーの Localization 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126761"
---
# <a name="localization"></a>ローカリゼーション

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 要素を使用すると、ユーザー体験に関するポリシーで複数のロケールや言語をサポートすることができます。 ポリシーでのローカライズのサポートにより、次のことが可能になります。

- ポリシーでサポートされている言語の明示的な一覧を設定し、既定の言語を選択する。
- 言語固有の文字列とコレクションを指定する。

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Localization** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| 有効 | いいえ | 指定できる値: `true` または `false`。 |

**Localization** 要素には、次の XML 要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | サポートされている言語の一覧。 |
| LocalizedResources | 0:n | ローカライズされたリソースの一覧。 |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| DefaultLanguage | はい | ローカライズされたリソースの既定値として使用される言語。 |
| MergeBehavior | いいえ | 同じ識別子を持つ親ポリシーに存在するすべての ClaimType と一緒にマージされる、値の列挙値。 基本ポリシーで指定された要求を上書きする場合は、この属性を使用します。 指定できる値: `Append`、`Prepend`、または `ReplaceAll`。 `Append` 値は、存在するデータのコレクションを、親ポリシーで指定したコレクションの末尾に追加する必要があることを指定します。 `Prepend` 値は、存在するデータのコレクションを、親ポリシーで指定したコレクションの前に追加する必要があることを指定します。 `ReplaceAll` 値は、親ポリシーで定義されているデータのコレクションを、現在のポリシーで定義されているデータを代わりに使用して無視する必要があることを指定します。 |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | 「RFC 5646 - 言語を識別するタグ」に従って、言語タグに適合する内容を表示します。 |

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | ローカライズされたリソースを一意に識別するために使用される識別子。 |

**LocalizedResources** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | さまざまなカルチャのコレクション全体を定義します。 1 つのコレクションに、さまざまなカルチャに対する異なる数の項目と異なる文字列を含めることができます。 コレクションの例として、要求の種類に表示される列挙が挙げられます。 たとえば、国/リージョン一覧がドロップダウンリストでユーザーに表示されます。 |
| LocalizedStrings | 0:n | さまざまなカルチャのすべての文字列 (コレクションに表示される文字列を除く) を定義します。 |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | サポートされている言語の一覧。 |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| ElementType | はい | ポリシー ファイル内の ClaimType 要素またはユーザー インターフェイス要素を参照します。 |
| ElementId | はい | **ElementType** が ClaimType に設定されている場合に使用される、ClaimsSchema セクションで定義済みの要求の種類への参照を含む文字列。 |
| TargetCollection | はい | ターゲット コレクション。 |

**LocalizedCollection** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| アイテム | 0:n | ユーザーが要求についてユーザー インターフェイスで選択可能なオプション (ドロップダウン リストの値など) を定義します。 |

**Item** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| Text | はい | このオプションのユーザー インターフェイスでユーザーに表示する必要がある、ユーザーフレンドリーな表示文字列。 |
| 値 | はい | このオプションの選択に関連付けられている要求の文字列値。 |
| SelectByDefault | いいえ | このオプションが既定で UI で選択するかどうかを示します。 使用可能な値: True または False。 |

次の例は、**LocalizedCollections** 要素の使用を示しています。 これには、2 つの**LocalizedCollection** 要素が含まれています。1 つは英語用、もう 1 つはスペイン語用です。 両方とも、英語とスペイン語の項目の一覧を使用して、要求 **の**Restriction`Gender` コレクションを設定します。

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

**LocalizedStrings** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | ローカライズされた文字列。 |

**LocalizedString** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| ElementType | はい | ポリシー内の要求の種類要素またはユーザー インターフェイス要素への参照。 指定できる値: `ClaimType`、`UxElement`、`ErrorMessage`、`Predicate`、または `GetLocalizedStringsTransformationClaimType`。 `ClaimType` 値は、StringId で指定された、要求属性のいずれかをローカライズするために使用されます。 `UxElement` 値は、StringId で指定されたユーザー インターフェイス要素のいずれかをローカライズするために使用されます。 `ErrorMessage` 値は、StringId で指定されたシステム エラー メッセージのいずれかをローカライズするために使用されます。 `Predicate` 値は、StringId で指定された、[Predicate](predicates.md) エラー メッセージのいずれかをローカライズするために使用されます。 `InputValidation` 値は、StringId で指定された [PredicateValidation](predicates.md) グループ エラー メッセージのいずれかをローカライズするために使用されます。 `GetLocalizedStringsTransformationClaimType` 値は、ローカライズされた文字列を要求にコピーするために使用されます。 詳細については、[GetLocalizedStringsTransformation 要求変換](string-transformations.md#getlocalizedstringstransformation)に関する記事を参照してください。  | 
| ElementId | はい | **ElementType** が `ClaimType`、`Predicate`、または `InputValidation` に設定されている場合、この要素には ClaimsSchema セクションで定義済みの要求の種類への参照が含まれます。 |
| StringId | はい | **ElementType** が `ClaimType` に設定されている場合、この要素には要求の種類の属性への参照が含まれます。 指定できる値: `DisplayName`、`AdminHelpText`、または `PatternHelpText`。 `DisplayName` 値は、要求の表示名を設定するために使用されます。 `AdminHelpText` 値は、要求ユーザーのヘルプ テキスト名を設定するために使用されます。 `PatternHelpText` 値は、要求パターンのヘルプ テキストを設定するために使用されます。 **ElementType** が `UxElement` に設定されている場合、この要素にはユーザー インターフェイス要素の属性への参照が含まれます。 **ElementType** が `ErrorMessage` に設定されている場合、この要素はエラー メッセージの識別子を指定します。 [ 識別子の完全な一覧については、「](localization-string-ids.md)ローカライズ文字列 ID`UxElement`」を参照してください。|


次の例は、ローカライズされたサインアップ ページを示しています。 最初の 3 つの **LocalizedString** 値は、要求属性を設定します。 4 つ目の値は、続行ボタンの値を変更します。 最後の値は、エラーメッセージを変更します。

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

次の例は、Id **を持つ**Predicate**のローカライズされた**UserHelpText`IsLengthBetween8And64` を示しています。 また、Id **を持つ**PredicateValidation**の Id** を持つ `CharacterClasses`PredicateGroup**のローカライズされた**UserHelpText`StrongPassword` を示しています。

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>ローカライズを設定する

この記事では、ユーザー体験に関するポリシーで複数のロケールや言語をサポートする方法について説明します。 ローカライズには、3 つのステップ (サポートされている言語の明示的な一覧の設定、言語固有の文字列とコレクションの指定、およびページのContentDefinition の編集) が必要です。

### <a name="set-up-the-explicit-list-of-supported-languages"></a>サポートされている言語の明示的な一覧を設定する

**BuildingBlocks** 要素の下に、サポートされている言語の一覧を含む **Localization** 要素を追加します。 次の例は、英語 (既定値) とスペイン語の両方に対するローカライズのサポートを定義する方法を示しています。

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>次のステップ

ローカライズの例については、次の記事を参照してください。

- [Azure Active Directory B2C でのカスタム ポリシーによる言語のカスタマイズ](custom-policy-localization.md)
- [Azure Active Directory B2C でのユーザー フローによる言語のカスタマイズ](user-flow-language-customization.md)
