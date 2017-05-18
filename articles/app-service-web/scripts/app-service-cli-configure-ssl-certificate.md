---
title: "Azure CLI のサンプル スクリプト - Web アプリへのカスタム SSL 証明書のバインド | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Web アプリへのカスタム SSL 証明書のバインド"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 27da647a10a52af40333a962c75c73cab93861d7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>カスタム SSL 証明書を Web アプリにバインドする

このサンプル スクリプトは、関連するリソースを使用して App Service に Web アプリを作成し、そこにカスタム ドメイン名の SSL 証明書をバインドします。 このサンプルでは、次のものが必要になります。

* ドメイン レジストラーの DNS 構成ページへのアクセス許可。
* アップロードしてバインドする SSL 証明書の有効な .PFX ファイルとパスワード。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "カスタム SSL 証明書を Web アプリにバインドする")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure Web アプリを作成します。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | カスタム ドメインを Web アプリにマップします。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Web アプリに SSL 証明書をアップロードします。 |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Web アプリにアップロードした SSL 証明書をバインドします。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../app-service-cli-samples.md)のページにあります。

