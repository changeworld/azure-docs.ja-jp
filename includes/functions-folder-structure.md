---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fd220a4a955a8b2cd583c2fe9e3899cb36c1587d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935897"
---
特定の関数アプリ内のすべての関数のコードは、ホスト構成ファイルと 1 つ以上のサブフォルダーを含むルート プロジェクト フォルダーにあります。 各サブフォルダーには、個別の関数のコードが含まれています。 フォルダー構造は、次の図のように表示されます。

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Functions ランタイムのバージョン 2.x およびそれ以上では、関数アプリ内のすべての関数が同じ言語スタックを共有する必要があります。  

[host.json](../articles/azure-functions/functions-host-json.md) ファイルにはランタイム固有の構成が含まれています。このファイルは関数アプリのルート フォルダーにあります。 *bin* フォルダーには、関数アプリに必要なパッケージやその他のライブラリ ファイルが含まれています。 関数アプリ プロジェクトの言語固有の要件を参照してください。

* [C# クラス ライブラリ (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# スクリプト (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# スクリプト](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
