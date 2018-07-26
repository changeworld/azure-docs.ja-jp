---
title: Azure モノのインターネット (IoT) の概要
description: Azure IoT と関連するサービスおよびテクノロジの概要について説明します。
author: BryanLa
manager: timlt
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 05/18/2018
ms.author: bryanla
ms.openlocfilehash: ed96181606e2db4102aa609973ade9ecbfde6c90
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187276"
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Azure とモノのインターネットの概要

Azure IoT は、ソリューション、プラットフォーム サービス、エッジという、3 つの領域のテクノロジとソリューションで構成されています。これらはすべて、IoT アプリケーションのエンド ツー エンド開発を促進するように設計されています。 この記事では、最初にクラウドにおける IoT ソリューションの共通特性について説明し、その後、Azure IoT で IoT プロジェクトの課題に対処する方法と Azure IoT の採用を検討する理由について説明します。

## <a name="iot-solution-architecture"></a>IoT ソリューションのアーキテクチャ

IoT ソリューションでは、数百万に達する可能性があるデバイスと、ソリューション バックエンドとの間にセキュリティで保護された双方向通信が必要です。 たとえばソリューションでは、自動化された予測分析を使用して、デバイスとクラウドとの間でやり取りされるイベント ストリームから知見を得ることが考えられます。 

次の図は、一般的な IoT ソリューションのアーキテクチャの主な要素を示しています。 この図は、使用されている Azure サービスなどの特定の実装の詳細や、デバイスのオペレーティング システムには依存しません。 このアーキテクチャでは、IoT デバイスはクラウド ゲートウェイに送信するデータを収集します。 クラウド ゲートウェイは、他のバックエンド サービスが処理にデータを使用できるようにします。 こうしたバックエンド サービスのデータの配信先は以下のとおりです。

* 他の基幹業務アプリケーション。
* 人間のオペレーター (ダッシュボードなどの表示デバイスを使用)。

![IoT ソリューションのアーキテクチャ][img-solution-architecture]

> [!NOTE]
> IoT アーキテクチャの詳細については、「[Microsoft Azure IoT Reference Architecture (Microsoft Azure IoT リファレンス アーキテクチャ)][lnk-refarch]」を参照してください。

### <a name="device-connectivity"></a>デバイスの接続性

IoT ソリューション アーキテクチャでは、通常、デバイスがテレメトリをクラウドに送信して格納し処理します。 たとえば、予測メンテナンスのシナリオでは、ソリューション バックエンドでセンサー データのストリームを使用して、特定のポンプのメンテナンスが必要な時期を判断することがあります。 デバイスは、クラウドからデバイスへのメッセージを受信し、クラウド エンドポイントからのメッセージを読み取り、コマンドに応答することもできます。 同じ例では、ソリューション バックエンドでポンプ場のその他のポンプにメッセージを送信して、メンテナンスの開始予定直前にフローの再ルーティングを開始することが考えられます。 そうすればメンテナンス エンジニアが到着してすぐにメンテナンスを開始することが可能です。

多くの場合、デバイスを安全かつ確実に接続することが、IoT ソリューションの最も大きな課題です。 これは、IoT デバイスには、ブラウザーやモバイル アプリなど、他のクライアントとは異なる特性があるためです。 具体的には、IoT デバイスの特性は次のとおりです。

* 多くの場合、人間が操作することのない組み込みシステムです (電話とは異なります)。
* 物理アクセスに大きなコストがかかる離れた場所にデプロイされている場合があります。
* ソリューション バック エンド経由でしか到達できない場合があります。 デバイスとやり取りする他の方法はありません。
* 電源や処理リソースが限られている場合があります。
* ネットワーク接続が断続的に切れたり、遅かったり、高コストである場合があります。
* 専用、カスタム、または業界固有のアプリケーション プロトコルを使用することが必要になる場合があります。
* 一般的なハードウェアおよびソフトウェア プラットフォームを多数使用して作成できます。

上記の制約に加え、IoT ソリューションはスケーラブルで安全かつ信頼できる必要もあります。

