---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 7537ad41aaac6fab1eb591300b561bba4167d85d
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829551"
---
# <a name="in-process"></a>[インプロセス](#tab/in-process)

`msg` 出力バインド オブジェクトを使用してキュー メッセージを作成するコードを追加します。 このコードは、メソッドから制御が戻る前に追加します。

:::code language="csharp" range="28-32" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" :::

この時点で、関数は次のようになります。

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-36":::

# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

既存の `HttpExample` クラスを次のコードに置き換えます。

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="11-32":::

---