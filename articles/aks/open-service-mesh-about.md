---
title: Open Service Mesh (プレビュー)
description: Azure Kubernetes Service (AKS) の Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: cd1cfde1b3c1dbeda7fb112b29e8579f32bdf05a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440158"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS アドオン (プレビュー)

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) は、軽量で拡張可能なクラウド ネイティブ サービス メッシュであり、ユーザーは、高度に動的なマイクロサービス環境のために、すぐに使用できる監視機能を統一して管理し、セキュリティで保護することができます。 

OSM では、Kubernetes 上でエンボイベースのコントロール プレーンを実行します。また OSM は [SMI](https://smi-spec.io/) API を使用して構成でき、アプリケーションの各インスタンスの横に、エンボイ プロキシをサイドカー コンテナーとして挿入することで機能します。 エンボイ プロキシでは、アクセス制御ポリシーに関する実行規則が含まれており、これらの規則を実行します。また、ルーティング構成を実装し、メトリックをキャプチャします。 コントロール プレーンでは、ポリシーとルーティング規則が最新であることを確認し、プロキシが正常であることを保証するために、プロキシを継続的に構成します。

OSM プロジェクトは Microsoft によって開始され、それ以降は [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/) によって寄贈および管理されてきました。 OSM オープンソース プロジェクトは引き続き、機能や特徴に関するコラボレーションによって主導されるコミュニティであり続けるため、このプロジェクトへの貢献が歓迎および奨励されます。 これに関与する方法については、Microsoft の[貢献者ラダー](https://github.com/openservicemesh/osm/blob/main/CONTRIBUTOR_LADDER.md) ガイドを参照してください。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

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
