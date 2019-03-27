---
title: チュートリアル - Azure Spatial Anchors と Azure Cosmos DB のバックエンドを使用したセッションやデバイス間での共有 | Microsoft Docs
description: このチュートリアルでは、バックエンド サービスと Azure Cosmos DB を使用して、Unity で Azure Spatial Anchors 識別子をデバイス間で共有する方法について説明します。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753494"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>チュートリアル:Azure Spatial Anchors と Azure Cosmos DB のバックエンドを使用したセッションやデバイス間での共有

このチュートリアルでは、[Azure Spatial Anchors](../overview.md) を使用して次のことを行う方法を説明します。

1. あるセッションでアンカーを作成した後、同じデバイスまたは異なるデバイス上で別のセッションにそれらを配置する。 たとえば、別の日に対して行う。
2. 複数のデバイスが同時に同じ場所に配置できるアンカーを作成する。

![永続化](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、2 つ以上のデバイスにデプロイ可能なアプリが完成します。 1 つのインスタンスによって作成された Azure 空間アンカーは、Cosmos DB を使用して他のインスタンスに識別子を共有します。

学習内容は次のとおりです。

> [!div class="checklist"]
> * アンカーを Cosmos DB に格納して共有するための ASP.NET Core Web アプリを Azure にデプロイする。
> * クイック スタートで提供されている Unity サンプルの AzureSpatialAnchorsLocalSharedDemo シーンを構成して、アンカー共有 Web アプリを利用する。
> * 1 つ以上のデバイスにデプロイして実行する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

このチュートリアルでは Unity と Azure Cosmos DB を使用しますが、これは Azure Spatial Anchors 識別子を複数デバイス間で共有する方法の一例に過ぎないことに注意してください。 同じ目的を実現するために、他の言語やバックエンド テクノロジを使用することもできます。 また、このチュートリアルで使用する ASP.NET Core Web アプリは .NET Core 2.2 SDK に依存しています。 通常の (Windows 向け) Azure Web Apps では正常に動作しますが、現在、Azure Web Apps for Linux では動作しません。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

`Connection String` は、後で使用するので書き留めておいてください。

## <a name="deploy-your-sharing-anchors-service"></a>アンカー共有サービスのデプロイ

Visual Studio を開き、`Sharing\SharingServiceSample` フォルダーのプロジェクトを開きます。

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Cosmos DB を使用するようにサービスを構成する

**ソリューション エクスプローラー**で `SharingService\Startup.cs` を開きます。

ファイルの先頭にある `#define INMEMORY_DEMO` 行を見つけ、それをコメントアウトします。ファイルを保存します。

**ソリューション エクスプローラー**で `SharingService\appsettings.json` を開きます。

`StorageConnectionString` プロパティを見つけて、その値を、[データベース アカウントの作成手順](#create-a-database-account)で書き留めておいた `Connection String` に設定します。 ファイルを保存します。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Cosmos DB を使用して、デバイス間でアンカーの識別子を共有しました。 Azure Spatial Anchors ライブラリの詳細については、アンカーの作成と配置方法に関するガイドを引き続き参照してください。

> [!div class="nextstepaction"]
> [Azure Spatial Anchors を使用したアンカーの作成と配置](../create-locate-anchors-overview.md)
