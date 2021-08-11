---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 07/13/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 766e135cbccf86884786c2b888fcb1ea9d98ecc7
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2021
ms.locfileid: "113688667"
---
App Service のアプリケーション コード内から生成されたコンソール ログにアクセスするには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行して、診断ログを有効にします。

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --docker-container-logging filesystem --level Verbose
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
