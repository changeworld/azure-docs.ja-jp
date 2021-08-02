---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: e65a628f846892294fdd9be693b89a535ac694a0
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901449"
---
## <a name="supported-versions"></a>サポートされているバージョン

Functions ランタイムの各バージョンは、.NET の特定のバージョンと動作します。 Functions のバージョンの詳細については、「[Azure Functions ランタイム バージョンの概要](../articles/azure-functions/functions-versions.md)」を参照してください。 バージョンのサポートは、関数がイン プロセスで実行されるか、アウト プロセス (分離) で実行されるかによって異なります。 

次の表は、特定のバージョンの Functions と共に使用できる最上位レベルの .NET Core と .NET Framework を示しています。 

| Functions ランタイムのバージョン | インプロセス<br/>([.NET クラス ライブラリ](../articles/azure-functions/functions-dotnet-class-library.md)) | アウトプロセス<br/>([.NET 分離](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x<sup>1</sup> | .NET 6.0 (プレビュー)| .NET 6.0 (プレビュー) |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>2</sup> | N/A |
| Functions 1.x | .NET Framework 4.8 | N/A |

<sup>1</sup> Azure Functions では、.NET 6.0 のプレビュー リリースで実行されている関数を試すことができる試験的なサポートが提供されています。 このプレリリース バージョンは正式にはサポートされていません。 詳細については、[Azure Functions v4 の早期プレビュー](https://aka.ms/functions-dotnet6earlypreview-wiki)に関するページを参照してください。  
<sup>2</sup> 詳細については、「[Functions v2. x に関する考慮事項](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)」を参照してください。   

特定の古いマイナー バージョンの削除など、Azure Functions リリースに関する最新のニュースについては、[Azure App Service のお知らせ](https://github.com/Azure/app-service-announcements/issues)を閲覧してください。
