---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 9fb462b83eccde3d2b486d491517973fe6bac4f1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133299"
---
Azure Functions の[トリガーとバインディング](..\articles\azure-functions\functions-triggers-bindings.md)はさまざまな Azure サービスと通信します。 これらのサービスと統合する際、基になる Azure サービスの API からのエラーが発生する場合があります。 エラーは、REST またはクライアント ライブラリを使用して、関数コードから他のサービスと通信しようとした場合に発生することもあります。 データの損失を防ぎ、関数を適切に動作させるには、いずれのソースからのエラーも処理することが重要です。

次のトリガーには、組み込みの再試行サポートがあります。

* [Azure BLOB Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (キュー/トピック)](../articles/azure-functions/functions-bindings-service-bus.md)

既定では、これらのトリガーは最大 5 回再試行されます。 5 回目の再試行後に、これらのトリガーは特別な[有害キュー](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)にメッセージを書き込みます。 

他の Functions トリガーでは、関数の実行時にエラーが発生した場合に組み込み再試行がありません。 関数でエラーが発生したときにトリガー情報の損失を防ぐために、関数コードで、try-catch ブロックを使用してエラーを受け取ることをお勧めします。 エラーが発生したときに、トリガーによって関数に渡される情報が特別な "有害" メッセージ キューに書き込まれます。 この方法は、[Blob ストレージ トリガー](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs)によって使用されるものと同じです。 

この方法では、エラーにより失われた可能性のあるトリガー イベントをキャプチャし、他の関数を使用してこれらを後で再試行して、格納されている情報を使って有害キューからのメッセージを処理できます。  
