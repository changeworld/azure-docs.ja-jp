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
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244611"
---
> [!NOTE]
> `az webapp up` コマンドは、次の処理を実行します。
>
>- 既定の[リソース グループ](/cli/azure/group#az-group-create)を作成する。
>
>- 既定の [App Service プラン](/cli/azure/appservice/plan#az-appservice-plan-create)を作成する。
>
>- 指定された名前で[アプリを作成](/cli/azure/webapp#az-webapp-create)する。
>
>- 現在の作業ディレクトリからアプリにファイルを [zip してデプロイ](../articles/app-service/deploy-zip.md)する。
>