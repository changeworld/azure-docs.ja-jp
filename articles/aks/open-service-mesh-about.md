---
title: サービスメッシュを開く
description: Azure Kubernetes Service (AKS) の Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: d03aead2056ef3394c35f5e9679edd28e47e3fd0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066902"
---
# <a name="open-service-mesh-aks-add-on"></a>Open Service Mesh AKS アドオン

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) は、軽量で拡張可能なクラウド ネイティブ サービス メッシュであり、ユーザーは、高度に動的なマイクロサービス環境のために、すぐに使用できる監視機能を統一して管理し、セキュリティで保護することができます。

OSM では、Kubernetes 上でエンボイベースのコントロール プレーンを実行します。また OSM は [SMI](https://smi-spec.io/) API を使用して構成でき、アプリケーションの各インスタンスの横に、エンボイ プロキシをサイドカー コンテナーとして挿入することで機能します。 エンボイ プロキシでは、アクセス制御ポリシーに関する実行規則が含まれており、これらの規則を実行します。また、ルーティング構成を実装し、メトリックをキャプチャします。 コントロール プレーンでは、ポリシーとルーティング規則が最新であることを確認し、プロキシが正常であることを保証するために、プロキシを継続的に構成します。

OSM プロジェクトは Microsoft によって開始され、それ以降は [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/) によって寄贈および管理されてきました。 OSM オープンソースプロジェクトは、引き続きコミュニティの機能と機能に関するコラボレーションを行い、プロジェクトへの貢献を歓迎し、お勧めします。 これに関与する方法については、Microsoft の[貢献者ラダー](https://github.com/openservicemesh/osm/blob/main/CONTRIBUTOR_LADDER.md) ガイドを参照してください。

## <a name="capabilities-and-features"></a>機能と特徴

OSM には、Azure Kubernetes Service (AKS) クラスターにクラウド ネイティブ サービス メッシュを提供するために、次の一連の機能と特徴が備えられています。

- OSM は AKS サービスに統合され、AKS 機能アドオンの利便性を備えた完全にサポートおよび管理されたサービス メッシュ エクスペリエンスを提供する

- mTLS を有効にして、サービス間の通信をセキュリティで保護する

- エンボイ プロキシの自動サイドカー挿入を有効にして、アプリケーションをメッシュに簡単にオンボードする

- デプロイ時のトラフィック移行の簡単で透過的な構成

- サービスに対してきめ細かなアクセス制御ポリシーを定義して実行する機能

- デバッグおよび監視サービスのためのアプリケーション メトリックの監視と洞察

- プラグ可能なインターフェイスを使用した外部証明書管理サービスやソリューションとの統合

## <a name="scenarios"></a>シナリオ

OSM では、次のシナリオで AKS のデプロイを支援できます。

- クラスターにデプロイされたサービス エンドポイント間の暗号化された通信を提供する

- メッシュ内の HTTP/HTTPS と TCP トラフィックの両方に対するトラフィックの認可

- A/B またはカナリア デプロイ用の複数のサービス間の重み付けトラフィック制御の構成

- アプリケーション トラフィックからの KPI の収集と表示

## <a name="osm-service-quotas-and-limits-preview"></a>OSM サービスのクォータと制限 (プレビュー)

サービスのクォータと制限に関する OSM プレビューの制限については、AKS の[「クォータと地域の制限」ページ](./quotas-skus-regions.md)を参照してください。

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