通信プロトコルとネットワークの可用性に応じて、デバイスはクラウドと直接通信するか、中間ゲートウェイを介して通信します。 IoT アーキテクチャでは、多くの場合、この 2 つの通信方式が混在しています。

### <a name="data-processing-and-analytics"></a>データ処理と分析

最新の IoT ソリューションでは、データ処理は、クラウドまたはデバイス側で行われます。 デバイス側の処理は、"*エッジ コンピューティング*" と呼ばれます。 どこでデータが処理されるかは、次の要因によって異なります。

* ネットワーク制約。 デバイスとクラウドの間の帯域幅が限られている場合は、エッジ処理が推進されます。
* 応答時間。 ほぼリアルタイムでデバイスを操作する必要があるときは、デバイス自体で応答を処理する方がよい場合があります。 緊急時に停止する必要があるロボット アームなどです。
* 規制環境。 一部のデータは、クラウドに送信できません。

一般的に、エッジとクラウドの両方でデータを処理する場合は、次のように機能を組み合わせます。

* デバイスから大量のテレメトリを受信し、そのデータを処理および格納する方法を決定します。
* テレメトリを分析して、リアルタイムで処理するか結果に基づいて処理するかについて、洞察を提供します。
* クラウドまたはゲートウェイ デバイスから、特定のデバイスにコマンドを送信します。

さらに、IoT クラウド バックエンドは次を提供する必要があります。

* 次を行うためのデバイス登録機能:
    * 新しいデバイスをプロビジョニングする。
    * どのデバイスに対してインフラストラクチャへの接続を許可するかを制御する。
* デバイスの状態を制御し、そのアクティビティを監視するためのデバイス管理。

たとえば、予測メンテナンスのシナリオでは、テレメトリ データ履歴がクラウド バックエンドで保存されます。 ソリューション は、このデータを使用して、特定のポンプにおける潜在的な異常動作を、その動作によって実際に問題が発生する前に特定します。 データ分析を実行すると、予防ソリューションとは、コマンドをデバイスに送信して、是正措置を実行することであるのがわかります。 このプロセスにより、デバイスとクラウドの間に自動フィードバック ループが生成され、ソリューションの効率性が大幅に向上します。

### <a name="presentation-and-business-connectivity"></a>プレゼンテーションおよびビジネスの接続性

プレゼンテーションおよびビジネスの接続性レイヤーでは、エンド ユーザーが IoT ソリューションおよびデバイスとやり取りできます。 このレイヤーによって、ユーザーが、デバイスから収集されたデータを表示して分析することができます。 これらのビューは、履歴データとほぼリアルタイムのデータの両方を表示できる、ダッシュボードまたは BI レポートの形式をとることができます。 たとえば、オペレーターは、特定のポンプ場の状態を確認し、システムによって生成されるアラートを参照できます。 また、このレイヤーによって、IoT ソリューション バックエンドと既存の基幹業務アプリケーションの統合が可能になり、企業のビジネス プロセスやワークフローと結びつけることができます。 たとえば、予測メンテナンス ソリューションは、メンテナンスを必要とするポンプ を特定したときに、エンジニアのポンプ場訪問を予約するスケジューリング システムと統合することができます。

## <a name="why-azure-iot"></a>Azure IoT を選ぶ理由

Azure IoT は IoT プロジェクトの複雑さを解消し、セキュリティ、インフラストラクチャの非互換性、および IoT ソリューションのスケーリングなどの課題に対処します。 以下に、その方法を示します。

**アジャイル** <br>
IoT への対応を加速させる
* スケール: 小規模から始めて、場所を問わずに任意のサイズまで拡大可能です。世界中のほとんどの地域で、百万単位のデバイス数、テラバイト データを実現します。

* オープン: 手持ちの方法を利用して、任意のデバイス、ソフトウェア、またはサービスに接続することで、将来に向けて最新化します。

* ハイブリッド: クラウドやクラウド間の任意の場所に最新の IoT ソリューションを展開することで、ニーズに合わせて構築します。

