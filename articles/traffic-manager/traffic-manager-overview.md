---
title: "Traffic Manager の概要 | Microsoft Docs"
description: "この記事は、Traffic Manager の内容と、これがアプリケーションに適したトラフィック ルーティングかどうかを理解するのに役立ちます"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 79c14e713fd58d3f69f5978e550a78e8e84d286d

---

# <a name="overview-of-traffic-manager"></a>Traffic Manager の概要

Microsoft Azure Traffic Manager では、さまざまなデータセンターのサービス エンドポイントへのユーザー トラフィックの分散を制御できます。 Traffic Manager でサポートされるサービス エンドポイントには、Azure VM、Web Apps、およびクラウド サービスが含まれます。 Azure 以外の外部エンドポイントで Traffic Manager を使用することもできます。

Traffic Manager では、ドメイン ネーム システム (DNS) を使用して、[トラフィック ルーティング方法](traffic-manager-routing-methods.md)とエンドポイントの正常性に基づいて最適なエンドポイントにクライアント要求を送信します。 Traffic Manager には、さまざまなアプリケーション ニーズ、エンドポイントの正常性、[監視](traffic-manager-monitoring.md)、自動フェールオーバーに対応する、さまざまなトラフィック ルーティング方法が備わっています。 Traffic Manager は Azure リージョン全体の障害などの障害に対応します。

## <a name="traffic-manager-benefits"></a>Traffic Manager の利点

Traffic Manager を使用すると、次のようなメリットが得られます。

* **重要なアプリケーションの可用性の向上**

    Traffic Manager では、エンドポイントを監視し、エンドポイントがダウンした場合の自動フェールオーバー機能を提供することにより、アプリケーションの高可用性を実現します。

* **パフォーマンスに優れたアプリケーションの応答性の向上**

    Azure では、クラウド サービスや Web サイトを世界各地に配置されたデータセンターで実行できます。 Traffic Manager は、クライアントのネットワーク待機時間が最も短いエンドポイントにトラフィックを転送することで、アプリケーションの応答性を高めます。

* **ダウンタイムを伴わないアップグレードとサービス メンテナンスの実行**

    ダウンタイムを発生させることなく、アプリケーションで定期的なメンテナンスを行うことができます。 メンテナンス中は、Traffic Manager がトラフィックを別のエンドポイントに送信します。

* **オンプレミス アプリケーションとクラウド アプリケーションの結合**

    Traffic Manager では、Azure 以外の外部エンドポイントがサポートされています。これにより、Traffic Manager は、"クラウドへのバースト"、"クラウドへの移行"、"クラウドへのフェールオーバー" など、ハイブリッド クラウドとオンプレミス デプロイメントで使用することができます。

* **大規模で複雑なデプロイメントのトラフィック分散**

    [入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)を使用することにより、トラフィック ルーティング方法を組み合わせて、柔軟で洗練されたトラフィック ルーティング スキームを作成し、より大規模で複雑なデプロイのニーズに対応できます。

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>次のステップ

* [Traffic Manager のしくみ](traffic-manager-how-traffic-manager-works.md)の詳細を確認します。
* [Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md)機能を使用して、高可用性アプリケーションを開発する方法を確認します。
* Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認します。
* [Traffic Manager プロファイルを作成します](traffic-manager-manage-profiles.md)。



<!--HONumber=Nov16_HO3-->


