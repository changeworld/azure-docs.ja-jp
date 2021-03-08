---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558022"
---
> [!NOTE]
> `az webapp up` コマンドは、次の処理を実行します。
>
>- 既定の[リソース グループ](/cli/azure/group?view=azure-cli-latest#az-group-create)を作成する。
>
>- 既定の [App Service プラン](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)を作成する。
>
>- 指定された名前で[アプリを作成](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)する。
>
>- 現在の作業ディレクトリからアプリにファイルを [zip してデプロイ](../articles/app-service/deploy-zip.md)する。
>