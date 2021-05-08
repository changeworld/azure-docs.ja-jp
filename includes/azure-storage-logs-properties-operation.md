---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011185"
---
| プロパティ | 説明 |
|:--- |:---|
|**time** | ストレージで要求が受信された協定世界時 (UTC) での時刻。 (例: `2018/11/08 21:09:36.6900118`)。|
|**resourceId** | ストレージ アカウントのリソース ID。 例: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | 要求された操作のカテゴリ。 例: `StorageRead`、`StorageWrite`、または`StorageDelete`。|
|**operationName** | 実行された REST 操作の種類。 <br> 操作の完全な一覧については、[「ストレージ分析のログに記録された操作とステータスメッセージ」のトピック](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)を参照してください。 |
|**operationVersion** | 要求が行われたときに指定されたストレージ サービスのバージョン。 これは、**x-ms-version** ヘッダーの値と同じです (例: `2017-04-17`)。|
|**schemaVersion** | ログのスキーマ バージョン (例: `1.0`)。|
|**statusCode** | 要求の HTTP ステータス コード。 要求が中断された場合、この値は `Unknown` に設定される可能性があります。 <br> 例: `206` |
|**statusText** | 要求された操作のステータス。  ステータス メッセージの完全な一覧については、[「ストレージ分析のログに記録された操作とステータスメッセージ」のトピック](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)を参照してください。 バージョン 2017-04-17 以降では、ステータス メッセージ `ClientOtherError` は使用されません。 代わりに、このフィールドにはエラー コードが含まれています。 例: `SASSuccess`  |
|**durationMs** | 要求された操作の実行に要した合計の時間 (ミリ秒単位)。 これには、受信要求を読み取り、要求元に応答を送信する時間が含まれます (例: `12`)。|
|**callerIpAddress** | ポート番号を含む要求元の IP アドレス (例: `192.100.0.102:4362`)。 |
|**correlationId** | リソース間でログを関連付けるために使用される ID (例: `b99ba45e-a01e-0042-4ea6-772bbb000000`)。 |
|**location** | ストレージ アカウントの場所 (例: `North Europe`)。 |
|**protocol**|操作で使用されるプロトコル。 例: `HTTP`、`HTTPS`、`SMB`、`NFS`|
| **uri** | 要求された Uniform Resource Identifier |