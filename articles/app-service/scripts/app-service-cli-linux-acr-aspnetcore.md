---
title: "Azure CLI のサンプル スクリプト - Azure コンテナー レジストリから Docker コンテナーに ASP.NET Core Web アプリケーションを作成する | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure コンテナー レジストリから Docker コンテナーに ASP.NET Core Web アプリケーションを作成する"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/23/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 862aad510a9d6e8ae15324457d8b15378b2b2776
ms.openlocfilehash: 61d0f71d13429cffa3b7e01cf2fb18eb358a1817
ms.lasthandoff: 02/27/2017

---

# <a name="create-an-aspnet-core-web-app-in-a-docker-container-from-azure-container-registry"></a>Azure コンテナー レジストリから Docker コンテナーに ASP.NET Core Web アプリケーションを作成する

このシナリオでは、リソース グループ、Linux App Service プラン、および Web アプリを作成し、Azure コンテナー レジストリから Docker コンテナーを使用して ASP.NET Core Web アプリケーションをデプロイします。

このスクリプトを実行する前に、`az login` コマンドを使用して Azure との接続が作成されていることを確認してください。

## <a name="create-app-sample"></a>サンプル アプリの作成

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh?highlight=6-9 "Linux の Azure コンテナー レジストリ")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Web アプリ、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 App Service プランとは、Azure Web アプリ用のサーバー ファームのようなものです。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | App Service プラン内に Azure Web アプリを作成します。 |
| [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) | Azure Web アプリ用の Docker コンテナーを設定します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../app-service-cli-samples.md)のページにあります。
