---
title: ページ レイアウトを選択する - Azure Active Directory B2C
description: Azure Active Directory B2C でページ レイアウトを選択する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 452687f3886a85bea796e3899410667ee1d592fa
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840317"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>カスタム ポリシーを使用して Azure Active Directory B2C でページ レイアウトを選択する | Microsoft Docs

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

ユーザー フローを使用しているか、カスタム ポリシーを使用しているかに関係なく、Azure Active Directory B2C (Azure AD B2C) ポリシーでクライアント側 JavaScript コードを有効にできます。 アプリケーションで JavaScript を有効にするには、[カスタム ポリシー](custom-policy-overview.md)に要素を追加し、ページ レイアウトを選択して、要求内で [b2clogin.com](b2clogin.md) を使用します。

ページ レイアウトは、Azure AD B2C が提供する要素とお客様が指定するコンテンツの関連付けです。

この記事では、カスタム ポリシーでページ レイアウトを構成して、Azure AD B2C でページ レイアウトを選択する方法について説明します。

> [!NOTE]
> ユーザー フローに対して JavaScript を有効にする場合、「[Azure Active Directory B2C における JavaScript とページ レイアウトのバージョン](user-flow-javascript-overview.md)」を参照してください。

## <a name="replace-datauri-values"></a>DataUri 値を置換する

カスタム ポリシー内には、ユーザー体験で使用される HTML テンプレートを定義する、[ContentDefinitions](contentdefinitions.md) が存在する場合があります。 **ContentDefinition** には、Azure AD B2C によって提供されるページ要素を参照する **DataUri** が含まれています。 **LoadUri** は、指定した HTML コンテンツと CSS コンテンツへの相対パスです。

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

ページ レイアウトを選択するには、ポリシーで [ContentDefinitions](contentdefinitions.md) 内の **DataUri** 値を変更します。 古い **DataUri** 値から新しい値に切り替えると、不変パッケージを選択することになります。 このパッケージを使用するメリットは、それが変更されたり、ページ上で予期しない動作が生じたりすることがないとわかっている点です。

古い **DataUri** 値を使用するカスタム ポリシーでページ レイアウトを指定するには、`elements` とページ タイプ (`selfasserted` など) の間に `contract` を挿入して、バージョン番号を指定します。 次に例を示します。

| 古い DataUri 値 | 新しい DataUri 値 |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>バージョンの変更ログ

ページ レイアウト パッケージは定期的に更新され、ページ要素に修正と機能強化が加えられます。 次の変更ログは、各バージョンで導入された変更を示しています。

### <a name="200"></a>2.0.0

- セルフアサート ページ (`selfasserted`)
  - カスタム ポリシーでの[表示コントロール](display-controls.md)のサポートが追加されました。

### <a name="120"></a>1.2.0

- すべてのページ
  - アクセシビリティの修正
  - HTML タグに `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。 シナリオには以下が含まれます。
    - これを CSS リンクで使用して、HTML と同時に CSS を読み込みます。これにより、それらのファイルの読み込みと読み込みの間に起こる "ちらつき" がなくなります
    - この属性を使用すると、ページの読み込みの前に、スクリプト タグをフェッチして実行する順序を制御できます
  - メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
  - Chrome 翻訳のサポート
- 統合されたセルフアサート ページ
  - [ユーザー名]、[電子メール]、および [パスワード] の各フィールドで、HTML の form 要素が使用されるようになりました。  これにより、Edge と IE でこの情報を適切に保存できるようになりました

### <a name="110"></a>1.1.0

- 例外ページ (globalexception)
  - アクセシビリティの修正
  - 連絡先がない場合のポリシーからの既定のメッセージが削除されました
  - 既定の CSS が削除されました
- MFA ページ (多要素)
  - [Confirm Code]\(コードの確認\) ボタンが削除されました
  - コードの入力フィールドは、最大 6 文字の入力のみを受け取るようになりました
  - 6 桁のコードを入力すると、入力したコードの確認がページによって自動的に試行されます。ボタンをクリックする必要はありません
  - コードが正しくない場合、入力フィールドは自動的にクリアされます
  - 正しくないコードが 3 回試行されると、B2C から証明書利用者にエラーが送信されます
  - アクセシビリティの修正
  - 既定の CSS が削除されました
- セルフアサート ページ (selfasserted)
  - キャンセル アラートが削除されました
  - エラー要素の CSS クラス
  - エラーの表示/非表示ロジックが改善されました
  - 既定の CSS が削除されました
- SSP が統一されました (unifiedssp)
  - サインインしたままにする (KMSI) コントロールが追加されました

### <a name="100"></a>1.0.0

- 最初のリリース

## <a name="next-steps"></a>次のステップ

アプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細については、「[Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](custom-policy-ui-customization.md)」を参照してください。
