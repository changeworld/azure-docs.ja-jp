---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198760"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Kubernetes での開発は、Azure Dev Spaces でスピードアップすることができます。 また、Azure Kubernetes コンテナーに対するデバッグなど、数多くの開発機能が Azure Dev Spaces で追加されるため、VS Code、Visual Studio、コマンド ラインなど、なじみのあるツールを使いながら、クラウドでコンテナーの反復開発を行うことができます。 Azure Dev Spaces は、チーム独自のスペース内で個々のコード ブランチを分離することが、開発ライフサイクルの生命線となるようなチーム開発で特に利便性を発揮します。

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev Spaces が省力化する Kubernetes 開発の実際 

この手法には、いくつかの利点があります。

* クラウド リソースへのフル アクセスを備えた運用環境を表す開発環境がインフラストラクチャなしで手に入る。
* VS Code または Visual Studio を使用し、Node.js と .NET Core のコンテナーを Kubernetes で直接デバッグする。 その他の言語はすべて、コマンド ライン インターフェイスで開発できます。
* Kubernetes インスタンスを開発チーム全体で共有することによって、コストを削減すると共に、新しいチーム メンバーに使用するローカル コンピューターのセットアップを最小限に抑える。
* 依存関係を複製したりモックアップしたりすることなく、自分のコードを分離して開発したり、他のコンポーネントと併せてエンド ツー エンドのテストを実行したりする。

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)