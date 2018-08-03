---
title: LUIS キーについて - Azure | Microsoft Docs
description: Language Understanding (LUIS) キーを使用して、アプリを作成し、エンドポイントにクエリを実行します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: diberry
ms.openlocfilehash: b40ca74999be1821ffa329224ff419646591960e
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225178"
---
# <a name="keys-in-luis"></a>LUIS でのキー
LUIS では、[オーサリング](#programmatic-key)と[エンドポイント](#endpoint-key)の 2 つのキーを使用します。 オーサリング キーは、LUIS アカウントの作成時に自動的に作成されます。 LUIS アプリを公開する準備ができたら、[エンドポイント キーを作成](luis-how-to-azure-subscription.md#create-luis-endpoint-key)して LUIS アプリに[割り当て](luis-how-to-manage-keys.md#assign-endpoint-key)、これを[エンドポイント クエリで使用](#use-endpoint-key-in-query)する必要があります。 

|キー|目的|
|--|--|
|[オーサリング キー](#programmatic-key)|作成、公開、コラボレーターの管理、バージョン管理|
|[エンドポイント キー](#endpoint-key)| クエリ実行|

公開およびクエリ実行を行う[リージョン](luis-reference-regions.md#publishing-regions)内で LUIS アプリを作成することが重要です。

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>オーサリング キー

オーサリング キーは、スターター キーとも呼ばれ、LUIS アカウントの作成時に自動的に作成され、無料です。 作成する[リージョン](luis-reference-regions.md)ごとに、すべての LUIS アプリにわたって 1 つのオーサリング キーを使用します。 オーサリング キーは、LUIS アプリを作成したり、エンドポイント クエリをテストしたりするために提供されます。 

オーサリング キーを見つけるには、[LUIS](luis-reference-regions.md#luis-website) にログインし、右上のナビゲーション バーにあるアカウント名をクリックして **[アカウント設定]** を開きます。

![オーサリング キー](./media/luis-concept-keys/programatic-key.png)

**運用エンドポイント クエリ**を実行するときは、Azure [LUIS サブスクリプション](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)を作成します。 

> [!CAUTION]
> [クォータ](luis-boundaries.md#key-limits)内で提供されるエンドポイント呼び出しは少数であるため、便宜上、多くのサンプルでこのオーサリング キーを使用します。  

## <a name="endpoint-key"></a>エンドポイント キー
 **運用エンドポイント クエリ**が必要な場合は、Azure portal で [LUIS キー](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)を作成します。 キーの作成に使用した名前を覚えておいてください。アプリにこのキーを追加するときに必要になります。

LUIS サブスクリプション プロセスが完了したら、**[公開]** ページでアプリに[キーを追加](luis-how-to-manage-keys.md#assign-endpoint-key)します。 

エンドポイント キーによって、キーの作成時に指定した使用計画に基づいて、エンドポイントのクォータに到達できます。 価格情報については、「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h)」を参照してください。

エンドポイント キーは、ご使用のすべての LUIS アプリまたは特定の LUIS アプリで使用できます。 

LUIS アプリの作成には、エンドポイント キーを使用しないでください。 

## <a name="use-endpoint-key-in-query"></a>クエリでエンドポイント キーを使用する
LUIS エンドポイントでは、2 つのスタイルでクエリを使用します。その両方でエンドポイント キーを使用しますが、場所が異なります。

|動詞|URL とキーの場所の例|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>`subscription-key` のクエリ文字列値<br><br>LUIS エンドポイント キーのクォータ レートを使用するために、`subscription-key` のエンドポイント クエリ値をオーサリング (スターター) キーから新しいエンドポイント キーに変更します。 キーを作成する場合、キーを割り当てるが、サブスクリプション キーのエンドポイント クエリ値を変更しないときは、エンドポイント キー クォータは使用しません。|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> `Ocp-Apim-Subscription-Key` のヘッダー値<br><br>LUIS エンドポイント キーのクォータ レートを使用するために、`Ocp-Apim-Subscription-Key` のエンドポイント クエリ値をオーサリング (スターター) キーから新しいエンドポイント キーに変更します。 キーを作成する場合、キーを割り当てるが、`Ocp-Apim-Subscription-Key` のエンドポイント クエリ値を変更しないときは、エンドポイント キー クォータは使用しません。|

前の URL `df67dcdb-c37d-46af-88e1-8b97951ca1c2` で使用されたアプリ ID は、[対話型デモ](https://azure.microsoft.com/en-us/services/cognitive-services/language-understanding-intelligent-service/)に使用されるパブリック IoT アプリです。 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>API での Ocp-Apim-Subscription-Key の使用法
LUIS API では、ヘッダー `Ocp-Apim-Subscription-Key` を使用します。 ヘッダー名は、使用するキーと API のセットに基づいて変更されません。 ヘッダーにオーサリング API のオーサリング キーを設定します。 エンドポイントを使用している場合は、ヘッダーにエンドポイント キーを設定します。 

オーサリング API のエンドポイント キーを渡すことはできません。 それを行うと、401 エラー (サブスクリプション キーが無効なため、アクセスが拒否されました) を受信します。 

## <a name="key-limits"></a>キーの制限
[キーの制限](luis-boundaries.md#key-limits)と [Azure リージョン](luis-reference-regions.md)に関するページを参照してください。 オーサリング キーは無料で、作成に使用します。 LUIS エンドポイント キーには Free レベルがありますが、ユーザーが作成し、**[発行]** ページで LUIS アプリに関連付ける必要があります。 作成には使用できず、エンドポイント クエリにのみ使用できます。

公開リージョンは、オーサリング リージョンとは異なります。 アプリの作成は必ず、必要な公開リージョンに対応するオーサリング リージョンで行ってください。

## <a name="key-limit-errors"></a>キーの制限に関するエラー
秒あたりのクォータを超えた場合、HTTP 429 エラーが発生します。 月あたりのクォータを超えた場合、HTTP 403 エラーが発生します。 LUIS [エンドポイント](#endpoint-key) キーを取得し、そのキーを [LUIS](luis-reference-regions.md#luis-website) Web サイトの **[発行]** ページでアプリに[割り当てる](luis-how-to-manage-keys.md#assign-endpoint-key)ことによって、これらのエラーを修正します。

## <a name="next-steps"></a>次の手順

* オーサリング キーとエンドポイント キーの[概念](luis-how-to-manage-keys.md#assign-endpoint-key)について学習します。