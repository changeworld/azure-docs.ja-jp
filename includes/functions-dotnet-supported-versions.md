---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: 729275766806bf5fe47d35b04ddc58b11c58217b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908369"
---
## <a name="supported-versions"></a>サポートされているバージョン

Functions ランタイムの各バージョンは、.NET の特定のバージョンと動作します。 Functions のバージョンの詳細については、「[Azure Functions ランタイム バージョンの概要](../articles/azure-functions/functions-versions.md)」を参照してください。 バージョンのサポートは、関数がイン プロセスで実行されるか、アウト プロセス (分離) で実行されるかによって異なります。 

次の表は、特定のバージョンの Functions と共に使用できる最上位レベルの .NET Core と .NET Framework を示しています。 

| Functions ランタイムのバージョン | インプロセス<br/>([.NET クラス ライブラリ](../articles/azure-functions/functions-dotnet-class-library.md)) | アウトプロセス<br/>([.NET 分離](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x (プレビュー) | .NET 6.0 (プレビュー)| .NET 6.0 (プレビュー)<sup>2</sup> |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>1</sup> | N/A |
| Functions 1.x | .NET Framework 4.8 | N/A |

<sup>1</sup> 詳細については、「[Functions v2. x に関する考慮事項](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)」を参照してください。    
<sup>2</sup> 現在、Azure Functions Core Tools を使用してのみ、分離プロセス関数を作成できます。 詳細については、「[クイック スタート: Azure でコマンド ラインから C# 関数を作成する](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)」を参照してください。  

特定の古いマイナー バージョンの削除など、Azure Functions リリースに関する最新のニュースについては、[Azure App Service のお知らせ](https://github.com/Azure/app-service-announcements/issues)を閲覧してください。
