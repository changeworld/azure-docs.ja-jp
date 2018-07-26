---
title: Language Understanding (LUIS) 境界での Node.js による LUIS リージョンの検索 | Microsoft Docs
description: LUIS のエンドポイント キーとアプリケーション ID で公開リージョンをプログラムによって検索します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: diberry
ms.openlocfilehash: 66a34b5b611934ca531e7709b13e0c6d1c794206
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222513"
---
# <a name="region-can-be-determined-from-api-call"></a>API 呼び出しからリージョンを特定可能 
LUIS アプリ ID と LUIS サブスクリプション ID がある場合は、エンドポイントのクエリに使用するリージョンを見つけることができます。

> [!NOTE] 
> 完成した Node.js ソリューションは、[**LUIS-Samples** の Github リポジトリ](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/)から入手できます。

## <a name="luis-endpoint-query-strategy"></a>LUIS エンドポイントのクエリ戦略
各 LUIS エンドポイントのクエリに以下が必要です。

* エンドポイント キー
* アプリ ID
* リージョン

LUIS エンドポイント クエリで使用されているエンドポイント キーとアプリ ID が正しく、リージョンが誤っている場合、応答コードは 401 です。 401 要求は、サブスクリプション クォータにはカウントされません。 この要求を、すべてのリージョンをポーリングして正しいリージョンを見つける戦略に変更します。 正しいリージョンは、2xx 状態コード返す要求だけです。 

|応答コード|parameters|
|--|--|
|2xx|正しいエンドポイント キー<br>正しいアプリ ID<br>正しいホスト リージョン|
|401|正しいエンドポイント キー<br>正しいアプリ ID<br>"_誤った_" ホスト リージョン|

## <a name="nodejs-code-to-find-region"></a>リージョンを見つけるための Node.js コード
コンソール アプリケーションによって、LUIS アプリ ID とエンドポイント キーが取得され、それに関連付けられているすべてのリージョンが返されます。 現時点では、リージョンによってエンドポイント キーが作成されているため、1 つのリージョンのみが返されます。

NPM 依存関係を追加します。

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

定数を追加します。 `subscriptionKey` および `appId` の変数値を自分の値に置き換えます。  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

`searchRegions` 関数を追加して、リージョンを検索します。 誤っているすべてのリージョンによって 401 が返されます。これはキャッチされ無視されます。

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

`searchRegions` 関数を呼び出して、1 つのリージョンを取得します。

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

アプリケーションが実行されると、ターミナルに、アプリ ID のリージョンが表示されます。

![LUIS リージョンが表示されているコンソール アプリのスクリーンショット](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>次の手順

LUIS [リージョン](luis-reference-regions.md)の詳細を確認します。
