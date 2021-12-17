---
title: インクルード ファイル
description: インクルード ファイル
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/17/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cc661942dc1cf110a07df383149b03c8a4ea7409
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321031"
---
特定の関数アプリ内のすべての関数のコードは、ホスト構成ファイルを含むルート プロジェクト フォルダーにあります。 [host.json](../articles/azure-functions/functions-host-json.md) ファイルにはランタイム固有の構成が含まれています。このファイルは関数アプリのルート フォルダーにあります。 *bin* フォルダーには、関数アプリに必要なパッケージやその他のライブラリ ファイルが含まれています。 関数アプリが必要とする特定のフォルダー構造は、言語によって異なります。

* [C# コンパイル済み (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# スクリプト (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# スクリプト](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)

Functions ランタイムのバージョン 2.x およびそれ以上では、関数アプリ内のすべての関数が同じ言語スタックを共有する必要があります。 
