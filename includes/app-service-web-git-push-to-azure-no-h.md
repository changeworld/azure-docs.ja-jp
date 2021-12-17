---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d79d02054e21ba359637194d00fee9ac01a6d56
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078278"
---
1. `main` ブランチをデプロイするため、App Service アプリの既定のデプロイ ブランチを `main` に設定する必要があります (「[デプロイ ブランチを変更する](../articles/app-service/deploy-local-git.md#change-deployment-branch)」を参照)。 Cloud Shell で、[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) コマンドを使用して `DEPLOYMENT_BRANCH` アプリ設定を設定します。 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DEPLOYMENT_BRANCH='main'
    ```

1. ローカル ターミナル ウィンドウで、ローカル Git リポジトリに Azure リモートを追加します。 *\<deploymentLocalGitUrl-from-create-step>* を、「[Web アプリを作成する](#create-a-web-app)」で保存した Git リモートの URL で置き換えます。

    ```bash
    git remote add azure <deploymentLocalGitUrl-from-create-step>
    ```

1. アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、Azure portal へのサインインに使用する資格情報ではなく、「**デプロイ ユーザーを構成する**」で作成した資格情報を入力してください。

    ```bash
    git push azure main
    ```

    このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。
