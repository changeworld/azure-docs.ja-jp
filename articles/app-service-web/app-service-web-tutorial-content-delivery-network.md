---
title: "Web アプリを Content Delivery Network に接続する | Microsoft Docs"
description: "Web アプリを Content Delivery Network に接続して、エッジ ノードから静的ファイルを配信します。"
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: d616ca49da7a909a1a104409c5845f39c4a5377a
ms.lasthandoff: 04/04/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Web アプリを Content Delivery Network に接続する

このチュートリアルでは、Azure CDN プロファイルと Azure CDN エンドポイントを作成して、Web アプリから Azure CDN の POP の場所を経由して静的ファイルを配信します。

> [!TIP]
> [Azure CDN の POP の場所](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations)の最新の一覧を確認してください。
>

## <a name="step-1---login-to-azure-portal"></a>手順 1 - Azure Portal にログインする

最初に、お使いのブラウザーを開き、Azure [Portal](https://portal.azure.com) にアクセスします。

## <a name="step-2---create-a-cdn-profile"></a>手順 2 - CDN プロファイルを作成する

左側のナビゲーションで、[`+ New`] ボタンをクリックし、**[Web + モバイル]** をクリックします。 [Web + モバイル] カテゴリで、**[CDN]** を選択します。

**[名前]**、**[場所]**、**[リソース グループ]**、**[価格レベル]** を指定し、**[作成]** をクリックします。

左側のナビゲーションからリソース グループ ハブを開き、**[myResourceGroup]** を選択します。 リソースの一覧から **[myCDNProfile]** を選択します。

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>手順 3 - CDN エンドポイントを作成する

検索ボックスの横にあるコマンドから [`+ Endpoint`] をクリックします。エンドポイント作成ブレードが開きます。

**[名前]**、**[配信元の種類]**、**[配信元のホスト名]** を指定し、**[追加]** をクリックします。

エンドポイントが作成されます。Content Delivery Network エンドポイントが作成されると、状態が **[実行中]** に更新されます。

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>手順 4 - CDN を活用する

エンドポイントが実行中になったので、Web サーバー上の静的ファイルを参照し、ホスト名を `http://<app_name>.azurewebsites.net/path/to-static-file` から `http://<endpoint_name>.azureedge.net/path/to-static-file` に変更して、POP サーバーでコンテンツを使用できることを確認してみましょう。

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>次のステップ


