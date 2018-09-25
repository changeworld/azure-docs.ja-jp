---
title: Azure CLI のサンプル スクリプト - Web アプリのバックアップ | Microsoft Docs
description: Azure CLI のサンプル スクリプト - Web アプリのバックアップ
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 12/07/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 816cc1cbe13a43aaaf527fe74ab8a3eb5faab3a3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946189"
---
# <a name="back-up-a-web-app"></a>Web アプリをバックアップする

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、これに対する 1 回限りのバックアップを作成します。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-onetime/backup-onetime.sh?highlight=3-7 "Back up a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | ストレージ アカウントを作成します。 |
| [`az storage container create`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) | Azure ストレージ コンテナーを作成します。 |
| [`az storage container generate-sas`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-generate-sas) | Azure ストレージ コンテナーの SAS トークンを生成します。  |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Azure Web アプリを作成します。 |
| [`az webapp config backup create`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-create) | Web アプリのバックアップを作成します。 |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Web アプリのバックアップの一覧を取得します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../app-service-cli-samples.md)のページにあります。
