---
title: Azure コンテナー イメージの技術資産を作成する | Azure Marketplace
description: Azure コンテナー用の技術資産を作成します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 68db606c9a01c4b1122f9b0cce620762485ca40a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148277"
---
# <a name="prepare-your-container-technical-assets"></a>コンテナーの技術資産を準備する

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure Container オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 [Azure Container の技術資産の準備](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)に関するページの手順に従って、移行されたオファーを管理します。

この記事では、Azure Marketplace 用のコンテナー オファーを構成するための手順と要件について説明します。

## <a name="before-you-begin"></a>開始する前に

クイック スタート、チュートリアル、サンプルが提供されている「[Azure Container Instances のドキュメント](https://docs.microsoft.com/azure/container-instances)」を確認してください。

## <a name="fundamental-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です｡
 
エンジニアリング チームには､ソリューションのドメインばかりでなく､Microsoft の次の技術に関する知識も必要です｡

-    [Azure Services](https://azure.microsoft.com/services/) に関する基本知識 
-    [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
-    [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/) と [Azure ストレージ](https://azure.microsoft.com/services/?filter=storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking)に関する実用的な知識
-    [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識
-    [JSON](https://www.json.org/) に関する実用的な知識

## <a name="suggested-tools"></a>推奨ツール

コンテナー イメージを管理するためのスクリプト環境として、次のいずれか一方または両方を選択します。

-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

また､開発環境には次にツールを加えることを推奨します｡

-    [Azure 記憶域エクスプローラー](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    拡張機能: [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

また､[Azure Developer Tools](https://azure.microsoft.com/tools/) ページの記載されている利用可能なツールもご覧になることをお勧めします｡Visual Studio を使用する場合は､[Visual Studio Marketplace](https://marketplace.visualstudio.com/) もご覧ください｡

## <a name="create-the-container-image"></a>コンテナー イメージを作成する

詳細については、以下を参照してください。

* [チュートリアル:Azure Container Instances へのデプロイに使用するコンテナー イメージを作成する](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [チュートリアル:Azure Container Registry タスクを使用して、クラウドでコンテナー イメージをビルドしてデプロイする](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>次のステップ

[コンテナー オファーを作成する](./cpp-create-offer.md)
