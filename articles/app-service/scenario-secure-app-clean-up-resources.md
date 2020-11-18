---
title: チュートリアル - リソースをクリーンアップする | Azure
description: このチュートリアルでは、Web アプリの作成時に割り当てられた Azure リソースをクリーンアップする方法について学習します。
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ab91ea7aa6e621dabc5cac83fe818dbf175214b6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428272"
---
# <a name="tutorial-clean-up-resources"></a>チュートリアル: リソースをクリーンアップする

この複数のパートで構成されるチュートリアルのすべての手順を完了している場合は、アプリ サービス、アプリ サービス ホスティング プラン、およびストレージ アカウントがリソース グループに作成されています。  また、Azure AD にアプリの登録も作成されています。  これらのリソースとアプリの登録が不要になったら、引き続き料金が発生することのないように、これらを削除します。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * チュートリアルに従って作成した Azure リソースを削除する

## <a name="delete-the-resource-group"></a>リソース グループを削除します
[Azure portal](https://portal.azure.com) で、ポータル メニューから **[リソース グループ]** を選択し、対象のアプリ サービスとアプリ サービス プランが含まれているリソース グループを選択します。

**[リソース グループの削除]** を選択して、リソース グループとすべてのリソースを削除します。

リソース グループの削除

このコマンドの実行には数分かかることがあります。

## <a name="delete-the-app-registration"></a>アプリの登録を削除する
ポータル メニューから、 **[Azure Active Directory]** 、 **[アプリの登録]** の順に選択し、作成したアプリケーションを選択します。
:::image type="content" alt-text="[アプリの登録] を選択する" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

アプリの登録の概要で、 **[削除]** を選択します。
:::image type="content" alt-text="アプリの登録を削除する" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> * チュートリアルに従って作成した Azure リソースを削除する

[.NET Core アプリ](tutorial-dotnetcore-sqldb-app.md)、[Python アプリ](tutorial-python-postgresql-app.md)、[Java アプリ](tutorial-java-spring-cosmosdb.md)、または [Node.js アプリ](tutorial-nodejs-mongodb-app.md)をデータベースに接続する方法について学習します。