---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822810"
---
コンテナー内から生成されたコンソール ログにアクセスできます。

まず、次のコマンドを実行して、コンテナーのログ記録をオンにします。

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

`<app-name>` と `<resource-group-name>` は、Web アプリに適した名前に置き換えます。

コンテナーのログ記録がオンになったら、次のコマンドを実行して、ログのストリームを確認します。

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

任意のタイミングでログのストリーミングを停止するには、**Ctrl**+**C** キーを押します。

ブラウザーから `https://<app-name>.scm.azurewebsites.net/api/logs/docker` でログ ファイルを検査することもできます。
