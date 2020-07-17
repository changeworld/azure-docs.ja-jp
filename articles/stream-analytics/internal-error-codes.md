---
title: Azure Stream Analytics のエラー コードについてのトラブルシューティング
description: 内部エラー コードに関する Azure Stream Analytics の問題のトラブルシューティングを行います。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 2e101747322baac860cbde02aca341171aa0cc1d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658355"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure Stream Analytics の内部エラー コード

アクティビティ ログとリソース ログを使用して、予期しない動作のデバッグを Azure Stream Analytics ジョブから行うことができます。 この記事では、すべての内部エラー コードについて説明します。 内部エラーは、エラーが内部の可用性エラーか、またはシステム内のバグかを Stream Analytics では識別できない場合に、Stream Analytics プラットフォーム内でスローされる一般的なエラーです。

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **原因**:Cosmos DB への書き込みに使用されたバッチ サイズが大きすぎます。 
* **推奨事項**:バッチ サイズを小さくして再試行してください。

## <a name="next-steps"></a>次のステップ

* [入力接続のトラブルシューティング](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics の出力のトラブルシューティング](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics のクエリのトラブルシューティング](stream-analytics-troubleshoot-query.md)
* [リソース ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics データ エラー](data-errors.md)