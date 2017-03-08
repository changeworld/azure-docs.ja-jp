---
title: "Azure CLI のサンプル スクリプト - GitHub からの継続的なデプロイで Web アプリを作成する | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - GitHub からの継続的なデプロイで Web アプリを作成する"
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
ms.sourcegitcommit: 97ab792a0ad7420e97dbab378736b9815356e8a4
ms.openlocfilehash: 220c150205858dd9ae200d7636a086972aeed6e9
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>GitHub からの継続的なデプロイで Web アプリを作成する

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、GitHub リポジトリからの継続的デプロイを設定します。 継続的なデプロイを使用しない GitHub でのデプロイについては、「[Web アプリを作成して GitHub からコードをデプロイする](app-service-cli-deploy-github.md)」を参照してください。

このスクリプトを実行する前に、次を確認してください。

- Azure との接続が、`az login` コマンドを使用して作成されている。
- アプリケーション コードが、自分が所有するパブリックまたはプライベートの GitHub リポジトリ内にある。
- [GitHub アカウントでアクセス トークンを作成](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)している。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../../virtual-machines/virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="create-app-sample"></a>サンプル アプリの作成

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "GitHub からの継続的なデプロイでの Web アプリの作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、App Service アプリ、およびすべての関連リソースを削除できます。

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
