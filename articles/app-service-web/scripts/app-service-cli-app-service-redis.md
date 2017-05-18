---
title: "Azure CLI のサンプル スクリプト - Redis Cache への Web アプリの接続 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Redis Cache への Web アプリの接続"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f94cfaf5a2c8585dadf9fec74feb6d8875a1b3e4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="connect-a-web-app-to-a-redis-cache"></a>Web アプリを Redis Cache に接続する

このシナリオでは、Azure Redis Cache と Azure Web アプリの作成方法について説明します。 作成後、アプリの設定で Redis Cache を Web アプリにリンクします。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、Redis Cache、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 App Service プランとは、Azure Web アプリ用のサーバー ファームのようなものです。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | App Service プラン内に Azure Web アプリを作成します。 |
| [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) | 新しい Redis Cache インスタンスを作成します。 これは、データを格納する場所になります。 |
| [az redis list-keys](https://docs.microsoft.com/en-us/cli/azure/redis#list-keys) | Redis Cache インスタンスのアクセス キーを一覧表示します。 |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Azure Web アプリのアプリケーション設定を作成または更新します。 アプリケーション設定は、アプリの環境変数として公開されます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../app-service-cli-samples.md)のページにあります。

