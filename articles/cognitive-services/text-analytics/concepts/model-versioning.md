---
title: Text Analytics v3 でモデルのバージョンを指定する
titleSuffix: Azure Cognitive Services
description: データで使用される Text Analytics API モデルを指定する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: d53c1c137e8359e3379e2403cf655c1f1dc592f5
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016401"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Text Analytics API でのモデルのバージョン管理

Text Analytics API のバージョン 3 を使用すると、データで使用されるモデルのバージョンを選択できます。 API 要求でモデルのバージョンを選択するには、省略可能な `model-version` パラメーターを使用します。 (例: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`)。 このパラメーターが指定されていない場合、この API の既定値は最新の安定バージョンになります。 

## <a name="available-versions"></a>使用可能なバージョン

下の表を使用して、各エンドポイントでサポートされているモデルのバージョンを確認してください。


| エンドポイント                        | サポートされているバージョン                                     | 最新バージョン |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`               | `2020-09-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


これらのモデルの更新情報の詳細については、[新機能](../whats-new.md)に関する記事を参照してください。

## <a name="text-analytics-for-health"></a>Text Analytics for Health

[Text Analytics for Health](../how-tos/text-analytics-for-health.md) コンテナーでは、上記の API エンドポイントとは異なるモデルのバージョン管理が使用されています。  コンテナー イメージごとに使用できるモデルのバージョンは 1 つだけであることに注意してください。

| エンドポイント                        | コンテナー イメージ タグ                     | モデル バージョン |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `1.1.013530001-amd64-preview` または最新          | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>次のステップ

* [Text Analytics の概要](../overview.md)
* [感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [エンティティの認識](../how-tos/text-analytics-how-to-entity-linking.md)
