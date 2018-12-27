---
title: LUIS での C# を使用したエンドポイントのリージョンの検索
titleSuffix: Azure Cognitive Services
description: LUIS のエンドポイント キーとアプリケーション ID で公開リージョンをプログラムによって検索します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 53c3d1abb24ae0d5b33a2a100dda07fd20ae92d1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039634"
---
# <a name="find-endpoint-region-with-c"></a>C# を使用したエンドポイントのリージョンの検索 
LUIS アプリ ID と LUIS サブスクリプション ID がある場合は、エンドポイントのクエリに使用するリージョンを見つけることができます。

> [!NOTE] 
> 完成した C# ソリューションは、[**LUIS-Samples** の GitHub リポジトリ](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/)から入手できます。

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

## <a name="c-class-code-to-find-region"></a>リージョンを見つけるための C# クラス コード
コンソール アプリケーションによって、LUIS アプリ ID とエンドポイント キーが取得され、それに関連付けられているすべてのリージョンが返されます。 現時点では、リージョンによってエンドポイント キーが作成されているため、1 つのリージョンのみが返されます。

.Net ライブラリの依存関係を追加します。

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

この組み込まれたカスタム LUIS クラスを追加して、リージョンを見つけます。 `luisAppId` および `luisSubscriptionKey` の変数値を自分の値に置き換えます。 401 を返すすべてのリージョンが、デバッグ コンソールに書き込まれます。 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

これは、コンソール アプリケーションの Main メソッドでカスタム LUIS クラスを呼び出す例です。

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

アプリケーションが実行されると、コンソールに、アプリ ID のリージョンが表示されます。

![LUIS リージョンが表示されているコンソール アプリのスクリーンショット](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>次の手順

LUIS [リージョン](luis-reference-regions.md)の詳細を確認します。
