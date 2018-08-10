---
title: Azure のプライベート Docker コンテナー レジストリ
description: クラウド ベースの管理されたプライベート Docker レジストリを提供する Azure Container Registry サービスの紹介です。
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 05/08/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: 394297e87ef03541725aad0689f11bca17c05ed9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576302"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure のプライベート Docker コンテナー レジストリの概要

Azure コンテナー レジストリは、オープンソースの Docker Registry 2.0 に基づいた、管理された [Docker レジストリ](https://docs.docker.com/registry/) サービスです。 プライベート [Docker コンテナー](https://www.docker.com/what-docker) イメージを保存および管理する Azure コンテナー レジストリを作成および管理します。

Azure では､すでにあるコンテナ開発パイプラインやデプロイ パイプラインでコンテナー レジストリを利用することができます｡ Azure Container Registry Build (ACR Build) を使用することで Azure 内にコンテナー イメージをビルドできます｡ オンデマンドでビルドしたり､ソースコード コミットやベース イメージ アップデート ビルド トリガーを使用してビルドを完全自動化したりできます｡

Docker およびコンテナーに関する背景情報は､[Docker概要](https://docs.docker.com/engine/docker-overview/)を参照してください｡

## <a name="use-cases"></a>ユース ケース

Azure コンテナー レジストリからさまざまなデプロイ ターゲットにイメージをプルできます。

* [DC/OS](https://docs.mesosphere.com/)、[Docker Swarm](https://docs.docker.com/swarm/)、[Kubernetes](http://kubernetes.io/docs/) など、ホストのクラスターのコンテナー化されたアプリケーションを管理する**スケーラブルなオーケストレーション システム**。
* [Azure Kubernetes Service (AKS)](../aks/index.yml) や  [App Service](/app-service/index.md)､[Batch](../batch/index.yml)､[Service Fabric](/azure/service-fabric/) など､大規模なアプリケーション のビルドと実行をサポートしている **Azure サービス**｡

開発者は、コンテナー開発ワークフローの一環としてコンテナー レジストリにプッシュすることもできます。 たとえば、[Visual Studio Team Services](https://www.visualstudio.com/docs/overview) や [Jenkins](https://jenkins.io/) などの継続的な統合とデプロイ ツールからコンテナー レジストリを対象にすることができます。

[ACR Build](#azure-container-registry-build) ビルド タスクの設定では､ベース イメージが更新されたときに自動的にアプリケーションイメージのリビルドされるように指定できます｡ ACR Build を利用して､Git レポジトリにコードが コミットされたときのイメージ ビルドを自動化してください｡ *ACR Build は現在プレビュー段階です｡*

## <a name="key-concepts"></a>主要な概念

* **レジストリ** - Azure サブスクリプションに 1 つ以上のコンテナー レジストリを作成できます。 レジストリには、[Basic、Standard、Premium](container-registry-skus.md) の 3 つの SKU があり、それぞれ webhook 統合、Azure Active Directory によるレジストリ認証、削除機能がサポートされます。 デプロイと同じ Azure の場所にレジストリを作成することで、ネットワーク上の近い場所にローカルで保存されたコンテナー イメージを活用します。 高度なレプリケーションとコンテナー イメージの配布に対応するには、Premium レジストリの [geo レプリケーション](container-registry-geo-replication.md)機能を使用してください。 レジストリの完全修飾名は `myregistry.azurecr.io` という形式になります。

  コンテナー レジストリへの[アクセスを制御](container-registry-authentication.md)するには、Azure Active Directory でサポートされている[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)または提供された管理者アカウントを使用します。 レジストリによる認証を行うには、標準の `docker login` コマンドを実行します。

* **リポジトリ** - レジストリには、1 つ以上のリポジトリ (コンテナー イメージのグループ) が含まれています。 Azure Container Registry では、複数レベルのリポジトリ名前空間をサポートしています。 複数レベルの名前空間により、特定のアプリまたはアプリのコレクションに関連するイメージのコレクションを特定の開発チームや運用チーム別にグループ化できます。 例: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1` は、会社全体のイメージを表します
  * `myregistry.azurecr.io/warrantydept/dotnet-build` は、保証部門全体で共有される、.NET アプリを構築するために使用するイメージを表します
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` は、保証部門が所有する、customer submissions アプリでグループ化された Web イメージを表します

* **イメージ** - リポジトリに格納されます。各イメージは、Docker コンテナーの読み取り専用のスナップショットです。 Azure コンテナー レジストリには、Windows と Linux の両方のイメージを含めることができます。 すべてのコンテナーのデプロイのイメージ名を制御できます。 イメージをリポジトリにプッシュしたり、イメージをリポジトリからプルしたりするには、標準の [Docker コマンド](https://docs.docker.com/engine/reference/commandline/)を使用します。

* **コンテナー** - コンテナーでは、ソフトウェア アプリケーションと、コード、ランタイム、システム ツール、ライブラリを含む完全なファイル システム内にラップされた依存関係を定義します。 コンテナー レジストリからプルした Windows または Linux イメージに基づいて Docker コンテナーを実行します。 1 台のコンピューターで実行されているすべてのコンテナーでは、オペレーティング システムのカーネルを共有します。 Docker コンテナーは、主要なすべての Linux ディストリビューション、macOS、および Windows に完全に移植できます。

## <a name="azure-container-registry-build-preview"></a>Azure Container Registry Build (プレビュー)

[Azure Container Registry Build](container-registry-build-overview.md) (ACR Build) は､Azure 上で簡略化し効率的な Docker コンテナー イメージ ビルドを行えるようにする､Azure Container Registry の機能一式です｡ ACR Build を利用して､`docker build` オペレーションを Azure にオフロードすることで開発内部ループをクラウドにまで拡大することができます｡ ビルド タスクの設定では､コンテナーの OS およびフレームワーク パッチ適用 パイプラインを自動化し､ソース コントロールにコードがコミットされたときに自動的にイメージがビルドされるよう指定できます｡

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="next-steps"></a>次の手順

* [Azure Portal を使用したコンテナー レジストリの作成](container-registry-get-started-portal.md)
* [Azure CLI を使用したコンテナー レジストリの作成](container-registry-get-started-azure-cli.md)
* [ACR Build による OS および フレームワーク パッチ適用の自動化](container-registry-build-overview.md) (プレビュー)
