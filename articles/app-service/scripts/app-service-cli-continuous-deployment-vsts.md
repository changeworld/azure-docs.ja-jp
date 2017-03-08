---
title: "Azure CLI のサンプル スクリプト - Visual Studio Team Services からの継続的なデプロイで Web アプリを作成する | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Visual Studio Team Services からの継続的なデプロイで Web アプリを作成する"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: d54e58da5755009710bdcd11eaf845d6ce4f76d4
ms.openlocfilehash: 915227599645186ed11165affd378f2de282c7ea
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Visual Studio Team Services からの継続的なデプロイで Web アプリを作成する

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、Visual Studio Team Services (VSTS) リポジトリからの継続的デプロイを設定します。 

このスクリプトを実行する前に、次を確認してください。

- Azure との接続が、`az login` コマンドを使用して作成されている。
- アプリケーション コードが、自分が所有するパブリックまたはプライベートの Visual Studio Team Services リポジトリ内にある。
- [VSTS でアクセス トークンを作成](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)している。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../../virtual-machines/virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="create-app-sample"></a>サンプル アプリの作成

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Visual Studio Team Services からの継続的なデプロイでの Web アプリの作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

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
