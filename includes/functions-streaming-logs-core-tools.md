---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164868"
---
#### <a name="built-in-log-streaming"></a>組み込みのログ ストリーミング

次の例のように、`logstream` オプションを使用して、Azure 内で実行されている特定の関数アプリのストリーミング ログの受信を開始します。

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>従量課金プランの場合、組み込みのログ ストリーミングは、Linux で実行されている関数アプリの Core Tools でまだ有効になっていません。 これらのホスティング プランでは、代わりに Live Metrics Stream を使用してログをほぼリアルタイムで表示する必要があります。

#### <a name="live-metrics-stream"></a>ライブ メトリック ストリーム

次の例に示すように、`--browser` オプションを含めることで、関数アプリの [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) を新しいブラウザー ウィンドウで表示できます。

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
