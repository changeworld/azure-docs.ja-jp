---
title: アプリケーションのシナリオと設計 | Microsoft Docs
description: Service Fabric のクラウド アプリケーションのカテゴリの概要 ステートフル サービスとステートレス サービスを使用したアプリケーションの設計について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228502"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric アプリケーションのシナリオ
Azure Service Fabric はさまざまな種類のビジネス アプリケーションやサービスを作成し実行できる信頼性の高い柔軟なプラットフォームを提供します。 これらのアプリケーションとマイクロサービスはステートレスまたはステートフルが考えられますが、最大限に効率化するために仮想マシン間でリソース分散されます。 Service Fabric 独自のアーキテクチャでは、リアルタイムに近いデータ分析、メモリ内のコンピューティング、並列トランザクション、アプリケーションでのイベント処理を実行できます。 リソース要件の変化に応じて、アプリケーションを簡単にスケール アップまたはスケール ダウン (実際にはスケール インまたはスケール アウト) できます。

アプリケーションの構築での設計のガイダンスについては、「[Microservices architecture on Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)」(Azure Service Fabric でのマイクロサービス アーキテクチャ) および「[Azure Service Fabric アプリケーションの設計に関するベスト プラクティス](service-fabric-best-practices-applications.md)」をご覧ください

Service Fabric プラットフォームは、次の種類のアプリケーションに最適です。

* **データの収集、処理、IoT**: Service Fabric では大規模な処理が行われ、そのステートフル サービスにより待機時間が短くなるため、デバイスのデータと計算用のデータが併置されている多数のデバイスでのデータ処理に最適です。

    Service Fabric を利用して IoT サービスを構築されているお客様としては、[Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)、[PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)、[Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)、[BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)、[Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)、[Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions) などがあります。

