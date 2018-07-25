---
title: Azure CLI のサンプル スクリプト - Function App へのカスタム ドメインのマッピング | Microsoft Docs
description: Azure CLI のサンプル スクリプト - Azure における Function App へのカスタム ドメインのマッピング。
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 0650fffeb54ebc4390c82fb2711d7c89e0ac4572
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989420"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Function App への カスタム ドメインのマッピング

このサンプル スクリプトは、App Service プランに関数アプリを作成し、指定したカスタム ドメインにそれをマッピングします。 関数アプリが [App Service プラン](../functions-scale.md#app-service-plan)でホストされている場合、CNAME レコードまたは A レコードを使用してカスタム ドメインをマップできます。 [従量課金プラン](../functions-scale.md#consumption-plan)の関数アプリの場合、CNAME オプションのみがサポートされます。 このサンプルは、App Service プランを作成します。そのドメインにマッピングするための A レコードが必要となります。 

このサンプル スクリプトを実行するには、Web アプリの既定のドメイン名を指し示す A レコードがカスタム ドメインに構成済みであることが必要です。 詳細については、[Azure App Service にカスタム ドメインをマップする方法](https://aka.ms/appservicecustomdns)に関するページを参照してください。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降のバージョンを使用する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 


## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用します。表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Function App に必要なストレージ アカウントを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | カスタム ドメインをマップするのに必要な App Service プランを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | App Service プランで関数アプリを作成します。 |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | カスタム ドメインを Function App にマップします。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
