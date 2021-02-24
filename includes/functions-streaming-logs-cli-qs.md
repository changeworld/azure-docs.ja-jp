---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424853"
---
凖リアルタイムの[ストリーミング ログ](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)を表示するには、次のコマンドを実行します。

```console
func azure functionapp logstream <APP_NAME> 
```

別のターミナル ウィンドウまたはブラウザーで、もう一度リモート関数を呼び出します。 Azure で実行された関数の詳細ログがターミナルに表示されます。 