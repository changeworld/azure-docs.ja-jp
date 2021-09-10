---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/16/2021
ms.author: glenga
ms.openlocfilehash: bda70e418b86c44750e9c826144fce8bbc027cbb
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830533"
---
::: zone pivot="programming-language-csharp"  

HTTP トリガーとタイマー トリガーを除き、バインドは拡張機能パッケージとして実装されます。 ターミナル ウィンドウで次の [dotnet add package](/dotnet/core/tools/dotnet-add-package) コマンドを実行して、Storage 拡張機能パッケージをプロジェクトに追加します。

# <a name="in-process"></a>[インプロセス](#tab/in-process) 
```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage 
```
# <a name="isolated-process"></a>[分離プロセス](#tab/isolated-process)
```bash
dotnet add package Microsoft.Azure.Functions.Worker.Extensions.Storage.Queues --prerelease
```
---
これで、Storage の出力バインドをプロジェクトに追加できるようになります。  
::: zone-end  
