---
title: Form Recognizer サービスによる保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Form Recognizer による保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: cafe170c4f4485791bbd65471a43d1d5f9726775
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202222"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Form Recognizer による保存データの暗号化

Azure Form Recognizer では、クラウドに永続化されるときにデータが自動的に暗号化されます。 Azure Form Recognizer の暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、2020年 5 月 11 日より後に作成されたリソースのみに使用できます。 Form Recognizer と共に CMK を使用するには、新しい Form Recognizer リソースを作成する必要があります。 リソースが作成されたら、Azure Key Vault を使用してマネージド ID を設定できます。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>次のステップ

* [Form Recognizer カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


