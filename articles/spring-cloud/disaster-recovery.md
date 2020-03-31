---
title: Azure Spring Cloud の geo ディザスター リカバリー | Microsoft Docs
description: Spring Cloud アプリケーションを地域的な停電から保護する方法について説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279152"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure Spring Cloud のディザスター リカバリー

この記事では、Azure Spring Cloud アプリケーションをダウンタイムの発生から保護するために使用できるいくつかの戦略について説明します。  リージョンまたはデータセンターでは、地域的な災害によってダウンタイムが発生する可能性がありますが、慎重に計画することで顧客への影響を軽減できます。

## <a name="plan-your-application-deployment"></a>アプリケーションのデプロイを計画する

Azure Spring Cloud アプリケーションは、特定のリージョンで実行されます。  Azure は、世界中の複数の geo で動作します。 Azure の geo とは、少なくとも 1 つの Azure リージョンを含む、世界の定義済みの地域です。 Azure リージョンは、geo に含まれる領域で、1 つ以上のデータセンターが含まれます。  各 Azure リージョンは、同じ geo 内の別のリージョンと組み合わせて、リージョン ペアにして使用します。 Azure では、リージョンのペアに対してプラットフォームの更新 (計画メンテナンス) をシリアル化し、一度に更新されるリージョンが各ペアのうち 1 つのみになるようにしています。 障害のイベントが複数のリージョンに影響を与える場合、各ペアの少なくとも 1 つのリージョンが優先的に復旧されます。

高可用性と災害からの保護を確保するには、Spring Cloud アプリケーションを複数のリージョンにデプロイする必要があります。  Azure には[ペアになっているリージョン](../best-practices-availability-paired-regions.md)の一覧が用意されているため、Spring Cloud のリージョン ペアへのデプロイを計画できます。  マイクロサービス アーキテクチャを設計する際には、利用可能なリージョン、Azure のペアになっているリージョン、サービスの可用性という 3 つの重要な要素を考慮することをお勧めします。

*  利用可能なリージョン:ネットワークの遅延と転送時間を最小限に抑えるために、ユーザーの近くにある地理的な領域を選択します。
*  Azure のペアになっているリージョン:選択した地域内でペアになっているリージョンを選択し、プラットフォームの調整された更新を確保し、必要に応じて、優先順位の高い復旧作業を行います。
*  サービスの可用性: ペアになっているリージョンの動作を、ホット/ホット、ホット/ウォーム、またはホット/コールドのいずれにするかを決定します。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager を使用してトラフィックをルーティングする

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) には、DNS ベースのトラフィックの負荷分散機能があるため、複数のリージョンにわたってネットワーク トラフィックを分散させることができます。  Azure Traffic Manager を使用して、顧客を最も近い Azure Spring Cloud サービス インスタンスに転送します。  最適なパフォーマンスと冗長性を実現するには、Azure Spring Cloud サービスに送信する前に、すべてのアプリケーション トラフィックを Azure Traffic Manager 経由で送信します。

複数のリージョンに Azure Spring Cloud アプリケーションがある場合は、Azure Traffic Manager を使用して、各リージョンでのアプリケーションへのトラフィック フローを制御します。  サービス IP を使用して、サービスごとに Azure Traffic Manager エンドポイントを定義します。 顧客は、Azure Spring Cloud サービスを指す Azure Traffic Manager の DNS 名に接続する必要があります。  Azure Traffic Manager では、定義されたエンドポイント間でトラフィックの負荷が分散されます。  データ センターが災害に遭った場合、Azure Traffic Manager により、そのリージョンからそのペアにトラフィックが転送され、サービスの継続性が確保されます。