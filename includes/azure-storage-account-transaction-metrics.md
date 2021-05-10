---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ac91ef5a56fe234cba47b430b78e74ce81c9d504
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96536989"
---
Azure Storage は、Azure Monitor で次のトランザクション メトリックを提供します。

| メトリック | 説明 |
| ------------------- | ----------------- |
| トランザクション | ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 <br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> 適用可能なディメンション:ResponseType、GeoType、ApiName、Authentication ([定義](#metrics-dimensions))<br/> 値の例:1024 |
| イングレス | イングレス データの量。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。 <br/><br/> 単位:バイト <br/> 集計の種類:合計 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| エグレス | エグレス データの量。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。 <br/><br/> 単位:バイト <br/> 集計の種類:合計 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| SuccessServerLatency | Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。 <br/><br/> 単位:ミリ秒 <br/> 集計の種類:Average <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| SuccessE2ELatency | ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。 SuccessE2ELatency 値と SuccessServerLatency 値の差は、ネットワークやクライアントによって発生する可能性のある待機時間を示します。<br/><br/> 単位:ミリ秒 <br/> 集計の種類:Average <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| 可用性 | ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、合計課金対象要求数の値を取得し、それを該当する要求の数 (予期しないエラーになった要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。 <br/><br/> 単位:Percent <br/> 集計の種類:Average <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:99.99 |
