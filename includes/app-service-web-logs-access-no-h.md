---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 7803ac9009af1657e7f162d656898492a694e28f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997932"
---
App Service のアプリケーション コード内から生成されたコンソール ログにアクセスするには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行して、診断ログを有効にします。

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

`--level` で有効な値は、`Error`、`Warning`、`Info`、および `Verbose` です。 後続の各レベルには、前のレベルが含まれます。 たとえば、`Error` にはエラー メッセージのみが含まれ、`Verbose` にはすべてのメッセージが含まれます。

診断ログがオンになったら、次のコマンドを実行して、ログのストリームを確認します。

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

> [!NOTE]
> `https://<app-name>.scm.azurewebsites.net/api/logs/docker` で、ブラウザーからログ ファイルを検査することもできます。

任意のタイミングでログのストリーミングを停止するには、`Ctrl`+`C` と入力します。
