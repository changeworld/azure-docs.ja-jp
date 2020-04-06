---
title: Face サービスによる保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Face サービスによる保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372032"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Face サービスによる保存データの暗号化

Face サービスでは、クラウドに永続化されるときにデータが自動的に暗号化されます。 Face サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、E0 価格レベルでのみ利用できます。 カスタマー マネージド キーを使用できるようにするには、[Face サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 要求の状態について連絡を差し上げるまで、約 3 から 5 営業日かかります。 要求によっては、お客様は待ち行列に登録され、スペースが利用できるようになってから承認される場合があります。 Face サービスでの CMK の使用が承認されたら、新しい Face リソースを作成し、価格レベルとして E0 を選択する必要があります。 E0 価格レベルで Face リソースを作成したら、Azure Key Vault を使用してマネージド ID を設定できます。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>次のステップ

* [Face サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


