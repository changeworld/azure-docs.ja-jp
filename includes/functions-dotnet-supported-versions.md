---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: f576840f87387ba6896c6fda3a5ef663cedaffaf
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132040280"
---
## <a name="supported-versions"></a>サポートされているバージョン

Functions ランタイムの各バージョンは、.NET の特定のバージョンと動作します。 Functions のバージョンの詳細については、「[Azure Functions ランタイム バージョンの概要](../articles/azure-functions/functions-versions.md)」を参照してください。 バージョンのサポートは、関数がイン プロセスで実行されるか、アウト プロセス (分離) で実行されるかによって異なります。 

次の表は、特定のバージョンの Functions と共に使用できる最上位レベルの .NET Core と .NET Framework を示しています。 

| Functions ランタイムのバージョン | インプロセス<br/>([.NET クラス ライブラリ](../articles/azure-functions/functions-dotnet-class-library.md)) | アウトプロセス<br/>([.NET 分離](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x | .NET 6.0 | .NET 6.0 |
| Functions 3.x | .NET Core 3.1 | .NET 5.0<sup>1</sup> |
| Functions 2.x | .NET Core 2.1<sup>2</sup> | N/A |
| Functions 1.x | .NET Framework 4.8 | N/A |


<sup>1</sup> ビルドプロセスには [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download) も必要です。   
<sup>2</sup> 詳細については、「[Functions v2. x に関する考慮事項](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)」を参照してください。     

特定の古いマイナー バージョンの削除など、Azure Functions リリースに関する最新のニュースについては、[Azure App Service のお知らせ](https://github.com/Azure/app-service-announcements/issues)を閲覧してください。
