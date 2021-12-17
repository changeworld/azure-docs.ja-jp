---
title: Microsoft 365 サービスに ExpressRoute を使用する | Microsoft Docs
description: このドキュメントでは、Microsoft 365 SaaS サービスに ExpressRoute 回線を使用する場合について客観的に説明します。
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 4/29/2021
ms.author: rambala
ms.openlocfilehash: 6e6b543741a27b7a99ef447e7e34fbec803403db
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970499"
---
# <a name="using-expressroute-for-routing-microsoft-365-traffic"></a>Microsoft 365 トラフィックのルーティングに ExpressRoute を使用する

ExpressRoute 回線により、Microsoft バックボーン ネットワークへのプライベート接続が提供されます。 
* Azure リージョンで IaaS デプロイのプライベート エンドポイントに接続するための "*プライベート ピアリング*" が提供されます。 
* また、Microsoft ネットワークの IaaS、PaaS、SaaS の各サービスのパブリック エンドポイントに接続するための "*Microsoft ピアリング*" も提供されます。 

ExpressRoute の詳細については、[ExpressRoute の概要][ExR-Intro]に関する記事を参照してください。


よく混乱を招くのは、ExpressRoute を Microsoft 365 SaaS トラフィックのルーティングに使用できるかどうかです。 

* 一方の主張: ExpressRoute により提供される Microsoft ピアリングを使用して、Microsoft ネットワークのほとんどのパブリック エンドポイントに接続できます。 実際に、"*ルート フィルター*" を使用して、Microsoft ピアリング経由でオンプレミス ネットワークにアドバタイズする必要がある Microsoft 365 サービス プレフィックスを選択できます。 これらのルート アドバタイズによって、ExpressRoute 回線での Microsoft 365 サービス トラフィックのルーティングが可能になります。 
* もう一方の主張: Microsoft 365 は分散サービスです。 世界中の顧客がインターネットを使用してサービスに接続できるように設計されています。 そのため、ExpressRoute を Microsoft 365 に使用しないことをお勧めします。

この記事の目標を以下に示します。 
* 議論の技術的な理由を提供する 
* Microsoft 365 トラフィックのルーティングにおいて、ExpressRoute を使用する場合と使用しない場合について客観的に説明する

## <a name="network-requirements-of-microsoft-365-traffic"></a>Microsoft 365 トラフィックのネットワーク要件
Microsoft 365 サービスには、多くの場合、音声とビデオの通話、オンライン会議、リアルタイム コラボレーションなどのリアルタイム トラフィックが含まれます。 このリアルタイム トラフィックには、待機時間とジッターの観点から厳しいネットワーク パフォーマンス要件があります。 ネットワーク待機時間の特定の制限内で、クライアント デバイスでバッファーを使用して、ジッターを効果的に処理できます。 ネットワーク待機時間は、トラフィックが移動しなければならない物理的距離、リンク帯域幅、ネットワーク処理待機時間の作用です。 

## <a name="network-optimization-features-of-microsoft-365"></a>Microsoft 365 のネットワーク最適化機能 

Microsoft は、アーキテクチャと機能の両方の観点から、すべてのクラウド アプリケーションのネットワーク パフォーマンスを最適化することに努めています。 まず、Microsoft は、最大のグローバル ネットワークのうちの 1 つを所有しており、これは最高のネットワーク パフォーマンスを提供するという中心的目標を実現するために最適化されています。 Microsoft ネットワークはソフトウェア定義されており、"コールド ポテト" ネットワークです。 "コールド ポテト" ネットワークとは、クライアント デバイスまたはカスタマー ネットワークのできるだけ近くにトラフィックを引き付け、送信するという意味です。 さらに、Microsoft ネットワークは高い冗長性と高可用性を備えています。 アーキテクチャ最適化の詳細については、「[マイクロソフトは高速で信頼性の高いグローバル ネットワークをどのように構築しているのか][MGN]」を参照してください。

厳しいネットワーク待機時間の要件に対応するために、Microsoft 365 では次の方法でルートの長さを短縮しています。
* 最も近い Microsoft 365 エントリ ポイントにエンドユーザー接続を動的にルーティングする 
* そのエントリ ポイントから、それらを Microsoft グローバル ネットワーク内で最も近い (および承認されている) Microsoft 365 データ センターに効率的にルーティングする

Microsoft 365 エントリ ポイントは、Azure Front Door (AFD) によって処理されます。 AFD は、Microsoft のグローバル エッジ ネットワークに存在する広く分散されたサービスであり、高速かつ安全で拡張性の高い SaaS アプリケーションを作成するのに役立ちます。 AFD が Web アプリケーションのパフォーマンスをどのようにして促進するかをより理解するには、「[Azure Front Door とは][AFD]」を参照してください。 Microsoft では、最も近い Microsoft 365 データ センターを選ぶ一方で、地政学的リージョン内のデータ主権に関する規制を考慮しています。

