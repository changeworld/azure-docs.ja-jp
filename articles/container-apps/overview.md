---
title: Azure Container Apps プレビューの概要
description: Azure Container Apps の一般的なシナリオと用途について説明します
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: overview
ms.date: 10/19/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7b7f0cfb02b5aa2b7b37efc306213915a855a7bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092610"
---
# <a name="azure-container-apps-preview-overview"></a>Azure Container Apps プレビューの概要

Azure Container Apps を使用すると、サーバーレス プラットフォームでマイクロサービスとコンテナー化されたアプリケーションを実行できます。 Azure Container Apps の一般的な用途には以下が含まれます。

- API エンドポイントのデプロイ
- バックグラウンド処理アプリケーションのホスティング
- イベント 駆動型処理の処理
- マイクロサービスの実行

Azure Container Apps 上に構築されたアプリケーションは、次の特性に基づいて動的にスケーリングできます。

- HTTP トラフィック
- イベント駆動型処理
- CPU またはメモリの負荷
- 任意の [KEDA でサポートされているスケーラー](https://keda.sh/docs/scalers/)

:::image type="content" source="media/overview/azure-container-apps-example-scenarios.png" alt-text="Azure Container Apps のシナリオ例。":::

Azure Container Apps を使用すると、任意のコンテナーにパッケージ化されたアプリケーション コードを実行できます。ランタイムまたはプログラミング モデルについての指定はありません。 Container Apps を使用すると、クラウド インフラストラクチャと複雑なコンテナー オーケストレーターの管理に関する懸念を残しつつも、コンテナーを実行するメリットが得られます。

Azure Container Apps を使用すると、次のことが行えます。

- [複数のコンテナー リビジョンを実行し](application-lifecycle-management.md)、コンテナー アプリのアプリケーション ライフサイクルを管理します。

- KEDA でサポートされているスケール トリガーに基づいてアプリを[自動スケーリング](scale-app.md)します。 ほとんどのアプリケーションは 0<sup>1</sup> にスケーリングできます。

- 他の Azure インフラストラクチャを管理することなく、[HTTPS イングレスを有効にします](ingress.md)。

- ブルーグリーン デプロイと A/B テスト シナリオのために、複数のバージョンのアプリケーションに[トラフィックを分割します](revisions.md)。

- DNS ベースのサービス検出が組み込まれた、セキュリティで保護された内部専用エンドポイントには、[内部イングレスとサービス検出を使用します](connect-apps.md)。

- [Dapr を使用してマイクロサービスを構築し](microservices.md)、豊富な API セットにアクセスします。

- [任意のレジストリ (パブリックまたはプライベート) からコンテナーを実行します](containers.md) (Docker Hub および Azure Container Registry (ACR) を含む)。

- アプリケーションを管理するには、[Azure CLI 拡張機能または ARM テンプレートを使用します](get-started.md)。

- アプリケーションで直接[シークレットを安全に管理します](secure-app.md)。

- Azure Log Analytics を使用して、[アプリケーション ログを調べる](monitor.md)。

<sup>1</sup> [CPU またはメモリ負荷でスケーリングする](scale-app.md)アプリケーションは、0 にスケーリングできない。

### <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [初めてのコンテナー アプリをデプロイする](get-started.md)
