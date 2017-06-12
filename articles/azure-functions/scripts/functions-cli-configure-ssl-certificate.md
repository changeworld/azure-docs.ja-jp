---
title: "Azure CLI のサンプル スクリプト - Function App へのカスタム SSL 証明書のバインド | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure における Function App へのカスタム SSL 証明書のバインド"
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 9b7f84f5ad01335a3ce72b6b2aa654968fdd9499
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>カスタム SSL 証明書を Function App にバインドする

このサンプル スクリプトは、App Service で関連リソースと共に Function App を作成し、そこにカスタム ドメイン名の SSL 証明書をバインドします。 このサンプルでは、次のものが必要になります。

* ドメイン レジストラーの DNS 構成ページへのアクセス許可。
* アップロードしてバインドする SSL 証明書の有効な .PFX ファイルとパスワード。

SSL 証明書をバインドするには、Function App を従量課金プランではなく、App Service プランで作成する必要があります。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "カスタム SSL 証明書を Web アプリにバインドする")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | SSL 証明書をバインドするのに必要な App Service プランを作成します。 |
| [az functionapp create]() | Function App を作成します。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | カスタム ドメインを Function App にマップします。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | SSL 証明書を Function App にアップロードします。 |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | アップロードした SSL 証明書を Function App にバインドします。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント]()のページにあります。

