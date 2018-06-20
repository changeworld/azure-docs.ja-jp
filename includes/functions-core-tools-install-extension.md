---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726889"
---
ローカルで関数を開発するときは、ターミナルまたはコマンド プロンプトから Azure Functions Core Tools を使って、必要な拡張機能をインストールできます。 

関数に必要なすべてのバインドを含むように *function.json* ファイルを更新した後、プロジェクト フォルダーで `func extensions install` コマンドを実行します。 コマンドで *function.json* ファイルを読み取り、必要なパッケージを確認してからインストールします。

特定のバージョンのパッケージをインストールする場合、または *function.json* ファイルを編集する前にパッケージをインストールする場合は、次の例のように `func extensions install` コマンドと共にパッケージ名を指定します。

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

`<target_version>` をパッケージの特定のバージョンに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。
