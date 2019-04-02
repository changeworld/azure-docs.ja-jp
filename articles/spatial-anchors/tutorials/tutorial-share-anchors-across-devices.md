---
title: チュートリアル - Azure Spatial Anchors を使用したセッションやデバイス間での共有 | Microsoft Docs
description: このチュートリアルでは、バックエンド サービスを使用して、Unity で Azure Spatial Anchors 識別子を Android/iOS デバイス間で共有する方法について説明します。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7d9fe58b7db60513eed81aae628ebd7ca754a53a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901306"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>チュートリアル:Azure Spatial Anchors を使用したセッションやデバイス間での共有

このチュートリアルでは、[Azure Spatial Anchors](../overview.md) を使用して次のことを行う方法を説明します。

1. あるセッションでアンカーを作成した後、同じデバイスまたは異なるデバイス上で別のセッションにそれらを配置する。 たとえば、別の日に対して行う。
2. 複数のデバイスが同時に同じ場所に配置できるアンカーを作成する。

![永続化](./media/persistence.gif)

Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、2 つ以上のデバイスにデプロイ可能なアプリが完成します。 1 つのインスタンスによって作成された Azure 空間アンカーは、他のインスタンスに共有できます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * アンカーを一定期間メモリに格納して共有するための ASP.NET Core Web アプリを Azure にデプロイする。
> * クイック スタートで提供されている Unity サンプルの AzureSpatialAnchorsLocalSharedDemo シーンを構成して、アンカー共有 Web アプリを利用する。
> * 1 つ以上のデバイスにデプロイして実行する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

このチュートリアルでは Unity と ASP.NET Core Web App を使用しますが、これは Azure Spatial Anchors 識別子を複数デバイス間で共有する方法の一例に過ぎないことに注意してください。 同じ目的を実現するために、他の言語やバックエンド テクノロジを使用することもできます。 また、このチュートリアルで使用する ASP.NET Core Web アプリは .NET Core 2.2 SDK に依存しています。 通常の (Windows 向け) Azure Web Apps では正常に動作しますが、現在、Azure Web Apps for Linux では動作しません。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Unity でサンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>アンカー共有サービスのデプロイ

Visual Studio を開き、`Sharing\SharingServiceSample` フォルダーのプロジェクトを開きます。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="open-the-sample-project-in-unity"></a>Unity でサンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ASP.NET Core Web アプリを Azure にデプロイした後、Unity アプリを構成してデプロイしました。 そのアプリで空間アンカーを作成し、ASP.NET Core Web アプリを使用して他のデバイスと共有しました。

共有された空間アンカーを Azure Cosmos DB を使用して格納するように ASP.NET Core Web アプリを改良する方法の詳細を確認するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Cosmos DB を使用したアンカーの格納](./tutorial-use-cosmos-db-to-store-anchors.md)
