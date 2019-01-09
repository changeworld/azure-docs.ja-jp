---
title: データのエクスポートと削除
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Service (LUIS) はサービスを運用するために顧客コンテンツを保持していますが、LUIS ユーザーは自分のデータの表示、エクスポート、削除を完全に制御できます。 この操作は、LUIS Web ポータルまたは LUIS Programmatic API を使って行うことができます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0aba859fc0c5e2de64b2bbdb9c06f2434937f4e8
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140678"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Cognitive Services で Language Understanding (LUIS) 内の顧客データをエクスポートおよび削除する

## <a name="summary-of-customer-data-request-features"></a>顧客データ要求機能の概要
Language Understanding Intelligent Service (LUIS) はサービスを運用するために顧客コンテンツを保持していますが、LUIS ユーザーは自分のデータの表示、エクスポート、削除を完全に制御できます。 この操作は、LUIS Web [ポータル](luis-reference-regions.md)または [LUIS Programmatic API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) を使って行うことができます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

顧客コンテンツは、リージョンの Microsoft Azure Storage に暗号化されて格納され、次のものが含まれます。

- 登録時に収集されたユーザー アカウントのコンテンツ
- モデルの構築に必要なトレーニング データ (つまり、意図とエンティティ)
- ユーザー モデルの改善に役立つ、実行時にログに記録されるユーザー クエリ
  - ユーザーは、要求に `&log=false` を追加することによってクエリのログ記録を無効にできます。詳しくは、[こちら](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances)をご覧ください。

## <a name="deleting-customer-data"></a>顧客データの削除
LUIS ユーザーは、LUIS Web ポータルまたは LUIS Programmatic API を使用して、ユーザー コンテンツの削除を完全に制御できます。 次の表では、両方を支援するリンクを示します。

| | **ユーザー アカウント** | **アプリケーション** | **発話** | **エンドユーザー クエリ** |
| --- | --- | --- | --- | --- |
| **ポータル** | [リンク](luis-how-to-account-settings.md) | [リンク](luis-how-to-start-new-app.md#delete-app) | [リンク](luis-how-to-start-new-app.md#delete-app) | [リンク](luis-how-to-start-new-app.md#delete-app) |
| **API** | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>顧客データのエクスポート
LUIS ユーザーは、ポータルでデータの表示を完全に制御できますが、LUIS Programmatic API を使ってエクスポートする必要があります。 次の表では、LUIS Programmatic API でのデータのエクスポートに役立つリンクを示します。

| | **ユーザー アカウント** | **アプリケーション** | **発話** | **エンドユーザー クエリ** |
| --- | --- | --- | --- | --- |
| **API** | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [LUIS リージョン リファレンス](./luis-reference-regions.md)
