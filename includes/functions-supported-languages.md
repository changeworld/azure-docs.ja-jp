---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 76bfd886b471e5e4e2c3fa8a6733dcb67e7ea77d
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113493237"
---
|Language                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-dotnet-class-library.md)<sup>1</sup>|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>2</sup>)| GA (.NET Core 3.1)<br/>[GA (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Node 6)|GA (Node 10、8)| GA (Node 14、12、10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.8)|GA (.NET Core 2.1<sup>2</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|該当なし|GA (Java 8)| GA (Java 11、8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |該当なし|GA (PowerShell Core 6)| GA (PowerShell 7.0、Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|該当なし|GA (Python 3.7、3.6)| GA (Python 3.8、3.7、3.6) <br/> プレビュー (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |該当なし|GA<sup>3</sup>| GA<sup>3</sup> |

<sup>1</sup> .NET 6.0 プレビューを使用できる Azure Functions の試験的なバージョンを利用できます。 詳細については、[Azure Functions v4 の早期プレビュー](https://aka.ms/functions-dotnet6earlypreview-wiki)に関するページを参照してください。
<sup>2</sup> ランタイム バージョン 2.x を対象とする .NET クラス ライブラリのアプリは、.NET Core 2.x 互換モードで .NET Core 3.1 で実行されます。 詳細については、[Functions v2. x に関する考慮事項](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)に関するページを参照してください。  
<sup>3</sup> JavaScript へのトランスパイリングによってサポートされます。

サポートされている言語バージョンの詳細については、言語固有の開発者ガイドに関する記事をご覧ください。   
言語サポートの計画的な変更については、「[Azure ロードマップ](https://azure.microsoft.com/roadmap/?tag=functions)」を参照してください。
