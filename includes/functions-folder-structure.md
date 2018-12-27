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
ms.openlocfilehash: 2808264b4641bda49a53677ebe216a3b53b7d0d9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293753"
---
特定の関数アプリ内のすべての関数のコードは、ホスト構成ファイルと 1 つ以上のサブフォルダーを含むルート プロジェクト フォルダーにあります。 各サブフォルダーには、次の図のように、個別の関数のコードが含まれています。

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Functions ランタイムのバージョン 2.x では、関数アプリ内のすべての関数が同じ言語ワーカーを共有する必要があります。  

[host.json](../articles/azure-functions/functions-host-json.md) ファイルにはランタイム固有の構成が含まれています。このファイルは関数アプリのルート フォルダーにあります。 `bin` フォルダーには、関数アプリに必要なパッケージやその他のライブラリ ファイルが保存されています。 関数アプリ プロジェクトの言語固有の要件を参照してください。

* [C# クラス ライブラリ (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# スクリプト (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# スクリプト](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



