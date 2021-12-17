---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 4c16fc8a6f497a05294a9b7357b8ffc4dab285f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741378"
---
#### <a name="built-in-log-streaming"></a>組み込みのログ ストリーミング

次の例のように、[`func azure functionapp logstream` コマンド](../articles/azure-functions/functions-core-tools-reference.md#func-azure-functionapp-list-functions)を使用して、Azure 内で実行されている特定の関数アプリのストリーミング ログの受信を開始します。

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
