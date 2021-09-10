---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 4f2aa82388882c192213b168faedd4f61069ae64
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829550"
---
C# プロジェクトでは、バインドは関数メソッドのバインド属性として定義されます。 具体的な定義は、お使いのアプリがインプロセス (C# クラス ライブラリ) で実行されるのか分離プロセスで実行されるのかによって異なります。  

# <a name="in-process"></a>[インプロセス](#tab/in-process)

*HttpExample.cs* プロジェクト ファイルを開いて、`Run` メソッドの定義に次のパラメーターを追加します。

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg` パラメーターは `ICollector<T>` 型です。これは、関数の完了時に出力バインドに書き込まれるメッセージのコレクションを表します。 この場合、出力は `outqueue` という名前のストレージ キューです。 このストレージ アカウントの接続文字列は、`StorageAccountAttribute` で設定されます。 この属性は、ストレージ アカウントの接続文字列を含む設定を示し、クラス、メソッド、パラメーター レベルで適用できます。 この例では、既定のストレージ アカウントを既に使用しているため、`StorageAccountAttribute` を省略できます。

Run メソッドの定義は次のようになります。  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::

# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)

*HttpExample.cs* プロジェクト ファイルを開き、次の `MultiResponse` クラスを追加します。

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="33-38":::

`MultiResponse` クラスを使用すると、`outqueue` という名前のストレージ キューと HTTP 成功メッセージの両方に書き込むことができます。 `QueueOutput` 属性は文字列配列に適用されるので、複数のメッセージをキューに送信できます。 

このストレージ アカウントの接続文字列は、`Connection` プロパティによって設定されます。 この例では、既定のストレージ アカウントを既に使用しているため、`Connection` を省略できます。

---