---
title: Azure Data Factory の Azure 関数アクティビティ | Microsoft Docs
description: Azure 関数アクティビティを使用して、Data Factory パイプライン内で Azure 関数を実行する方法について説明します
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: douglasl
ms.openlocfilehash: 4b185236e5925152acb5f8a733e117186a2318cf
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53740894"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory の Azure 関数アクティビティ

Azure 関数アクティビティを使用すると、Data Factory パイプライン内で [Azure Functions](../azure-functions/functions-overview.md) を実行できます。 Azure 関数を実行するには、リンクされたサービスの接続と、実行する Azure 関数を指定するアクティビティを作成する必要があります。

## <a name="azure-function-linked-service"></a>Azure 関数のリンクされたサービス

Azure 関数の戻り値の型は、有効な JObject である必要があります。 それ以外の場合は失敗し、*エンドポイント呼び出し時のエラー* という一般的なユーザー エラーが発生します。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| type   | type プロパティは、次のように設定する必要があります:**AzureFunction** | はい |
| function app url | Azure 関数アプリの URL。 形式は `https://<accountname>.azurewebsites.net` です。 この URL は、Azure portal で関数アプリを表示した際に **URL** セクションに表示される値です  | はい |
| function key | Azure 関数のアクセス キーです。 それぞれの関数の **[管理]** セクションをクリックし、**ファンクション キー**または**ホスト キー**をコピーします。 詳しくは、次の記事をご覧ください:[Azure Functions の HTTP トリガーとバインド](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | はい |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 関数アクティビティ

| **プロパティ**  | **説明** | **使用できる値** | **必須** |
| --- | --- | --- | --- |
| name  | パイプラインのアクティビティの名前。  | String | はい |
| type  | アクティビティの種類は 'AzureFunctionActivity' です | String | はい |
| linked service | 対応する Azure 関数アプリの、Azure 関数のリンクされたサービス  | リンクされたサービスの参照 | はい |
| function name  | このアクティビティによって呼び出される Azure 関数アプリ内の関数の名前 | String | はい |
| method  | 関数呼び出しのための REST API メソッド | 文字列がサポートされている型:"GET"、"POST"、"PUT"   | はい |
| ヘッダー  | 要求に送信されるヘッダー。 たとえば、要求に種類と言語を設定する場合: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 文字列 (または文字列の resultType を含む式) | いいえ  |
| body  | 関数 API メソッドへの要求と共に送信される本文  | 文字列 (または文字列の resultType を含む式) またはオブジェクト。   | PUT/POST メソッドには必須です |
|   |   |   | |

「 [要求ペイロードのスキーマ](control-flow-web-activity.md#request-payload-schema) 」セクションにある要求ペイロードのスキーマを参照してください。

## <a name="more-info"></a>詳細情報

Azure 関数アクティビティでは、**ルーティング**がサポートされます。 たとえば、アプリで `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>` というルーティングを使用する場合、`functionName` は `functionName/{value}` となります。これを、実行時に目的の `functionName` を提供するようにパラメータ化できます。

また、Azure 関数アクティビティでは**クエリ**がサポートされます。 クエリは、`functionName` の一部である必要があります。たとえば、`HttpTriggerCSharp2?name=hello` です。ここで、`function name` は `HttpTriggerCSharp2` です。

## <a name="next-steps"></a>次の手順

[Azure Data Factory のパイプラインとアクティビティ](concepts-pipelines-activities.md)について学習する。