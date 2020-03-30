---
title: カスタム サブドメイン
titleSuffix: Azure Cognitive Services
description: 各 Cognitive Services リソースのカスタム サブドメイン名は、Azure portal、Azure Cloud Shell、または Azure CLI を使用して作成します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647685"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Cognitive Services のカスタム サブドメイン名

Azure Cognitive Services では、[Azure portal](https://portal.azure.com)、[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)、または [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) を通じて作成された各リソースに対して、カスタム サブドメイン名が使用されます。 特定の Azure リージョンのすべてのお客様に共通だった、リージョン エンドポイントとは異なり、カスタム サブドメイン名はリソースに対して一意です。 認証で Azure Active Directory (Azure AD) などの機能を有効にするには、カスタム サブドメイン名が必要です。

## <a name="how-does-this-impact-existing-resources"></a>これは既存のリソースにどのように影響しますか?

2019 年 7 月 1 日より前に作成された Cognitive Services リソースでは、関連付けられたサービスのリージョン エンドポイントが使用されます。 これらのエンドポイントは、既存および新しいリソースで動作します。

既存のリソースを移行してカスタム サブドメイン名を活用し、Azure AD などの機能を有効にできるようにするには、次の手順に従います。

1. Azure portal にサインインし、カスタム サブドメイン名を追加する Cognitive Services リソースを見つけます。
2. **[概要]** ブレードで、 **[カスタム ドメイン名を生成します]** を見つけて選択します。
3. パネルが開き、リソースの一意のカスタム サブドメインを作成する手順が示されます。
   > [!WARNING]
   > カスタム サブドメインの名前は、作成後に変更することは**できません**。

## <a name="do-i-need-to-update-my-existing-resources"></a>既存のリソースを更新する必要はありますか?

いいえ。 リージョン エンドポイントは、新しいおよび既存の Cognitive Services に対して引き続き機能します。カスタム サブドメイン名は省略可能です。 カスタム サブドメイン名が追加されている場合でも、リージョン エンドポイントはリソースで引き続き機能します。

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>SDK でリソースのリージョンが要求された場合はどうすればよいですか?

> [!WARNING]
> 現時点では、Speech Services でカスタム サブドメインはサポート**されていません**。 Speech Services と関連する SDK を使用する場合は、リージョン エンドポイントを使用してください。

リージョン エンドポイントとカスタム サブドメイン名は両方サポートされており、どちらを使用しても変わりはありません。 ただし、完全なエンドポイントが必要です。

リージョン情報は、**Azure portal** のご利用のリソースの [[概要]](https://portal.azure.com) ブレードで確認できます。 リージョン エンドポイントの完全な一覧については、[Is リージョン エンドポイントの一覧はありますか?](#is-there-a-list-of-regional-endpoints) に関するページをご覧ください。

## <a name="are-custom-subdomain-names-regional"></a>カスタム サブドメイン名はリージョン単位ですか?

はい。 カスタム サブドメイン名を使用しても、Cognitive Services リソースのリージョンについては何も変わりません。

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>カスタム サブドメイン名にはどのような要件がありますか?

カスタム サブドメイン名はリソースに対して一意です。 名前に含めることができるのは、英数字と `-` 文字のみです。長さは 2 文字から 64 文字である必要があり、末尾を `-` にすることはできません。

## <a name="can-i-change-a-custom-domain-name"></a>カスタム ドメイン名を変更できますか?

いいえ。 カスタム サブドメイン名は、作成されてリソースに関連付けられた後は、変更することはできません。

## <a name="can-i-reuse-a-custom-domain-name"></a>カスタム ドメイン名を再利用できますか?

各カスタム サブドメイン名は一意であるため、Cognitive Services リソースに割り当てたカスタム サブドメイン名を再利用するには、既存のリソースを削除する必要があります。 リソースが削除された後、カスタム サブドメイン名を再利用できます。

## <a name="is-there-a-list-of-regional-endpoints"></a>リージョン エンドポイントの一覧はありますか?

はい。 これは、Azure Cognitive Services リソースで使用できるリージョン エンドポイントの一覧です。

> [!NOTE]
> Translator Text API と Bing Search API では、グローバル エンドポイントが使用されます。

| エンドポイントの種類 | リージョン | エンドポイント |
|---------------|--------|----------|
| パブリック | グローバル (Translator Text と Bing) | `https://api.cognitive.microsoft.com` |
| | オーストラリア東部 | `https://australiaeast.api.cognitive.microsoft.com` |
| | ブラジル南部 | `https://brazilsouth.api.cognitive.microsoft.com` |
| | カナダ中部 | `https://canadacentral.api.cognitive.microsoft.com` |
| | 米国中部 | `https://centralus.api.cognitive.microsoft.com` |
| | 東アジア | `https://eastasia.api.cognitive.microsoft.com` |
| | East US | `https://eastus.api.cognitive.microsoft.com` |
| | 米国東部 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | フランス中部 | `https://francecentral.api.cognitive.microsoft.com` |
| | インド中部 | `https://centralindia.api.cognitive.microsoft.com` |
| | 東日本 | `https://japaneast.api.cognitive.microsoft.com` |
| | 韓国中部 | `https://koreacentral.api.cognitive.microsoft.com` |
| | 米国中北部 | `https://northcentralus.api.cognitive.microsoft.com` |
| | 北ヨーロッパ | `https://northeurope.api.cognitive.microsoft.com` |
| | 南アフリカ北部 | `https://southafricanorth.api.cognitive.microsoft.com` |
| | 米国中南部 | `https://southcentralus.api.cognitive.microsoft.com` |
| | 東南アジア | `https://southeastasia.api.cognitive.microsoft.com` |
| | 英国南部 | `https://uksouth.api.cognitive.microsoft.com` |
| | 米国中西部 | `https://westcentralus.api.cognitive.microsoft.com` |
| | 西ヨーロッパ | `https://westeurope.api.cognitive.microsoft.com` |
| | 米国西部 | `https://westus.api.cognitive.microsoft.com` |
| | 米国西部 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov バージニア州 | `https://virginia.api.cognitive.microsoft.us` |
| 中国 | 中国東部 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | 中国北部 | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>参照

* [Azure Cognitive Services とは](Welcome.md)
* [認証](authentication.md)
