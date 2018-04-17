---
title: Azure Content Delivery Network (CDN) 製品の機能 | Microsoft Docs
description: 各 Azure Content Delivery Network (CDN) 製品がサポートする機能について説明します。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/23/2018
ms.author: rli
ms.custom: mvc
ms.openlocfilehash: 40638e2180b63c90fbcbe15cc2c1e944a97e166e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
# <a name="azure-cdn-product-features"></a>Azure CDN 製品の機能

Azure Content Delivery Network (CDN) 製品には、**Azure CDN Standard from Akamai**、**Azure CDN Standard from Verizon**、**Azure CDN Premium from Verizon** の 3 つがあります。 次の表では、各製品で使用できる機能を比較しています。

| **パフォーマンス機能と最適化** | Standard Akamai | Standard Verizon | Premium Verizon |
| --- | --- | --- | --- |
| [動的サイト アクセラレーション](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動的サイトの高速化 - アダプティブ画像圧縮](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only) | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動的サイト アクセラレーション - オブジェクトのプリフェッチ](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only) | **&#x2713;**  |  |  |
| [ビデオ ストリーミングの最適化](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization) | **&#x2713;**  | \* |  \* |
| [大きなファイルの最適化](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization) | **&#x2713;**  | \* |  \* |
| [グローバル サーバー負荷分散 (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [高速消去](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [資産の事前読み込み](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| キャッシュ/ヘッダーの設定 ( [キャッシュ規則](cdn-caching-rules.md)を使用) |**&#x2713;** |**&#x2713;** | |
| キャッシュ/ヘッダーの設定 ( [ルール エンジン](cdn-rules-engine.md)を使用) | | |**&#x2713;** |
| [クエリ文字列のキャッシュ](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 デュアルスタック |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 のサポート](cdn-http2.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **セキュリティ** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| CDN エンドポイントでの HTTPS のサポート |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [カスタム ドメイン HTTPS](cdn-custom-ssl.md) | |**&#x2713;** |**&#x2713;** |
| [カスタム ドメイン名のサポート](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtering](cdn-restrict-access-by-country.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [認証トークン](cdn-token-auth.md)|  |  |**&#x2713;**| 
| [DDOS 保護](https://www.us-cert.gov/ncas/tips/ST04-015) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
| **分析とレポート** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure 診断ログ](cdn-azure-diagnostic-logs.md) | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon からのコア レポート](cdn-analyze-usage-patterns.md) | |**&#x2713;** |**&#x2713;** |
| [Verizon からのカスタム レポート](cdn-verizon-custom-reports.md) | |**&#x2713;** |**&#x2713;** |
| [詳細な HTTP レポート](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [リアルタイム統計](cdn-real-time-stats.md) | | |**&#x2713;** |
| [エッジ ノードのパフォーマンス](cdn-edge-performance.md) | | |**&#x2713;** |
| [リアルタイム アラート](cdn-real-time-alerts.md) | | |**&#x2713;** |
||||
| **使いやすさ** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Storage](cdn-create-a-storage-account-with-cdn.md)、[Cloud Services](cdn-cloud-service-with-cdn.md)、[Web Apps](../app-service/app-service-web-tutorial-content-delivery-network.md)、[Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) などの Azure サービスと簡単に統合 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [REST API](https://msdn.microsoft.com/library/mt634456.aspx)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md)、または [PowerShell](cdn-manage-powershell.md) を介した管理 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [カスタマイズ可能なルール ベースのコンテンツ配信エンジン](cdn-rules-engine.md) | | |**&#x2713;** |
| URL のリダイレクト/書き換え ([ルール エンジン](cdn-rules-engine.md)を使用) | | |**&#x2713;** |
| モバイル デバイスのルール ( [ルール エンジン](cdn-rules-engine.md)を使用) | | |**&#x2713;** |

\* Verizon では、一般的な Web 配信の最適化を介して大きなファイルやメディアを直接配信することができます。



