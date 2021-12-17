---
author: baanders
description: クエリ API の遅延時間に含めるメモ
ms.service: digital-twins
ms.topic: include
ms.date: 09/21/2021
ms.author: baanders
ms.openlocfilehash: 2e538b6f0197f408be649bd76cb4a15fbe22b4bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557670"
---
>[!NOTE]
>グラフのデータを変更してからそれがクエリに反映されるまで、最大 10 秒の待機時間が発生する場合があります。 
>
>[DigitalTwins API](../articles/digital-twins/concepts-apis-sdks.md#overview-data-plane-apis) を使用すると変更がすぐに反映されるため、即時の応答が必要な場合は、クエリの代わりに API 要求 ([DigitalTwins GetById](/rest/api/digital-twins/dataplane/twins/digitaltwins_getbyid)) または SDK 呼び出し ([GetDigitalTwin](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.getdigitaltwin?view=azure-dotnet&preserve-view=true)) を使用してツイン データを取得します。