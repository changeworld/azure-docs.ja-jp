---
title: CLI:TLS/SSL 証明書をアプリにアップロードしてバインドする
description: Azure CLI を使用して App Service アプリのデプロイと管理を自動化する方法について説明します。 このサンプルでは、カスタム TLS/SSL 証明書をアプリにバインドする方法を紹介しています。
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 50f5c0f00daf025791eb0982ea4c5f626ded02ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782497"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>CLI を使用してカスタム TLS/SSL 証明書を App Service アプリにバインドする

このサンプル スクリプトでは、App Service でアプリを関連リソースと合わせて作成し、カスタム ドメイン名の TLS/SSL 証明書をバインドします。 このサンプルでは、以下が必要です。

* ドメイン レジストラーの DNS 構成ページへのアクセス許可。
* アップロードしてバインドする TLS/SSL 証明書の有効な .PFX ファイルとパスワード。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service アプリを作成します。 |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname#az_webapp_config_hostname_add) | カスタム ドメインを App Service アプリにマップします。 |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl#az_webapp_config_ssl_upload) | TLS/SSL 証明書を App Service アプリにアップロードします。 |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl#az_webapp_config_ssl_bind) | アップロードした TLS/SSL 証明書を App Service アプリにバインドします。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
