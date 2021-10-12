---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/18/2021
ms.author: glenga
ms.openlocfilehash: ed981abd0ff849fb2a88164b2814794a48603ce7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559908"
---
この記事では、次の 2 種類のコンパイル済み C# 関数の作成をサポートしています。 

| 実行モデル | 説明 |
| --- | --- |
| **[インプロセス](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=in-process)**| 関数コードは、Functions ホスト プロセスと同じプロセスで実行されます。 詳細については、「[Azure Functions を使用する C# クラス ライブラリ関数を開発する](../articles/azure-functions/functions-dotnet-class-library.md)」を参照してください。 |
| **[分離プロセス](../articles/azure-functions/create-first-function-cli-csharp.md?tabs=isolated-process)**| 関数コードは、別の .NET ワーカー プロセスで実行されます。 詳細については、「[Azure において関数を .NET 5.0 で実行するためのガイド](../articles/azure-functions/dotnet-isolated-process-guide.md)」を参照してください。 |