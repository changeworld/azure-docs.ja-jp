---
title: "Kubernetes 向け Azure Container Service の概要 | Microsoft Docs"
description: "Kubernetes 向けの Azure Container Service では、Azure でコンテナー ベースのアプリケーションを簡単にデプロイして管理できます。"
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, コンテナー, マイクロサービス, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: a8ac18464d0efcc0db96e1667f18f2f853208573
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Azure Container Service (AKS) の概要

Azure Container Service (AKS) を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。 これにより、既存のスキルを使用して、または増え続けている大規模な専門家コミュニティの知恵に頼って、Microsoft Azure にコンテナーベースのアプリケーションをデプロイおよび管理できます。

AKS を使用することで、Kubernetes と Docker イメージ形式を介してアプリケーションの移植性を維持しながら、エンタープライズ レベルの Azure の機能を活用することができます。

## <a name="using-azure-container-service-aks"></a>Azure Container Service (AKS) の使用
AKS は、今日お客様に人気のあるオープンソースのツールとテクノロジを使用することで、コンテナー ホスト環境を提供することを目的としています。 この目的のために、標準の Kubernetes API エンドポイントを公開します。 これらの標準のエンドポイントを使用することにより、Kubernetes クラスターと通信できる任意のソフトウェアを活用できます。 たとえば、[kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)、[helm](https://helm.sh/)、[draft](https://github.com/Azure/draft) を選択できます。

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Azure Container Service (AKS) を使用した Kubernetes クラスターの作成
AKS の使用を開始するには、[Azure CLI](./kubernetes-walkthrough.md) を使用するか、ポータル (Marketplace で **Azure Container Service** を検索) 経由で、AKS クラスターをデプロイします。 Azure Resource Manager テンプレートを細かく制御する必要がある上級ユーザーの場合は、オープン ソースの [acs-engine](https://github.com/Azure/acs-engine) プロジェクトを使用して、独自のカスタム Kubernetes クラスターを構築し、これを `az` CLI 経由でデプロイできます。

### <a name="using-kubernetes"></a>Kubernetes の使用
この Kubernetes により、コンテナー化されたアプリケーションのデプロイ、スケーリング、および管理が自動化されます。 Kubernetes には、次に示す豊富な機能セットが用意されています。
* 自動ビンパッキング
* 自己復旧
* 水平スケーリング
* サービス検出と負荷分散
* 自動化されたロールアウトとロールバック
* シークレットと構成管理
* ストレージ オーケストレーション
* バッチ実行

## <a name="videos"></a>ビデオ

Azure Container Service (AKS) (Azure Friday、2017 年 10 月):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Kubernetes でのアプリケーションの開発およびデプロイに使用するツール (Azure OpenDev、2017 年 6 月):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

AKS のデプロイと管理の詳細を AKS クイックスタートで確認してください。

> [!div class="nextstepaction"]
> [AKS チュートリアル](./kubernetes-walkthrough.md)