---
title: チュートリアル - Azure Spatial Anchors と Azure Cosmos DB のバックエンドを使用したセッションやデバイス間での共有 | Microsoft Docs
description: このチュートリアルでは、バックエンド サービスと Azure Cosmos DB を使用して、Unity で Azure Spatial Anchors 識別子を Android/iOS デバイス間で共有する方法について説明します。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0e7011b9778221869940b137a2b87239f2d8db9b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286399"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>チュートリアル:Azure Spatial Anchors と Azure Cosmos DB のバックエンドを使用したセッションやデバイス間での共有

このチュートリアルでは、[Azure Spatial Anchors](../overview.md) を使用して次の操作を行う方法について学習します。

- あるセッション中にアンカーを作成した後、同じデバイスまたは異なるデバイス上の別のセッション中にそれらを探知する。 たとえば、2 番目のセッションは、別の日であってもかまいません。
- 複数のデバイスが同時に同じ場所に探知できるアンカーを作成する。

![オブジェクトの永続性を示す GIF](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、2 つ以上のデバイスにデプロイ可能なアプリが完成します。 1 つのインスタンスによって作成された空間アンカーは、Azure Cosmos DB を使用してその識別子を他のインスタンスと共有します。

学習内容は次のとおりです。

> [!div class="checklist"]
> * アンカーを Azure Cosmos DB に格納して共有するための ASP.NET Core Web アプリを Azure にデプロイする。
> * Azure クイック スタートで提供されている Unity サンプルの AzureSpatialAnchorsLocalSharedDemo シーンを構成して、アンカー共有 Web アプリを利用する。
> * アプリを 1 つ以上のデバイスにデプロイして実行する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

このチュートリアルでは Unity と Azure Cosmos DB を使用しますが、これは Spatial Anchors 識別子を複数デバイス間で共有する方法の例を示すことのみを目的としています。 同じ目的を実現するために、他の言語やバックエンド テクノロジを使用することもできます。 また、このチュートリアルで使用する ASP.NET Core Web アプリは .NET Core 2.2 SDK を必要とします。 このアプリは、Windows 用 Web Apps では正常に動作しますが、Linux 用 Web Apps では現在実行できません。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

後で必要になるため、`Connection String` をコピーします。

## <a name="open-the-sample-project-in-unity"></a>Unity でサンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>アンカー共有サービスをデプロイする

Visual Studio を開き、`Sharing\SharingServiceSample` フォルダーのプロジェクトを開きます。

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Azure Cosmos DB データベースを使用するようにサービスを構成する

**ソリューション エクスプローラー**で、`SharingService\Startup.cs` を開きます。

ファイルの先頭にある `#define INMEMORY_DEMO` を見つけ、その行をコメントアウトします。ファイルを保存します。

**ソリューション エクスプローラー**で、`SharingService\appsettings.json` を開きます。

`StorageConnectionString` プロパティを見つけて、その値を、[データベース アカウントの作成手順](#create-a-database-account)でコピーした `Connection String` 値と同じになるように設定します。 ファイルを保存します。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Cosmos DB を使用して、デバイス間でアンカーの識別子を共有しました。 Azure Spatial Anchors ライブラリの詳細については、アンカーの作成と配置方法に関するガイドを引き続き参照してください。

> [!div class="nextstepaction"]
> [Azure Spatial Anchors を使用したアンカーの作成と配置](../create-locate-anchors-overview.md)
