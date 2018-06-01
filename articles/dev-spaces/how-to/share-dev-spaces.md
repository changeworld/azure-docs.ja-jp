---
title: Azure Dev Spaces の共有方法 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 140a487a0a605e0f03122868af0d2d12bcdba35b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197659"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces を共有する

Azure Dev Spaces を使用すると、チームの他のメンバーと開発スペースを共有できます。 それぞれの開発者は、他の開発者に影響を及ぼす心配なしに、独自のスペースで作業できます。 また、1 つのスペースで共同作業することで、モックを作成する必要なしにコードをエンドツーエンドでテストしたり、依存関係をシミュレートしたりできます。 詳細については、「[チーム開発について学ぶ](../get-started-nodejs.md#learn-about-team-development)」ガイドを参照してください。

複数の開発者向けに環境をセットアップするには:
1. Azure で Dev Space を作成します。 [[.NET Core and VS Code] (.NET Core と VS Code)](../get-started-netcore.md)、[[.NET Core and Visual Studio] (.NET Core と Visual Studio)](../get-started-netcore-visualstudio.md)、または [[Node.js and VS Code] (Node.js と VS Code)](../get-started-nodejs.md) を選択します。 選択した Azure サブスクリプションの所有者または共同作成者のアクセスが必要になります。
1. Azure Dev Space の**リソース グループ**を構成して、各チーム メンバーの[共同作成者アクセスを付与](/azure/active-directory/role-based-access-control-configure)します。 次のコマンドを実行して、環境のリソース グループを確認できます: `azds resource list`
1. その中で開発を行うために**環境を選択**するよう、チーム メンバーに依頼します。
     * **コマンド ラインまたは VS Code**: アクセスできる既存の Azure Dev Spaces を確認する: `azds resource list`。 環境を選択する: `azds resource select`。
     * **Visual Studio IDE**: プロジェクトを Visual Studio で開き、起動設定ドロップダウンから **[Azure Dev Spaces]** を選択します。 表示されるダイアログ ボックスで、既存の開発環境を選択します。

![Visual Studio の起動設定ドロップダウン](../media/get-started-netcore-visualstudio/LaunchSettings.png)