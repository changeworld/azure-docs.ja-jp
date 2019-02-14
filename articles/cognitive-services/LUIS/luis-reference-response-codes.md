---
title: API HTTP 応答コード
titleSuffix: Azure
description: LUIS のオーサリング API とエンドポイント API からどのような HTTP 応答コードが返されるかを理解します
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: e55d0e1566edf8f9d969a16d0ae558b9831920e7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867700"
---
# <a name="common-api-response-codes-and-their-meaning"></a>一般的な API 応答コードとその意味

[オーサリング](https://aka.ms/luis-authoring-apis) API と[エンドポイント](https://aka.ms/luis-endpoint-apis) API は HTTP 応答コードを返します。 応答メッセージには、要求に固有の情報が含まれていますが、HTTP 応答の状態コードは全般的なものです。 

## <a name="common-status-codes"></a>一般的な状態コード
次の表に、[オーサリング](https://aka.ms/luis-authoring-apis) API と[エンドポイント](https://aka.ms/luis-endpoint-apis) API の最も一般的な HTTP 応答状態コードの一部を示します。

|コード|API|説明|
|:--|--|--|
|400|オーサリング、エンドポイント|"要求のパラメーターが正しくない" とは、必須パラメーターが見つからない、形式が正しくない、または大きすぎることを意味します|
|400|オーサリング、エンドポイント|"要求の本文が正しくない" とは、JSON が見つからない、形式が正しくない、または大きすぎることを意味します|
|401|Authoring|オーサリング キーではなくエンドポイント サブスクリプション キーが使用されました|
|401|オーサリング、エンドポイント|キーが無効、キーの形式が正しくない、またはキーが空です|
|401|オーサリング、エンドポイント| キーがリージョンと一致しません|
|401|Authoring|ユーザーが所有者でもコラボレーターでもありません|
|401|Authoring|API 呼び出しの順序が無効です|
|403|オーサリング、エンドポイント|月間の合計キー クォータ制限を超えました|
|409|エンドポイント|アプリケーションがまだ読み込み中です|
|410|エンドポイント|アプリケーションの再トレーニングと再公開が必要です|
|414|エンドポイント|クエリが最大文字数の制限を超えています|
|429|オーサリング、エンドポイント|レート制限を超過しています (要求数/秒)|

## <a name="next-steps"></a>次の手順

* REST API の[作成](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)と[エンドポイント](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)のドキュメント
