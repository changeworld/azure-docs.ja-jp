---
title: リファレンス - Azure API Management リソース ログ
description: Azure API Management GatewayLogs リソース ログのスキーマ リファレンス
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 064ac21e01239f7be3ddca9e48089ce880c6af58
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379898"
---
# <a name="reference-api-management-resource-log-schema"></a>リファレンス: API Management リソース ログのスキーマ

この記事では、Azure API Management GatewayLogs リソース ログのスキーマ リファレンスを提供します。 ログ エントリには、[上位の共通スキーマ](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema)のフィールドも含まれています。

API Management のリソース ログの収集を有効にする方法については、「[発行された API を監視する](api-management-howto-use-azure-monitor.md#resource-logs)」の該当セクションを参照してください。

## <a name="gatewaylogs-schema"></a>GatewayLogs のスキーマ

API 要求ごとに、次のプロパティがログに記録されます。

| プロパティ  | Type | 説明 |
| ------------- | ------------- | ------------- |
| method | string | 受信要求の HTTP メソッド |
| url | string | 受信要求の URL |
| responseCode | 整数 (integer) | クライアントに送信された HTTP 応答の状態コード |
| responseSize | 整数 (integer) | 要求の処理中にクライアントに送信されたバイト数 | 
| cache | string | 要求の処理における API Management キャッシュ使用の状態 (ヒット、ミス、なし) | 
| apiId | string | 現在の要求の API エンティティ識別子 | 
| operationId | string | 現在の要求の操作エンティティ識別子 | 
| clientProtocol | string | 受信要求の HTTP プロトコル バージョン |
| clientTime | 整数 (integer) | クライアントの I/O 全体 (接続バイト、送信バイト、受信バイト) に費やされたミリ秒数 | 
| apiRevision | string | 現在の要求の API リビジョン | 
| clientTlsVersion| string | 要求を送信するクライアントによって使用される TLS のバージョン |
| lastError | object | 失敗した要求の場合、最後の要求処理エラーの詳細 | 
| backendMethod | string | バックエンドに送信された要求の HTTP メソッド |
| backendUrl | string | バックエンドに送信された要求の URL |
| backendResponseCode | 整数 (integer) | バックエンドから受信した HTTP 応答のコード |
| backedProtocol | string | バックエンドに送信された要求の HTTP プロトコル バージョン |
| backendTime | 整数 (integer) | バックエンドの IO 全体 (接続バイト、送信バイト、受信バイト) に費やされたミリ秒数 | 


## <a name="next-steps"></a>次の手順

* API Management の API を監視する方法については、「[発行された API を監視する](api-management-howto-use-azure-monitor.md)」を参照してください
* [Azure リソース ログの共通およびサービス固有のスキーマ](../azure-monitor/platform/resource-logs-schema.md)について、さらに学習してください

