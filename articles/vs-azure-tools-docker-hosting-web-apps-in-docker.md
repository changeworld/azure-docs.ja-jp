---
title: ASP.NET Docker コンテナーを Azure Container Registry (ACR) にデプロイする |Microsoft Docs
description: Visual Studio Tools for Docker を使用して、ASP.NET Core Web アプリをコンテナー レジストリにデプロイする方法を説明します
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 58df17b17de1d93683875b68dd7c6c087bc6d16d
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972310"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Visual Studio を使用して ASP.NET Docker コンテナーをコンテナー レジストリにデプロイする
## <a name="overview"></a>概要
Docker は軽量のコンテナー エンジンで、アプリケーションとサービスをホストするために使用できる仮想マシンにいくつかの点で似ています。
このチュートリアルでは、Visual Studio を使用して、コンテナー化されたアプリケーションを [Azure Container Registry](https://azure.microsoft.com/services/container-registry) に発行する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) を作成してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* "ASP.NET および Web 開発" ワークロードと共に、最新バージョンの [Visual Studio 2017](https://azure.microsoft.com/downloads/) をインストールする
* [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) をインストールする

## <a name="1-create-an-aspnet-core-web-app"></a>1.ASP.NET Core Web アプリケーションの作成
次の手順では、このチュートリアルで使用する基本的な ASP.NET Core アプリの作成について説明します。

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2.Azure Container Registry へのコンテナーの発行
1. **ソリューション エクスプローラー**で対象のプロジェクトを右クリックし、**[発行]** を選択します。
2. 発行先ダイアログで **[コンテナー レジストリ]** タブを選択します。
3. **[New Azure Container Registry]\(新しい Azure コンテナー レジストリ)** を選択し、**[発行]** をクリックします。
4. **[新しい Azure コンテナー レジストリを作成する]** で、目的の値を入力します。

    | Setting      | 推奨値  | 説明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS プレフィックス** | グローバルに一意の名前 | コンテナー レジストリを一意に識別する名前。 |
    | **サブスクリプション** | サブスクリプションの選択 | 使用する Azure サブスクリプション。 |
    | **[リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  コンテナー レジストリを作成するリソース グループの名前。 新しいリソース グループを作成する場合は、**[新規]** を選択します。|
    | **[SKU](https://docs.microsoft.com/azure/container-registry/container-registry-skus)** | 標準 | コンテナー レジストリのサービス層  |
    | **レジストリの場所** | 近くの場所 | [[地域]](https://azure.microsoft.com/regions/) で、自分に近いか、またはコンテナー レジストリを使用する他のサービスに近い場所を選択します。 |
    ![Visual Studio の Azure コンテナー レジストリを作成するダイアログ][0]
5. **[作成]**

これでレジストリからコンテナーを、[Azure Container Instances](./container-instances/container-instances-tutorial-deploy-app.md) などの Docker イメージを実行できるホストにプルできるようになりました。

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
