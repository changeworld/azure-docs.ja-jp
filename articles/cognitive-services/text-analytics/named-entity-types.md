---
title: 固有表現認識でサポートされるカテゴリ
titleSuffix: Azure Cognitive Services
description: Text Analytics API でサポートされているエンティティのカテゴリについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/03/2021
ms.author: aahi
ms.openlocfilehash: 9c2cdcaa5aaadf9b1314b436fce4ab747bebdf8c
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527606"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Text Analytics API v3 でサポートされているエンティティのカテゴリ

この記事では、[固有表現認識 ](how-tos/text-analytics-how-to-entity-linking.md) (NER) から取得できるエンティティのカテゴリを紹介しています。 NER では、予測モデルを実行して、入力ドキュメントから固有表現を識別して分類します。

NER v3.1 のプレビューも提供されており、そちらには、個人情報 (`PII`) と医療情報 (`PHI`) を検出する機能が備わっています。 また、 **[正常性]** タブをクリックして、Text Analytics で正常性に対してサポートされているカテゴリの一覧を表示します。 

[移行ガイド](migration-guide.md?tabs=named-entity-recognition)で、バージョン 2.1 で返される種類の一覧を確認できます。

## <a name="entity-categories"></a>エンティティのカテゴリ

#### <a name="general"></a>[全般](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[正常性](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>次のステップ

* [Text Analytics で固有表現抽出
を使用する方法](how-tos/text-analytics-how-to-entity-linking.md)
