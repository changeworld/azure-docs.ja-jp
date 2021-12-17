---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: 7257f70acb8b931791f3f08437ba61b05e1b4f6d
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026686"
---
この記事では、次の 2 種類のコンパイル済み C# 関数の作成をサポートしています。 

| 実行モデル | 説明 |
| --- | --- |
| **[インプロセス](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| 関数コードは、Functions ホスト プロセスと同じプロセスで実行されます。 .NET Core 3.1 と .NET 6.0 の両方をサポートします。 詳細については、「[Azure Functions を使用する C# クラス ライブラリ関数を開発する](../articles/azure-functions/functions-dotnet-class-library.md)」を参照してください。 |
| **[分離プロセス](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| 関数コードは、別の .NET ワーカー プロセスで実行されます。 .NET 5.0 と .NET 6.0 の両方をサポートします。 詳細については、[C# での分離プロセス関数の開発](../articles/azure-functions/dotnet-isolated-process-guide.md)に関する記事を参照してください。 |