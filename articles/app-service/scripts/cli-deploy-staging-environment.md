---
title: CLI:ステージング スロットにデプロイする
description: Azure CLI を使用して App Service アプリのデプロイと管理を自動化する方法について説明します。 このサンプルでは、ステージング スロットにコードをデプロイする方法を紹介しています。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: bba2c3d6faf99962ce4e687832c4d26dcd3dc671
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787876"
---
# <a name="create-an-app-service-app-and-deploy-code-to-a-staging-environment-using-azure-cli"></a>Azure CLI を使用した App Service アプリの作成とステージング環境へのコードのデプロイ

このサンプル スクリプトでは、App Service 上でアプリを作成し、"staging" という名前のデプロイ スロットを追加した後、サンプル アプリを "staging" スロットにデプロイします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create an app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service アプリを作成します。 |
| [`az webapp deployment slot create`](/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_create) | デプロイ スロットを作成します。 |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | App Service アプリを Git または Mercurial のリポジトリに関連付けます。 |
| [`az webapp deployment slot swap`](/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_swap) | 指定したデプロイ スロットを運用環境にスワップします。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
