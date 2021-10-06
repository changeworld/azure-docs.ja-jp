---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/14/2021
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: afaacb79906c07893d835d654b00815ea97199d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577330"
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
>- [ビルド自動化を有効にして](../articles/app-service/deploy-zip.md#enable-build-automation-for-zip-deploy)、現在の作業ディレクトリからすべてのファイルを [ZIP デプロイ](../articles/app-service/deploy-zip.md#deploy-a-zip-package)します。
>
>- 後で `az webapp up` または他の Azure CLI コマンドを使用してデプロイするときに再指定する必要がないように、パラメーターを *.azure/config* ファイルにローカルにキャッシュします。 キャッシュされた値は、既定で自動的に使用されます。
>