* ペース: より迅速に展開し、市場に届くまでの時間を加速させて、先進的なソリューション促進企業との競合や IoT の革新のペースに遅れを取りません。

**包括的** <br>
ビジネスへの影響力を発信する

* 完全型: Microsoft は、クラウドに対する完全なプラットフォーム スパニング デバイスを備えた唯一の IoT ソリューション プロバイダーであり、その範囲はビッグ データ、高度な分析、およびマネージド サービスの利用に及びます。

* 成功へのパートナー: 世界の最大規模のパートナー エコシステムの勢いを取り入れて、業界全体および世界全体で、基幹業務とテクノロジに活力を与えます。

* データ駆動型: IoT はデータに関わるものであり、最適な IoT ソリューションは、適切なユーザーに、適切な場面で、適切なタイミングでデータを格納、解釈、変換、分析、表示する必要があるすべてのツールに活用されます。

* デバイス中心型: Microsoft IoT は、従来の装置から認定ハードウェアの広大なエコシステムまで、あらゆる接続を実現すると共に、エッジ、モバイル、および埋め込みのシステムに及ぶ独自のデバイスの構築を可能にします。

**セキュリティ保護** <br>
IoT の最も困難な部分であるセキュリティを解決する

* 強化: Microsoft IoT では、IoT の最も困難な部分であるセキュリティを解決するためにテクノロジ、ベスト プラクティス、および機能を利用して、お使いのバージョンで運用できます。

* 行動する: 自分の IoT データを保護し、ID を使ってリスク管理すると共に、管理、スレッドと情報の保護、およびセキュリティ管理にアクセスします。

* 安心感: オンプレミス環境だけでなくデバイス、ソフトウェア、アプリケーション、およびクラウド サービスでの機密情報の安全性を確保します。

* コンプライアンス: Microsoft は、IoT デバイス、データ、およびサービスの国際標準および業界固有の標準に幅広く適合するセキュリティ要件を確立するうえで、業界の主要な役割を担ってきました。

## <a name="next-steps"></a>次の手順

以下に示すテクノロジおよびソリューションの分野を確認するか、または Azure IoT サービス一覧の左側にある目次を参照してください。

<ul class="panelContent cardsF">  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>解決方法</h3>
                        <a href="/azure/iot-suite">IoT ソリューション アクセラレータ</a><br/>
                        <a href="/azure/iot-central">IoT Central</a>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>プラットフォーム サービス</h3>
                        <a href="/azure/iot-hub">IoT Hub</a><br/>
                        <a href="/azure/iot-dps">IoT Hub Device Provisioning Service</a><br/>
                        <a href="/azure/azure-maps">Maps</a><br/>
                        <a href="/azure/time-series-insights">Time Series Insights</a>
                    </div>
                </div>
            </div>
        </div>
    </li>  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Microsoft Edge</h3>
                        <a href="/azure/iot-edge">IoT Edge</a><br/>
                        <a href="/azure/iot-edge/how-iot-edge-works">IoT Edge とは?</a>
                    </div>
                </div>
            </div>
        </div>
    </li>      
</ul>

[img-paas-saas-technologies-solutions]: media/index/paas-saas-technologies-solutions.png
[img-solution-architecture]: ./media/iot-introduction/iot-reference-architecture.png
[img-dashboard]: ./media/iot-introduction/iot-suite.png

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-central-land]: https://docs.microsoft.com/microsoft-iot-central/
[lnk-iot-dps-land]: /azure/iot-dps/index.yml
[lnk-iot-edge-land]: /azure/iot-edge/index.yml
[lnk-iot-hub-land]: /azure/iot-hub/index.md
[lnk-iot-maps-land]: /azure/maps/index.yml
[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[lnk-iot-tsi-land]: /azure/time-series-insights/index.yml

[lnk-iot-hub]: ../iot-hub/about-iot-hub.md
[lnk-iot-sa]: ../iot-accelerators/about-iot-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[lnk-protocol-gateway]:  ../iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: https://aka.ms/iotrefarchitecture


