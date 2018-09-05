---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809721"
---
特定の関数アプリ内のすべての関数のコードは、ホスト構成ファイルと 1 つ以上のサブフォルダーを含むルート フォルダー (`wwwroot`) にあります。 各サブフォルダーには、次の例のように、個別の関数のコードが含まれています。

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

host.json ファイルにはランタイム固有の構成が含まれています。このファイルは関数アプリのルート フォルダーにあります。 使用可能な設定の詳細については、[host.json のリファレンス](../articles/azure-functions/functions-host-json.md)に関するページを参照してください。

関数にはそれぞれ、1 つ以上のコード ファイル、function.json 構成、およびその他の依存関係を含むフォルダーがあります。 C# クラス ライブラリ プロジェクトの場合、コンパイル済みのクラス ライブラリ (.dll) ファイルは `bin` サブフォルダーにデプロイされます。

