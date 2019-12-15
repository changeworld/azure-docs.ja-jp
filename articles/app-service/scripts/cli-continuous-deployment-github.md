---
title: CLI:GitHub からの継続的デプロイ
description: Azure CLI を使用して App Service アプリのデプロイと管理を自動化する方法について説明します。 このサンプルでは、GitHub から CI/CD を使用してアプリを作成する方法を紹介しています。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/02/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 829f1507bdf69522f1baf5af1c8d92455608334f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688481"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>CLI を使用して GitHub からの継続的なデプロイで App Service アプリを作成する

このサンプル スクリプトでは、App Service でアプリを関連リソースと合わせて作成し、GitHub リポジトリからの継続的デプロイを設定します。 継続的なデプロイを使用しない GitHub でのデプロイについては、[アプリの作成と GitHub からのコードのデプロイ](cli-deploy-github.md)に関するページを参照してください。 このサンプルでは、以下が必要です。

* 管理アクセス許可のある、アプリケーション コードを含む GitHub リポジトリ。 自動ビルドを取得するには、「[リポジトリを準備する](../deploy-continuous-deployment.md#prepare-your-repository)」の表に従ってリポジトリを構築します。
* GitHub アカウントの[個人用アクセス トークン (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service アプリを作成します。 |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | App Service アプリを Git または Mercurial のリポジトリに関連付けます。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
