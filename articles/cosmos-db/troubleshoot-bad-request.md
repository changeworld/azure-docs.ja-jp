---
title: Azure Cosmos DB の 無効な要求例外をトラブルシューティングする
description: 入力コンテンツまたはパーティション キーが無効である、パーティション キーが Azure Cosmos DB で一致しないなどの無効な要求例外を診断して修正する方法について説明します。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555871"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Azure Cosmos DB での無効な要求例外の診断とトラブルシューティング
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 状態コード 400 は、要求に無効なデータが含まれているか、必要なパラメーターが不足していることを表しています。

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a> ID プロパティがありません
このシナリオでは、次のようなエラーが表示されるのが一般的です。

*必要なプロパティ 'id; ' が不足しているため、入力内容が無効です*

このエラーを示す応答は、サービスに送信されている JSON ドキュメントに、必要な ID プロパティがないことを意味します。

### <a name="solution"></a>解決策
ドキュメントの一部として [REST の仕様](/rest/api/cosmos-db/documents)に従って、文字列値を持つ `id` プロパティを指定すると、SDK はこのプロパティの値を自動生成しません。

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a> パーティション キーの型が無効です
このシナリオでは、次のようなエラーが表示されるのが一般的です。

*パーティション キー ... が無効です。*

このエラーを示す応答は、パーティション キーの値が無効な型であることを意味します。

### <a name="solution"></a>解決策
パーティション キーの値は、文字列または数値である必要があります。値が予期される型であることを確認してください。

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a> パーティション キーの値を入力します
このシナリオでは、次のようなエラーが表示されるのが一般的です。

*ドキュメントから抽出された PartitionKey が、ヘッダーに指定された PartitionKey と一致しません*

このエラーを示す応答は、操作を実行し、予期されるプロパティのドキュメントの本文値と一致しないパーティション キー値を渡していることを意味します。 コレクションのパーティション キーのパスが `/myPartitionKey` の場合、ドキュメントには、SDK メソッドを呼び出すときにパーティション キー値として提供された値と一致しない `myPartitionKey` というプロパティがあります。

### <a name="solution"></a>解決策
ドキュメント プロパティ値に一致するパーティション キー値パラメーターを送信します。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。
* Azure Cosmos DB Java v4 SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-java-sdk-v4-sql.md)。
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) のパフォーマンス ガイドラインを確認する。
