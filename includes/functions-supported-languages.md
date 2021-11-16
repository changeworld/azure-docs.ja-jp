---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2e5699a4522ef9da30b51a97eba49d7bc6e2c001
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025983"
---
|Language                                 |1.x         |2.x| 3.x | 4.x |
|-----------------------------------------|------------|---| --- | --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>1</sup>)| GA (.NET Core 3.1)<br/>[GA (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) | GA (.NET 6.0) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Node 6)|GA (Node 10、8)| GA (Node 14、12、10) | GA (Node 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>1</sup>)| GA (.NET Core 3.1) | GA (.NET 6.0) |
|[Java](../articles/azure-functions/functions-reference-java.md)|該当なし|GA (Java 8)| GA (Java 11、8)| GA (Java 11、8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |該当なし|GA (PowerShell Core 6)| GA (PowerShell 7.0、Core 6)| GA (PowerShell 7.0)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|該当なし|GA (Python 3.7、3.6)| GA (Python 3.9、3.8、3.7、3.6)| GA (Python 3.9、3.8)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript)<sup>2</sup> |該当なし|GA| GA | GA |

<sup>1</sup> ランタイム バージョン 2.x を対象とする .NET クラス ライブラリのアプリは、.NET Core 2.x 互換モードで .NET Core 3.1 で実行されます。 詳細については、「[Functions v2.x の考慮事項](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)」を参照してください。  
<sup>2</sup> JavaScript へのトランスパイリングによってサポートされます。

サポートされている言語バージョンの詳細については、言語固有の開発者ガイドに関する記事をご覧ください。   
言語サポートの計画的な変更については、「[Azure ロードマップ](https://azure.microsoft.com/roadmap/?tag=functions)」を参照してください。
