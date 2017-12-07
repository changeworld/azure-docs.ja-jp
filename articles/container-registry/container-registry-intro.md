---
title: "Azure のプライベート Docker コンテナー レジストリ"
description: "クラウド ベースの管理されたプライベート Docker レジストリを提供する Azure Container Registry サービスの紹介です。"
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: overview
ms.date: 10/13/2017
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: e4bca1cc09d115cf0b1b13f7675645ef1ef5ec53
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure のプライベート Docker コンテナー レジストリの概要

Azure コンテナー レジストリは、オープンソースの Docker Registry 2.0 に基づいた、管理された [Docker レジストリ](https://docs.docker.com/registry/) サービスです。 プライベート [Docker コンテナー](https://www.docker.com/what-docker) イメージを保存および管理する Azure コンテナー レジストリを作成および管理します。 既存のコンテナーの開発とデプロイ パイプラインで Azure のコンテナー レジストリを使用し、Docker コミュニティの専門知識を活用します。

Docker とコンテナーに関する背景については、[Docker のユーザー ガイド](https://docs.docker.com/engine/userguide/)を参照してください。

## <a name="use-cases"></a>ユース ケース
Azure コンテナー レジストリからさまざまなデプロイ ターゲットにイメージをプルできます。

* [DC/OS](https://docs.mesosphere.com/)、[Docker Swarm](https://docs.docker.com/swarm/)、[Kubernetes](http://kubernetes.io/docs/) など、ホストのクラスターのコンテナー化されたアプリケーションを管理する**スケーラブルなオーケストレーション システム**。
* [Container Service](../container-service/index.yml)、[App Service](/app-service/index.md)、[Batch](../batch/index.md)、[Service Fabric](/azure/service-fabric/) など、アプリケーションの大規模な構築と実行をサポートする **Azure サービス**。

開発者は、コンテナー開発ワークフローの一環としてコンテナー レジストリにプッシュすることもできます。 たとえば、[Visual Studio Team Services](https://www.visualstudio.com/docs/overview) や [Jenkins](https://jenkins.io/) などの継続的な統合とデプロイ ツールからコンテナー レジストリを対象にすることができます。

## <a name="key-concepts"></a>主要な概念
* **レジストリ** - Azure サブスクリプションに 1 つ以上のコンテナー レジストリを作成できます。 レジストリには、[Basic、Standard、Premium](container-registry-skus.md) の 3 つの SKU があり、それぞれ webhook 統合、Azure Active Directory によるリポジトリ認証、削除機能がサポートされます。 デプロイと同じ Azure の場所にレジストリを作成することで、ネットワーク上の近い場所にローカルで保存されたコンテナー イメージを活用します。 高度なレプリケーションとコンテナー イメージの配布に対応するには、Premium レジストリの [geo レプリケーション](container-registry-geo-replication.md)機能を使用してください。 レジストリの完全修飾名は `myregistry.azurecr.io` という形式になります。

  コンテナー レジストリへの[アクセスを制御](container-registry-authentication.md)するには、Azure Active Directory でサポートされている[サービス プリンシパル](../active-directory/active-directory-application-objects.md)または提供された管理者アカウントを使用します。 レジストリによる認証を行うには、標準の `docker login` コマンドを実行します。

* **リポジトリ** - レジストリには、1 つ以上のリポジトリ (コンテナー イメージのグループ) が含まれています。 Azure Container Registry では、複数レベルのリポジトリ名前空間をサポートしています。 複数レベルの名前空間により、特定のアプリまたはアプリのコレクションに関連するイメージのコレクションを特定の開発チームや運用チーム別にグループ化できます。 For example:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` は、会社全体のイメージを表します
  * `myregistry.azurecr.io/warrantydept/dotnet-build` は、保証部門全体で共有される、.NET アプリを構築するために使用するイメージを表します
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` は、保証部門が所有する、customer submissions アプリでグループ化された Web イメージを表します

* **イメージ** - リポジトリに格納されます。各イメージは、Docker コンテナーの読み取り専用のスナップショットです。 Azure コンテナー レジストリには、Windows と Linux の両方のイメージを含めることができます。 すべてのコンテナーのデプロイのイメージ名を制御できます。 イメージをリポジトリにプッシュしたり、イメージをリポジトリからプルしたりするには、標準の [Docker コマンド](https://docs.docker.com/engine/reference/commandline/)を使用します。

* **コンテナー** - コンテナーでは、ソフトウェア アプリケーションと、コード、ランタイム、システム ツール、ライブラリを含む完全なファイル システム内にラップされた依存関係を定義します。 コンテナー レジストリからプルした Windows または Linux イメージに基づいて Docker コンテナーを実行します。 1 台のコンピューターで実行されているすべてのコンテナーでは、オペレーティング システムのカーネルを共有します。 Docker コンテナーは、主要なすべての Linux ディストリビューション、macOS、および Windows に完全に移植できます。

## <a name="next-steps"></a>次のステップ
* [Azure Portal を使用したコンテナー レジストリの作成](container-registry-get-started-portal.md)
* [Azure CLI を使用したコンテナー レジストリの作成](container-registry-get-started-azure-cli.md)
* [Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)
