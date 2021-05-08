---
title: チュートリアル:セッションやデバイス間でアンカーを共有する
description: このチュートリアルでは、バックエンド サービスを使用して、Unity で Azure Spatial Anchors 識別子を Android/iOS デバイス間で共有する方法について説明します。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3ed9b18144bb727af751fbcbbdd5cc6631afc40f
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105727231"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>チュートリアル:セッションやデバイス間で空間アンカーを共有する

Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 

このチュートリアルでは、[Azure Spatial Anchors](../overview.md) を使用してあるセッション中にアンカーを作成した後、同じデバイスまたは異なるデバイス上でそれらを探知します。 同じアンカーを、同じ場所にある複数のデバイスで同時に探知することもできます。

![モバイル デバイスで作成され、数日にわたって別のデバイスで使用される空間アンカーを示すアニメーション。](./media/persistence.gif)


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アンカーを共有するために使用できる ASP.NET Core Web アプリを Azure にデプロイし、指定した期間、メモリ内にアンカーを格納する。
> * クイック スタートで提供されている Unity サンプルの AzureSpatialAnchorsLocalSharedDemo シーンを構成して、アンカー共有 Web アプリを利用する。
> * 1 つまたは複数のデバイスにアンカーをデプロイして実行する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> このチュートリアルでは、Unity と ASP.NET Core Web アプリを使用しますが、ここでのアプローチは Azure Spatial Anchors 識別子を他の複数デバイス間で共有する方法の一例を示しているに過ぎません。 同じ目的を実現するために、他の言語やバックエンド テクノロジを使用することもできます。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>サンプル プロジェクトのダウンロード

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>アンカー共有サービスをデプロイする

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Visual Studio を開いてから、*Sharing\SharingServiceSample* フォルダー内のプロジェクトを開きます。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Visual Studio Code でサービスをデプロイする前に、リソース グループと App Service プランを作成する必要があります。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

<a href="https://portal.azure.com/" target="_blank">Azure portal</a> に移動して、Azure サブスクリプションにサインインします。

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

**[リソース グループ]** の横にある **[新規]** をクリックします。

リソース グループに **myResourceGroup** という名前を付けて、 **[OK]** をクリックします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

**[ホスティング プラン]** の隣にある **[新規]** を選択します。

**[ホスティング プランの構成]** ウィンドウで、以下の設定を使用します。

| 設定 | 推奨値 | 説明 |
|-|-|-|
|App Service プラン| MySharingServicePlan | App Service プランの名前 |
| 場所 | 米国西部 | Web アプリがホストされているデータセンター |
| サイズ | Free | ホスティング機能を決める[価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |

**[OK]** を選択します。

Visual Studio Code を開いてから、*Sharing\SharingServiceSample* フォルダーでプロジェクトを開きます。 

Visual Studio Code を介して共有サービスをデプロイするには、「<a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2&preserve-view=true#open-it-with-visual-studio-code" target="_blank">Visual Studio Code で ASP.NET Core アプリを Azure に公開する</a>」の手順に従ってください。 "Visual Studio Code で開く" セクションから開始します。 デプロイおよび発行するプロジェクトは既にあるので、前の手順の説明に従って別の ASP.NET プロジェクトを作成することは行わないでください。SharingServiceSample。

---

## <a name="deploy-the-sample-app"></a>サンプル アプリのデプロイ

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ASP.NET Core Web アプリを Azure にデプロイし、Unity アプリを構成してデプロイしました。 そのアプリで空間アンカーを作成し、ASP.NET Core Web アプリを使用して他のデバイスと共有しました。

Azure Cosmos DB を使用して共有済みの空間アンカー識別子のストレージを永続化するように、ASP.NET Core Web アプリを改良できます。 Azure Cosmos DB サポートを追加すれば、ASP.NET Core Web アプリでアンカーを今日にも作成できるようになります。 次に、Web アプリに格納されているアンカー識別子を使用して、数日後再びアンカーを検索するようにアプリを設定できます。

> [!div class="nextstepaction"]
> [Azure Cosmos DB を使用したアンカーの格納](./tutorial-use-cosmos-db-to-store-anchors.md)