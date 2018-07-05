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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063750"
---
ローカルで関数を開発するときは、ターミナルまたはコマンド プロンプトから Azure Functions Core Tools を使って、必要な拡張機能をインストールできます。 

関数に必要なすべてのバインドを含むように *function.json* ファイルを更新した後、プロジェクト フォルダーで `func extensions install` コマンドを実行します。 コマンドで *function.json* ファイルを読み取り、必要なパッケージを確認してからインストールします。

特定のバージョンのパッケージをインストールする場合、または *function.json* ファイルを編集する前にパッケージをインストールする場合は、次の例のように `func extensions install` コマンドと共にパッケージ名を指定します。

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

`<target_version>` をパッケージの特定のバージョン (`3.0.0-beta5`など) に置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。
