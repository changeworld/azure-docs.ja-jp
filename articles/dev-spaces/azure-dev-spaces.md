---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 14b51cc2ad2e8e0f294e5e73e542001e30d21c9d
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918512"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Azure Dev Spaces は、高速で反復的な Kubernetes 開発エクスペリエンスをチームに提供します。 開発用コンピューターの最小限のセットアップで、Azure Kubernetes Service (AKS) 内での直接的なコンテナーの実行とデバッグを繰り返し実行できます。 Visual Studio、Visual Studio Code、コマンド ラインなどの使い慣れたツールを使用して、Windows、Mac、または Linux 上で開発します。

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev Spaces が省力化する Kubernetes 開発の実際 

Azure Dev Spaces は、次の方法で開発チームの Kubernetes での生産性を向上させることができます。
- 各チーム メンバーのローカル開発用コンピューターのセットアップを最小限に抑え、Azure のマネージド Kubernetes クラスターである AKS で直接作業する。
- Visual Studio 2017 または Visual Studio Code を使用して、Kubernetes でコードの迅速な反復処理とデバッグを直接実行する。
- 開発から運用に至るまで使用できる Docker と Kubernetes のコードとしての構成資産を生成する。 
- マネージド Kubernetes クラスターをチームで共有して、共同作業を行う。 依存関係を複製したりモックアップしたりすることなく、自分のコードを分離して開発したり、他のコンポーネントと併せてエンド ツー エンドのテストを実行したりする。

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>関連項目

[Azure Kubernetes Service](/azure/aks)