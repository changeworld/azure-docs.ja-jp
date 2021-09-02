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
ms.openlocfilehash: d26b8d131d5a8ff54b12fc5817718ddd381b720b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "122643262"
---
> [!NOTE]
> `az webapp up` コマンドは、次の処理を実行します。
>
>- 既定の[リソース グループ](/cli/azure/group#az_group_create)を作成する。
>
>- 既定の [App Service プラン](/cli/azure/appservice/plan#az_appservice_plan_create)を作成する。
>
>- 指定された名前で[アプリを作成](/cli/azure/webapp#az_webapp_create)する。
>
>- 現在の作業ディレクトリからアプリにファイルを [zip してデプロイ](../articles/app-service/deploy-zip.md)する。
>