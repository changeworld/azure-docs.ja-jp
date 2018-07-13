---
title: '人工知能 (AI) アプリケーションの DevOps: Docker、Kubernetes、および Python Flask アプリケーションを使用した Azure での継続的インテグレーション パイプラインの作成'
description: '人工知能 (AI) アプリケーションの DevOps: Docker と Kubernetes を使用した Azure での継続的インテグレーション パイプラインの作成'
services: machine-learning, team-data-science-process
documentationcenter: ''
author: jainr
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jainr
ms.openlocfilehash: 4d95fc25ed6f2f2efec8313e5b208b3cccbb619f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968793"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>人工知能 (AI) アプリケーションの DevOps: Docker と Kubernetes を使用した Azure での継続的インテグレーション パイプラインの作成
多くの場合、AI アプリケーションには、データ サイエンティストが機械学習モデルを構築し、アプリ開発者がアプリケーションをビルドしてエンド ユーザーが使用できるように公開するという 2 つの作業の流れがあります。 この記事では、AI アプリケーション パイプラインの継続的インテグレーション (CI)/継続的デリバリー (CD) を実装する方法について説明します。 AI アプリケーションは、事前トレーニング済みの機械学習 (ML) モデルに埋め込まれたアプリケーション コードの組み合わせです。 この記事では、個人の Azure Blob Storage アカウントから事前トレーニング済みモデルを取得しますが、AWS S3 アカウントも使用できます。 この記事では、単純な Python Flask Web アプリケーションを使用します。

> [!NOTE]
> これは、CI/CD を実行できるさまざまな方法の 1 つです。 以下に示すツールとその他の前提条件とは別のツールと前提条件があります。 記事の用意ができたら、順次公開する予定です。
>
>

## <a name="github-repository-with-document-and-code"></a>GitHub リポジトリのドキュメントとコード
ソース コードを [GitHub](https://github.com/Azure/DevOps-For-AI-Apps) からダウンロードできます。 [詳細なチュートリアル](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)も利用できます。

## <a name="pre-requisites"></a>前提条件
後述する CI/CD パイプラインには、次の前提条件があります。
* [Visual Studio Team Services アカウント](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Kubernetes を実行する Azure Container Service (AKS)](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Azure Container Registy (ACR) アカウント](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Kubernetes クラスターに対してコマンドを実行するための Kubectl のインストール](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。 これは、ACS クラスターから構成をフェッチするために必要になります。 
* GitHub アカウントへのリポジトリのフォーク

## <a name="description-of-the-cicd-pipeline"></a>CI/CD パイプラインの説明
このパイプラインは、新しいコミットのたびに開始され、テスト スイートを実行し、テストに合格した最新のビルドを採用し、それを Docker コンテナーにパッケージ化します。 その後、Azure Container Service (ACS) を使用してコンテナーが配置され、イメージが Azure Container Registry (ACR) に安全に格納されます。 ACS では、コンテナー クラスターを管理するために Kubernetes が実行されますが、Docker Swarm または Mesos も選択できます。

アプリケーションは、Azure Storage アカウントから最新のモデルを安全にプルし、それをアプリケーションの一部としてパッケージ化します。 展開されるアプリケーションには、アプリ コードと ML モデルが 1 つのコンテナーとしてパッケージ化されます。 これにより、アプリ開発者とデータ サイエンティストが切り離され、運用アプリで常に最新のコードが最新の ML モデルで実行されることが保証されます。

このパイプラインのアーキテクチャを次に示します。 

![アーキテクチャ](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>CI/CD パイプラインの各ステップ
1. 開発者は、任意の IDE でアプリケーション コードに対する作業を行います。
2. 開発者は、任意のソース管理にコードをコミットします (VSTS には、さまざまなソース コントロールの適切なサポートがあります)。
3. これとは別に、データ サイエンティストは、モデルの開発に取り組みます。
4. 開発が終わったら、モデルをモデル リポジトリにパブリッシュします。ここでは、Blob Storage アカウントを使用しています。 これは、REST API を介して Azure ML Workbench のモデル管理サービスに簡単に置き換えることができます。
5. GitHub のコミットに基づいて、VSTS でビルドが開始されます。
6. VSTS ビルド パイプラインは、Blob コンテナーから最新のモデルをプルしてコンテナーを作成します。
7. VSTS は、イメージを Azure Container Registry のプライベート イメージ リポジトリにプッシュします。
8. 設定されたスケジュール (夜間) に従って、リリース パイプラインが開始されます。
9. 最新のイメージが ACR からプルされ、ACS の Kubernetes クラスターに配置されます。
10. アプリに対するユーザー要求は、DNS サーバーを経由します。
11. DNS サーバーは、要求をロード バランサーに渡し、応答をユーザーに返します。

## <a name="next-steps"></a>次の手順
* 各自のアプリケーション向けの独自の CI/CD パイプラインを実装するための詳細については、こちらの[チュートリアル]((https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md))を参照してください。

## <a name="references"></a>参照
* [Team Data Science Process (TDSP)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/vso/)
* [Azure Kubernetes Services (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)