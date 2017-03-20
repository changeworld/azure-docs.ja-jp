---
title: "初めての Python Web アプリを Azure に&5; 分で作成する | Microsoft Docs"
description: "サンプル Python アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: dbacf368684bf23f63d997941ac68cf0bc1d9a40
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-python-web-app-in-azure-in-five-minutes"></a>初めての Python Web アプリを Azure に&5; 分で作成する
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

このクイック スタートを読むと、初めての Python Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md) に数分でデプロイできるようになります。

クイック スタートを始める前に、コンピューターに [Azure CLI がインストール](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)されていることを確認してください。

## <a name="create-a-python-web-app-in-azure"></a>Azure に Python Web アプリを作成する
2. `az login` を実行し、画面の指示に従って Azure にログインします。
   
    ```azurecli
    az login
    ```
   
3. [リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 ここには、Web アプリとその SQL Database バックエンドなど、まとめて管理するすべての Azure リソースを置くことができます。

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    `---location` に使用できる値を確認するには、Azure CLI コマンド `az appservice list-locations` を使用してください。

3. "Free" [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. `<app_name>` に一意の名前を指定して新しい Web アプリを作成します。

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. サンプル Python アプリを GitHub からデプロイします。

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-python-get-started.git" --branch master --manual-integration 
    ```

5. Azure でアプリがライブ実行されるのを確認するには、次のコマンドを実行します。

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

初めての Python Web アプリを Azure App Services でライブ実行することができました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

[Web アプリの CLI スクリプト](app-service-cli-samples.md) サンプルに目を通します。