## <a name="what-is-geo-pinning-connections"></a>geo ピン留め接続とは

クライアント サーバー間で、トラフィックが地理的な場所に配置された特定のネットワーク デバイスを通るよう強制することを、ネットワーク接続の geo ピン留めと呼びます。 従来のネットワーク アーキテクチャでは通常、クライアント サーバーが静的に配置されている基本的な設計原則を使用して、接続が geo ピン留めされます。
たとえば、企業のインターネット接続が企業のネットワークを通過し、中央の場所 (通常は一連のプロキシ サーバーまたはファイアウォール経由) から送信されるよう強制すると、インターネット接続を geo ピン留めしていることになります。  

同様に、SaaS アプリケーション アーキテクチャで、トラフィックがリージョン内の中間データセンター (クラウド セキュリティなど) を通るように、あるいは特定の場所にある 1 つ以上の中間ネットワーク デバイス (ExpressRoute など) を経由するようにルーティングを強制する場合は、SaaS 接続を geo ピン留めしていることになります。

## <a name="when-not-to-use-expressroute-for-microsoft-365"></a>ExpressRoute を Microsoft 365 で使用しない場合

ルートの長さを動的に短縮でき、クライアントの場所に応じて最も近いサーバー データセンターを動的に選択できることから、Microsoft 365 はインターネット用に設計されていると言えます。 さらに、特定の Microsoft 365 トラフィックは、インターネットを通してのみルーティングされます。
SaaS クライアントがリージョン全体またはグローバルに広く分散しているとき、接続を特定の場所に geo ピン留めすると、geo ピン留めした場所からクライアントを大きく遠ざからせることになり、ネットワーク待機時間が長くなってしまいます。 ネットワーク待機時間が長くなると、ネットワーク パフォーマンスが不足し、アプリケーション パフォーマンスの低下につながります。

そのため、SaaS クライアントが広く分散している、またはクライアントの移動が激しいシナリオでは、特定のピアリング場所の ExpressRoute 回線を経由するようトラフィックに強制するなど、あらゆる手段で接続を geo ピン留めしないようにします。


## <a name="when-to-use-expressroute-for-microsoft-365"></a>ExpressRoute を Microsoft 365 で使用する場合

ExpressRoute を Microsoft 365 トラフィックのルーティングに使用する場合の理由を次にいくつか挙げます。
* SaaS クライアントが地理的な場所に集中しており、ExpressRoute 回線を経由するのが Microsoft グローバル ネットワークに接続する最適な方法である
* SaaS クライアントが複数のグローバルな場所に集中しており、各場所にあるそれぞれの ExpressRoute 回線により Microsoft グローバル ネットワークへの最適な接続が提供されている
* クラウド主体のトラフィックをプライベート接続を介してルーティングすることが法律で規定されている
* すべての SaaS トラフィックを geo ピン留めされた一元化された場所 (プライベートまたはパブリックのデータセンター) にルーティングする必要があり、一元化された場所を Microsoft グローバル ネットワークに接続するための最適な方法が ExpressRoute を経由することである
* 一部の静的 SaaS クライアントには ExpressRoute でのみ最適な接続が提供されるが、他のクライアントはインターネットを使用する

ExpressRoute を使用する場合、ExpressRoute の Microsoft ピアリングに関連したルート フィルターを適用して Microsoft 365 サービスや Azure PaaS サービスのサブセットのみを ExpressRoute 回線経由でルーティングできます。 詳しくは、[チュートリアル: Microsoft ピアリング用のルート フィルターを構成][ExRRF]に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Microsoft Teams がフローを呼び出す方法と、最良の結果を得るために Express Route を使用する場合などのさまざまなシナリオでネットワーク接続を最適化する方法については、「[Microsoft Teams 通話フロー][Teams]」を参照してください。
* 個々のオフィスの場所での Microsoft 365 の接続に関する問題を把握するためにテストを行う場合は、「[Microsoft 365 ネットワーク接続のテスト][Microsoft 365-Test]」を参照してください。
* Microsoft 365 のパフォーマンスに関する新たな問題を検出するためのベースラインとパフォーマンス履歴を確立するには、「[ベースラインとパフォーマンス履歴を使用した Office 365 のパフォーマンス チューニング][Microsoft 365perf]」を参照してください。

<!--Link References-->
[ExR-Intro]: ./expressroute-introduction.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[MGN]: https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/
[AFD]: ../frontdoor/front-door-overview.md
[ExRRF]: ./how-to-routefilter-portal.md
[Teams]: /microsoftteams/microsoft-teams-online-call-flows
[Microsoft 365-Test]: https://connectivity.office.com/
[Microsoft 365perf]: /microsoft-365/enterprise/performance-tuning-using-baselines-and-history