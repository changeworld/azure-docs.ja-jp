---
title: Language Understanding (LUIS) API の HTTP 応答コード - Azure | Microsoft Docs
titleSuffix: Azure
description: LUIS のオーサリング API とエンドポイント API からどのような HTTP 応答コードが返されるかを理解します
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 04/16/2018
ms.author: v-geberr
ms.openlocfilehash: 9c7381d9dc2ecf302c85c6b4f1f24b24a28d9ebe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374501"
---
# <a name="luis-api-http-response-codes"></a>LUIS API の HTTP 応答コード
[オーサリング](https://aka.ms/luis-authoring-apis) API と[エンドポイント](https://aka.ms/luis-endpoint-apis) API は HTTP 応答コードを返します。 応答メッセージには、要求に固有の情報が含まれていますが、HTTP 応答の状態コードは全般的なものです。 

## <a name="common-status-codes"></a>一般的な状態コード
次の表に、[オーサリング](https://aka.ms/luis-authoring-apis) API と[エンドポイント](https://aka.ms/luis-endpoint-apis) API の最も一般的な HTTP 応答状態コードの一部を示します。

|コード|API|説明|
|:--|--|--|
|400|オーサリング、エンドポイント|"要求のパラメーターが正しくない" とは、必須パラメーターが見つからない、形式が正しくない、または大きすぎることを意味します|
|400|オーサリング、エンドポイント|"要求の本文が正しくない" とは、JSON が見つからない、形式が正しくない、または大きすぎることを意味します|
|401|作成|オーサリング キーではなくエンドポイント サブスクリプション キーが使用されました|
|401|オーサリング、エンドポイント|キーが無効、キーの形式が正しくない、またはキーが空です|
|401|オーサリング、エンドポイント| キーがリージョンと一致しません|
|401|作成|ユーザーが所有者でもコラボレーターでもありません|
|401|作成|API 呼び出しの順序が無効です|
|403|オーサリング、エンドポイント|月間の合計キー クォータ制限を超えました|
|409|エンドポイント|アプリケーションがまだ読み込み中です|
|410|エンドポイント|アプリケーションの再トレーニングと再公開が必要です|
|414|エンドポイント|クエリが最大文字数の制限を超えています|
|429|オーサリング、エンドポイント|レート制限を超過しています (要求数/秒)|