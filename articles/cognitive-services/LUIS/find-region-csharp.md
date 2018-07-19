---
title: Language Understanding (LUIS) 境界での C# による LUIS リージョンの検索 | Microsoft Docs
description: LUIS のエンドポイント キーとアプリケーション ID で公開リージョンをプログラムによって検索します。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: f0df14736e0ed47957999e3aa7c6a22b0b0c0a35
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110411"
---
# <a name="region-can-be-determined-from-api-call"></a>API 呼び出しからリージョンを特定可能 
LUIS アプリ ID と LUIS サブスクリプション ID がある場合は、エンドポイントのクエリに使用するリージョンを見つけることができます。

> [!NOTE] 
> 完成した C# ソリューションは、[**LUIS-Samples** の Github リポジトリ](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/)から入手できます。

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
