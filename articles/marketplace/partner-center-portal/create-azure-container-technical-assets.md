---
title: Azure VM の技術資産を作成する
description: この記事では、Azure Marketplace 用のコンテナー プランを構成するための手順と要件について説明します。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730641"
---
# <a name="create-an-azure-vm-technical-asset"></a>Azure VM の技術資産を作成する

> [!IMPORTANT]
> Azure Container プランの管理を Cloud パートナー ポータルからパートナー センターに移行しています。 プランが移行されるまでは、Cloud パートナー ポータル向けの「[コンテナーの技術資産を準備する](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets)」の手順に従ってプランを管理してください。

この記事では、Azure Marketplace 用のコンテナー プランを構成するための手順と要件について説明します。

## <a name="before-you-begin"></a>開始する前に

クイックスタート、チュートリアル、サンプルについては、[Azure Container Instances](https://docs.microsoft.com/azure/container-instances) に関する記事をご覧ください。

## <a name="fundamental-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です。

エンジニアリング チームには､ソリューションのドメインだけでなく､Microsoft の次の技術に関する知識も必要です。

- [Azure Services](https://azure.microsoft.com/services/) に関する基本知識
- [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
- [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/)、[Azure ストレージ](https://azure.microsoft.com/services/?filter=storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking)に関する実用的な知識
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識
- [JSON](https://www.json.org/) に関する実用的な知識。

## <a name="suggested-tools"></a>推奨ツール

コンテナー イメージを管理するためのスクリプト環境として、次のいずれか一方または両方を選択します。

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

開発環境には次にツールを加えることを推奨します｡

- [Azure 記憶域エクスプローラー](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 拡張機能: [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

使用できるツールについては、[Azure 開発者ツール](https://azure.microsoft.com/)に関するページをご覧ください。 Visual Studio を使用している場合は、[Visual Studio Marketplace](https://marketplace.visualstudio.com/) で使用できるツールを確認してください。

## <a name="create-the-container-image"></a>コンテナー イメージを作成する

詳細については、次のチュートリアルを参照してください。

- [チュートリアル:Azure Container Instances へのデプロイに使用するコンテナー イメージを作成する](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [チュートリアル:Azure Container Registry タスクを使用して、クラウドでコンテナー イメージをビルドしてデプロイする](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)。

## <a name="next-step"></a>次のステップ

- [コンテナー オファーを作成する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)。
