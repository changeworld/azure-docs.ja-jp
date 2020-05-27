---
title: 外部可用性のエラー コード - Azure Stream Analytics
description: 外部可用性のエラー コードに関する Azure Stream Analytics の問題のトラブルシューティングを行います。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 092b548e73330fe80ef10b2e25d9ab7883b256ac
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595201"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Azure Stream Analytics の外部可用性エラー コード

アクティビティ ログとリソース ログを使用して、予期しない動作のデバッグを Azure Stream Analytics ジョブから行うことができます。 この記事では、すべての外部可用性エラー コードについて説明します。 外部可用性エラーは、依存サービスを使用できない場合に発生します。

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **原因**:サービスが一時的に利用できません。
* **推奨事項**:Stream Analytics は引き続きサービスへの接続を試みます。 

## <a name="kafkaservernotavailable"></a>KafkaServerNotAvailable

* **原因**:Kafka サーバーを利用できません。

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **原因**:EventHub との通信中に、Stream Analytics でエラーが発生しました。 


## <a name="next-steps"></a>次のステップ

* [入力接続のトラブルシューティング](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics の出力のトラブルシューティング](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics のクエリのトラブルシューティング](stream-analytics-troubleshoot-query.md)
* [リソース ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics データ エラー](data-errors.md)
