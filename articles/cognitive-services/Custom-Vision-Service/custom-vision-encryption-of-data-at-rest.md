---
title: Custom Vision による保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Custom Vision による保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202230"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision による保存データの暗号化

Azure Custom Vision では、クラウドに永続化されるときにデータが自動的に暗号化されます。 Azure Custom Vision 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、2020年 5 月 11 日より後に作成されたリソースのみに使用できます。 Custom Vision と共に CMK を使用するには、新しい Custom Vision リソースを作成する必要があります。 リソースが作成されたら、Azure Key Vault を使用してマネージド ID を設定できます。

### <a name="regional-availability"></a>リージョン別の提供状況

カスタマー マネージド キーは、現在、次のリージョンで利用できます。

* 米国中南部
* 米国西部 2
* 米国東部
* US Gov バージニア州

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>次のステップ

* [Custom Vision カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


