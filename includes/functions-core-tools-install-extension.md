---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044511"
---
ローカルで関数を開発するときは、ターミナルまたはコマンド プロンプトから Azure Functions Core Tools を使って、必要な拡張機能をインストールできます。

関数に必要なすべてのバインドを含むように *function.json* ファイルを更新した後、プロジェクト フォルダーで以下のコマンドを実行します。

```bash
func extensions install
```

コマンドは、*function.json* ファイルを読み取って必要なパッケージを確認して、パッケージをインストールして、拡張プロジェクトを再構築します。 現在のバージョンで新しいバインドが追加されますが、既存のバインドは更新されません。 新しいバージョンをインストールするときに、`--force` オプションを使用して既存のバインドを最新バージョンに更新します。

特定のバージョンのパッケージをインストールする場合、または *function.json* ファイルを編集する前にパッケージをインストールする場合は、次の例のように `func extensions install` コマンドと共にパッケージ名を指定します。

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

`<target_version>` をパッケージの特定のバージョン (`3.0.0-beta5`など) に置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。
