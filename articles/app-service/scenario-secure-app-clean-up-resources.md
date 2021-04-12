---
title: チュートリアル - リソースをクリーンアップする | Azure
description: このチュートリアルでは、Web アプリの作成時に割り当てられた Azure リソースをクリーンアップする方法について説明します。
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: cfb42b82943f03c3633ff69662ab841bf587ede4
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221859"
---
# <a name="tutorial-clean-up-resources"></a>チュートリアル:リソースをクリーンアップする

この複数のパートで構成されるチュートリアルのすべての手順を完了している場合、アプリ サービス、アプリ サービス ホスティング プラン、ストレージ アカウントがリソース グループに作成されています。 また、Azure Active Directory にアプリの登録も作成されています。 これらのリソースとアプリの登録が不要になったら、引き続き料金が発生することのないように、これらを削除します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * チュートリアルに従って、作成した Azure リソースを削除する。

## <a name="delete-the-resource-group"></a>リソース グループを削除します

[Azure portal](https://portal.azure.com) で、ポータル メニューから **[リソース グループ]** を選択し、対象のアプリ サービスとアプリ サービス プランが含まれているリソース グループを選択します。

**[リソース グループの削除]** を選択して、リソース グループとすべてのリソースを削除します。

:::image type="content" alt-text="リソース グループの削除を示すスクリーンショット。" source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

このコマンドの実行には数分かかることがあります。

## <a name="delete-the-app-registration"></a>アプリの登録を削除する

ポータル メニューから、 **[Azure Active Directory]**  >  **[アプリの登録]** の順に選択します。 次に、作成したアプリケーションを選択します。
:::image type="content" alt-text="アプリの登録の選択を示すスクリーンショット。" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

アプリの登録の概要で、 **[削除]** を選択します。
:::image type="content" alt-text="アプリの登録の削除を示すスクリーンショット。" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
>
> * チュートリアルに従って、作成した Azure リソースを削除する。

[.NET Core アプリ](tutorial-dotnetcore-sqldb-app.md)、[Python アプリ](tutorial-python-postgresql-app.md)、[Java アプリ](tutorial-java-spring-cosmosdb.md)、または [Node.js アプリ](tutorial-nodejs-mongodb-app.md)をデータベースに接続する方法について学習します。