* **ゲームおよびセッション ベースの対話型アプリケーション**: オンライン ゲームやインスタント メッセージングなど、低待機時間の読み取りと書き込みが必要なアプリケーションの場合は、Service Fabric が理想的です。 Service Fabric では、専用のストアやキャッシュを作成する必要なしに、対話型のステートフルなアプリケーションを構築できます。 [ゲーム サービスでの Service Fabric の使用](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)に関する設計ガイダンスについては、[Azure のゲーム ソリューション](https://azure.microsoft.com/solutions/gaming/)に関するページをご覧ください

    ゲーム サービスを構築されているお客様としては、[Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) や [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/) などがあります。 対話型セッションを構築されているお客様としては、[Honeywell with Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens) などがあります

* **データ分析とワークフロー処理**: イベントやデータのストリームを確実に処理する必要のあるアプリケーションにも、Service Fabric での最適化された読み取りや書き込みによるメリットがあります。 さらに、Service Fabric では、信頼できる結果を、損失なく次の処理ステージに渡す必要がある、アプリケーション処理パイプラインがサポートされています。 たとえば、取引システムや金融システムです。データの一貫性と確実な計算が重要となります。

    ビジネス ワークフロー サービスを構築されているお客様としては、[Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) や [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/) などがあります

* **データの計算**: Service Fabric では、多くのデータ計算処理を要するステートフルなアプリケーションを構築できます。 Service Fabric では、アプリケーションでの処理 (計算) とデータのコロケーションを実現できます。 通常、アプリケーションでデータにアクセスする必要があるときは、外部のデータ キャッシュまたはストレージ階層に関連するネットワーク待機時間により、計算時間が制限されます。 ステートフルな Service Fabric サービスでは、その待機時間がなくして、いっそう最適化された読み取りと書き込みを実現できます。 たとえば、顧客に対してリアルタイムに近い推奨選択を実行するアプリケーションがあり、そのラウンド トリップ時間の要件が 100 ミリ秒未満であるとします。 リモート ストレージから必要なデータを取得する必要がある標準的な実装モデルの場合と比較して、Service Fabric サービスの待機時間およびパフォーマンス特性 (この場合、推奨選択の計算がデータおよびルールと共存されている) では、ユーザーに応答性の高いエクスペリエンスが提供されます。

    計算サービスを構築されているお客様としては、[Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) や [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura) などがあります

* **高可用性サービス**: Service Fabric では、複数のセカンダリ サービス レプリカを作成することによって、高速のフェールオーバーが提供されます。 ハードウェアまたはその他の障害によってノード、プロセス、または個々のサービスがダウンした場合、セカンダリ レプリカの 1 つが最小限のサービス中断でプライマリ レプリカに昇格されます。

* **スケーラブルなサービス**: クラスター間でスケールアウトの状態になれるよう個々のサービスをパーティション分割できます。 さらに、動作中に個々のサービスの作成および削除を行うことができます。 サービスは、リソースのニーズに応じて、迅速かつ簡単に、数個のノード上の数個のインスタンスから、多数のノード上の数千ものインスタンスにスケールアウトし、その後再びスケールインすることができます。 Service Fabric を使用することで、このようなサービスを構築し、その完全なライフサイクルを管理することができます。

## <a name="application-design-case-studies"></a>アプリケーション設計のケース スタディ
アプリケーション設計における Service Fabric の使用方法を紹介するケース スタディが、[顧客事例](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/)および[マイクロサービス ソリューション サイト](https://azure.microsoft.com/solutions/microservice-applications/)で多数公開されています。

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>ステートレスとステートフルなマイクロサービスから成るアプリケーションを設計する
Azure Cloud Service worker ロールでのアプリケーションの構築は、ステートレス サービスの一例です。 対照的に、ステートフルなマイクロサービスは、要求とその応答を超える権限のある状態を維持します。 この機能では、レプリケーションによって裏付けられたトランザクションの保証を提供するシンプルな API により、状態の高可用性と一貫性が実現されます。 Service Fabric のステートフルなサービスでは、高可用性を平等に実現可能にすることで、データベースなどのデータ ストアだけでなく、すべての種類のアプリケーションに高可用性をもたらします。 これは自然な進展です。 アプリケーションは既に、高可用性のための純粋なリレーショナル データベースから NoSQL データベースに移っています。 アプリケーション自体を「ホット」な状態にして、データを中で管理し、信頼性、一貫性、可用性を犠牲にすることなく、パフォーマンスを上げることができるようになりました。

マイクロサービスで構成されるアプリケーションを構築するとき、通常、ステートレスに呼び出すステートレスな Web アプリ (ASP.NET、Node.js など) と Service Fabric デプロイ コマンドを使ってすべてが同じ Service Fabric クラスターにデプロイされたステートフルなビジネス中間層サービスの組み合わせがあります。 これらのサービスは、スケール、信頼性、およびリソースの使用に関して、それぞれ独立しているため、開発とライフサイクルの管理での機敏性と柔軟性が大幅に向上します。

純粋にステートレスなアプリケーションの可用性と待機時間の要件に対応するために従来必要だった追加のキューとキャッシュは、ステートフル マイクロサービスでは不要になるため、アプリケーション設計が単純化されます。 ステートフル サービスは高可用性と低待機時間が特徴なので、アプリケーション全体で管理すべき変動要素が少なくなります。 次の図では、ステートレスとステートフルなアプリケーション設計の違いを示しています。 [Reliable Services](service-fabric-reliable-services-introduction.md) と [Reliable Actors](service-fabric-reliable-actors-introduction.md) のプログラミング モデルを利用することで、高スループットと低待機時間を実現しながら、ステートフル サービスによりアプリケーションの複雑さが軽減されます。

## <a name="an-application-built-using-stateless-services"></a>ステートレス サービスを使用して構築されたアプリケーション
![ステートレス サービスを使用したアプリケーション][Image1]

## <a name="an-application-built-using-stateful-services"></a>ステートフル サービスを使用して構築されたアプリケーション
![ステートレス サービスを使用したアプリケーション][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次の手順

* [お客様導入事例](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)をお読みください
* [パターンとシナリオ](service-fabric-patterns-and-scenarios.md)の詳細についてご確認ください

* Service Fabric の [Reliable Services](service-fabric-reliable-services-quick-start.md) プログラミング モデルと [Reliable Actors](service-fabric-reliable-actors-get-started.md) プログラミング モデルで、ステートレス サービスとステートフル サービスの構築を始めます。
* [Azure でのマイクロサービスの構築](https://docs.microsoft.com/azure/architecture/microservices/)に関するガイダンスについては、Azure アーキテクチャ センターをご覧ください
* アプリケーションの設計のガイダンスについては、「[Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス](service-fabric-best-practices-overview.md)」をご覧ください。

* 次のトピックもご覧ください。
  * [マイクロサービスについて](service-fabric-overview-microservices.md)
  * [サービスの状態を定義し、管理する](service-fabric-concepts-state.md)
  * [Service Fabric サービスの可用性](service-fabric-availability-services.md)
  * [Service Fabric サービスのスケーリング](service-fabric-concepts-scalability.md)
  * [Service Fabric サービスのパーティション分割](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
