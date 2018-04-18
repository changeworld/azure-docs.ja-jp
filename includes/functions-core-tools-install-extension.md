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
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
ローカルで関数を開発するときは、ターミナルまたはコマンド プロンプトから Azure Functions Core Tools を使って、必要な拡張機能をインストールできます。 次の `func extensions install` コマンドは、Azure Cosmos DB バインド拡張機能をインストールします。

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

`<taget_version>` をパッケージの特定のバージョンに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。
