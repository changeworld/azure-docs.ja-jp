---
title: "Azure CLI のサンプル スクリプト - GitHub からのデプロイで Web アプリを作成する | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - GitHub からのデプロイで Web アプリを作成する"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: ab4be9d9a03001789bed90e58d62fe1891608823
ms.lasthandoff: 03/11/2017

---


# <a name="create-a-web-app-with-deployment-from-github"></a>GitHub からのデプロイで Web アプリを作成する

このサンプル スクリプトでは、Azure CLI 2.0 を使用して次の操作を行います。

* 西ヨーロッパ Azure リージョンの Azure App Service で Web アプリを作成する。
* GitHub から Web アプリ コードをデプロイする。
* デプロイした Azure Web アプリをブラウザーで表示する。

## <a name="prerequisites"></a>前提条件

* `az login` を実行して Azure にログインします。
* GitHub リポジトリに Web アプリ コードを配置します。

> [!NOTE]
> 自分のものではないパブリックの GitHub リポジトリを使用する場合、App Service ではその GitHub リポジトリからコードがデプロイされますが、継続的なデプロイに必要な SSH キーと webhook を設定することはできません。
>
>

## <a name="create-app-sample"></a>サンプル アプリの作成

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "GitHub からのデプロイで Web アプリを作成する")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure Web アプリを作成します。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Git または Mercurial リポジトリと Azure Web アプリを関連付けます。 |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | ブラウザーで Azure Web アプリを開きます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../app-service-cli-samples.md)のページにあります。
