---
title: "Azure CLI のサンプル スクリプト - Web サーバー ログによる Web アプリの監視 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Web サーバー ログによる Web アプリの監視"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: ef75aa1ea579d4a3804e641ef9c848ccde8b9d7a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="monitor-a-web-app-with-web-server-logs"></a>Web サーバー ログによる Web アプリの監視

このシナリオでは、リソース グループ、App Service プラン、Web アプリを作成し、Web アプリを構成して Web サーバー ログを有効にします。 次に、ログ ファイルをダウンロードして確認します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "ログの監視")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 App Service プランとは、Azure Web アプリ用のサーバー ファームのようなものです。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | App Service プラン内に Azure Web アプリを作成します。 |
| [az appservice web log config](https://docs.microsoft.com/cli/azure/appservice/web/log#config) | Azure の Web アプリを保存するログを構成します。 |
| [az appservice web log download](https://docs.microsoft.com/cli/azure/appservice/web/log#download) | Azure Web アプリのログをローカル コンピューターにダウンロードします。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../app-service-cli-samples.md)のページにあります。

