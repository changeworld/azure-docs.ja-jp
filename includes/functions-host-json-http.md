---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 5abefd55aa06f53bc3b437b29a2582b3e2239a65
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133715"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|routePrefix|api|すべてのルートに適用されるルート プレフィックス。 既定のプレフィックスを削除するには、空の文字列を使用します。 |
|maxOutstandingRequests|200*|特定の時点で保持される未処理の要求の最大数。 この制限には、キューに格納され、まだ実行が開始されていない要求と、処理中の実行が含まれます。 この制限を超える受信要求は、429 "Too Busy" 応答で拒否されます。 これにより、呼び出し元は時間ベースの再試行戦略を採用でき、要求の最大待機時間の制御にも役立ちます。 この設定は、スクリプト ホストの実行パス内で発生するキューのみを制御します。 ASP.NET 要求キューなどの他のキューは有効なままで、この設定の影響を受けません。 *バージョン 1.x の既定値は unbounded です。 従量課金プランでのバージョン 2.x の既定値は 200 です。 専用プランでのバージョン 2.x の既定値は unbounded です。|
|maxConcurrentRequests|100*|並列で実行される HTTP 関数の最大数。 これによりコンカレンシーを制御でき、リソース使用率の管理に役立ちます。 たとえば、多くのシステム リソース (メモリ、CPU、ソケット) を消費する HTTP 関数があった場合、コンカレンシー率が高すぎると問題が発生します。 または、サードパーティのサービスに対して要求を送信する関数があり、その呼び出し速度を制限する必要がある場合です。 このような場合は、調整を適用することができます。 *バージョン 1.x の既定値は unbounded です。 従量課金プランでのバージョン 2.x の既定値は 100 です。 専用プランでのバージョン 2.x の既定値は unbounded です。|
|dynamicThrottlesEnabled|true*|この設定を有効にすると、要求処理パイプラインが、システム パフォーマンス カウンター (接続、スレッド、プロセス、メモリ、CPU など) を定期的にチェックし、カウンターのいずれかが組み込まれた上限閾値 (80%) を超えた場合は、カウンターが正常なレベルに戻るまで要求は 429 "Too Busy" 応答で拒否されます。 *バージョン 1.x の既定値は false です。 従量課金プランでのバージョン 2.x の既定値は true です。 専用プランでのバージョン 2.x の既定値は false です。|
