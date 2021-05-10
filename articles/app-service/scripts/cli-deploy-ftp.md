---
title: CLI:FTP を使用してアプリ ファイルをデプロイする
description: Azure CLI を使用して App Service アプリのデプロイと管理を自動化する方法について説明します。 このサンプルでは、アプリを作成し、FTP を使用してファイルをデプロイする方法を示します。
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 875192b5da0c034f4ac92c74dd617ded79df7f45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788009"
---
# <a name="create-an-app-service-app-and-deploy-files-with-ftp-using-azure-cli"></a>Azure CLI を使用して App Service アプリを作成し、FTP を使用してファイルをデプロイする

このサンプル スクリプトでは、App Service でアプリを関連リソースと合わせて作成し、FTP を使用して静的 HTML ページをデプロイします。 FTP へのアップロードでは、スクリプトによって [cURL](https://en.wikipedia.org/wiki/CURL) が例として使用されます。 ファイルのアップロードには、どのような FTP ツールでも使用できます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create an app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明 

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service アプリを作成します。 |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) | 使用可能なアプリ デプロイ プロファイルの詳細を取得します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
