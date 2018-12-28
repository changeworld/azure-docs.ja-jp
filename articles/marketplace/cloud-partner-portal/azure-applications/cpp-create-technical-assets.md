---
title: Azure アプリケーションの技術アセットを作成する | Microsoft Docs
description: Azure アプリケーション オファー用の技術アセットを作成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6050ad98c87dbe38516a6ee3c4862495ad868031
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414352"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Azure アプリケーションの技術アセットを準備する

この記事では、Azure アプリケーションの技術アセットを準備するためのリソースについて説明します。

## <a name="before-you-begin"></a>開始する前に

クイック スタート、チュートリアル、サンプルが提供されている以下の Azure アプリケーションのドキュメントを確認してください。

- [Azure Resource Manager テンプレートについての理解](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- クイック スタート:

  - [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure クイックスタート テンプレート](https://github.com/azure/azure-quickstart-templates)
  - [アプリケーション定義の発行](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [サービス カタログ アプリのデプロイ](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- チュートリアル:

  - [定義ファイルの作成](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [マーケットプレース アプリケーションの発行](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

 - サンプル:

   - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
   - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
   - [マネージド アプリケーション ソリューション](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です｡

エンジニアリング チームには､Microsoft の次の技術に関する知識も必要です｡

- [Azure Services](https://azure.microsoft.com/services/) に関する基本知識
- [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
- [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/)、[Azure ストレージ](https://azure.microsoft.com/services/?filter=storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking)に関する実用的な知識
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識
- [JSON](https://www.json.org/) に関する実用的な知識

## <a name="suggested-tools"></a>推奨ツール

Azure アプリケーションを管理するためのスクリプト環境として、次のいずれか一方または両方を選択します。

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

開発環境には次にツールを加えることを推奨します｡

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能:

  - 拡張機能: [Azure リソース マネージャー ツール[
  - 拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

また､[Azure Developer Tools](https://azure.microsoft.com/tools/) ページの記載されている利用可能なツールもご覧になることをお勧めします｡Visual Studio を使用する場合は､[Visual Studio Marketplace](https://marketplace.visualstudio.com/) もご覧ください｡

## <a name="next-steps"></a>次の手順

[Azure アプリケーション プランの作成](./cpp-create-offer.md)

