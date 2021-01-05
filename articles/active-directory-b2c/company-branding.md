---
title: 組織のサインイン ページにブランドを追加する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C ページに組織のブランドを追加する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111049"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>組織の Azure Active Directory B2C ページにブランドを追加する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory [[会社のブランド]](../active-directory/fundamentals/customize-branding.md) 機能を使用して、バナー ロゴ、背景画像、背景色で Azure AD B2C ページをカスタマイズできます。 会社のブランドには、サインアップ、サインイン、プロファイル編集、パスワード リセットが含まれます。 

> [!TIP]
> バナー ロゴ、背景画像、背景色以外のユーザー フロー ページのその他の側面をカスタマイズするには、「[HTML テンプレートを使用して UI をカスタマイズする](customize-ui-with-html.md)」方法をご覧ください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


ユーザー フロー ページをカスタマイズするには、まず Azure Active Directory で会社のブランドを構成し、次に Azure AD B2C のユーザー フローのページ レイアウトでこれを有効にします。

## <a name="configure-company-branding"></a>会社のブランドの構成

まず、 **[会社のブランド]** でバナー ロゴ、背景画像、および背景色を設定します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[管理]** から **[会社のブランド]** を選択します。
1. 「[組織の Azure Active Directory のサインイン ページにブランドを追加する](../active-directory/fundamentals/customize-branding.md)」の手順に従います。

Azure AD B2C で会社のブランドを構成する際は、次の点に注意してください。

* 現在、Azure AD B2C の会社のブランドは、**背景画像**、**バナー ロゴ**、**背景色** のカスタマイズに制限されています。 **[詳細設定]** などの会社のブランド ペインのその他のプロパティは *サポートされていません*。
* ユーザー フロー ページでは、背景画像が読み込まれる前に背景色が表示されます。 よりスムーズな読み込みエクスペリエンスのため、背景画像の色とほぼ同じ背景色を選択することをお勧めします。
* バナー ロゴは、ユーザーがサインアップ ユーザー フローを開始するときに、ユーザーに送信された確認メールに表示されます。

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>ユーザー フロー ページでのブランド化の有効化

会社のブランドを構成したら、それをユーザー フローで有効にします。

1. Azure portal の左側のメニューで、 **[Azure AD B2C]** を選択します。
1. **[ポリシー]** で **[ユーザー フロー (ポリシー)]** を選択します。
1. 会社のブランド化を有効にするユーザー フローを選択します。 会社のブランド化は、標準の "*サインイン*" および標準の "*プロファイル編集*" ユーザー フローの種類では **サポートされていません**。
1. **[カスタマイズ]** で、 **[ページ レイアウト]** を選択して、ブランド化するレイアウトを選択します。 たとえば、 **[統合されたサインアップまたはサインイン ページ]** を選択します。
1. **[ページ レイアウト バージョン (プレビュー)]** では、バージョン **1.2.0** 以上を選択します。
1. **[保存]** を選択します。

ユーザー フローのすべてのページをブランド化する場合は、ユーザー フローのページ レイアウトごとにページ レイアウト バージョンを設定します。

![Azure portal での Azure AD B2C のページ レイアウトの選択](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>ページ レイアウトを選択する

会社のブランドを有効にするには、カスタム ポリシーのコンテンツ定義の "*すべて*" に対して、ページ `contract` バージョンを使用して[ページ レイアウト バージョンを定義する](contentdefinitions.md#migrating-to-page-layout)必要があります。 値の形式には、次のように `contract` という語が含まれている必要があります: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_。 古い **DataUri** 値を使用するカスタム ポリシーでページ レイアウトを指定するには、次の操作を行います。

ページ バージョンで[ページ レイアウトに移行する](contentdefinitions.md#migrating-to-page-layout)方法について説明します。

次の例は、コンテンツ定義識別子と、対応する **DataUri** をページ コントラクトと共に示しています。 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

次の例では、オーシャン ブルー テンプレートを使用する *[サインアップとサインイン]* ユーザー フロー ページのカスタム バナー ロゴと背景画像を示します。

![Azure AD B2C によって提供されるブランド化されたサインアップ/サインイン ページ](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>カスタム HTML で会社のブランド資産を使用する

[カスタム HTML](customize-ui-with-html.md) で会社のブランド資産を使用するには、`<div id="api">` タグの外側に次のタグを追加します。

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

画像ソースが背景画像とバナー ロゴの画像に置き換えられます。

## <a name="next-steps"></a>次のステップ

[Azure Active Directory B2C でのアプリケーションのユーザー インターフェイスのカスタマイズ](customize-ui-with-html.md)に関する記事を参照して、アプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細を確認します。