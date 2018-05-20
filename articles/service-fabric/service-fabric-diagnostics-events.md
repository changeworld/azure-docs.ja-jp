---
title: Azure Service Fabric イベント | Microsoft Docs
description: Azure Service Fabric クラスターの監視に役立つように標準で提供されている Service Fabric イベントについて説明します。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 6379f463d5c2e9027e1d8d4dd90db61cb72cdd44
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="service-fabric-events"></a>Service Fabric イベント 

Service Fabric プラットフォームは、クラスター内で発生している重要な運用アクティビティについて、いくつかの構造化されたイベントを書き込みます。 その範囲は、クラスターのアップグレードから、レプリカの配置決定までにわたります。 Service Fabric が公開している各イベントは、クラスター内の以下のエンティティのいずれかにマッピングされています。
* クラスター
* アプリケーション
* サービス
* Partition
* レプリカ 
* コンテナー

プラットフォームによって公開されるイベントの完全な一覧については、[Service Fabric イベントの一覧](service-fabric-diagnostics-event-generation-operational.md)を参照してください。

以下に、クラスター内でイベントを確認する必要がある、いくつかの重要なシナリオの例を示します。 
1. ノードのライフ サイクル イベント: ノードの起動、停止、アクティブ化/非アクティブ化、または再起動が発生すると、何が発生したかを示すイベントが公開され、マシン自体に問題があるかどうかや、ノードの状態を変更するために SF を通じて呼び出された API があったかどうかを識別するのに役立ちます。
1. クラスターのアップグレード: クラスターがアップグレードされるとき (SF バージョンまたは構成の変更) には、アップグレードの開始、UD それぞれでの進行、および完了 (またはロールバック) が示されます。 
1. アプリケーションのアップグレード: クラスターのアップグレードと同様に、アップグレードが進行するときには包括的なイベントのセットが存在します。 これらのイベントは、アップグレードがスケジュールされているタイミング、アップグレードの現在の状態、およびイベントの全体的なシーケンスを解釈するのに役立つ場合があります。 これは、どのアップグレードが正常にロールアウトされたかを後から確認するのに便利です。
1. アプリケーション/サービスのデプロイ/削除: アプリケーション、サービス、およびコンテナーの作成や削除があるたびに、イベントが存在します。
1. パーティションの移動 (再構成): ステートフルなパーティションの再構成が行われる (レプリカ セット内での変更) たびに、イベントがログ記録されます。 これは、パーティションのレプリカ セットが変更されている頻度を把握しようとする場合や、任意の時点でどのノードでプライマリ レプリカが実行されていたかを追跡しようとする場合に役立ちます。
1. Chaos イベント: Service Fabric の [Chaos](service-fabric-controlled-chaos.md) サービスの使用時には、サービスが開始または停止されるたびにイベントが示されます。また、サービスがシステムにエラーを挿入するとイベントが示されます。
1. 正常性イベント: Service Fabric は、警告またはエラーの正常性レポートが作成されるたび、エンティティが OK の正常性状態に戻るたび、または正常性レポートの有効期限が切れたびに正常性イベントを公開します。 これらのイベントは、エンティティの正常性に関する履歴の統計を追跡する場合に非常に役立ちます。 

## <a name="how-to-access-events"></a>イベントへのアクセス方法

Service Fabric イベントには、いくつかの異なる方法を通してアクセスすることができます。
* 稼働チャネルを介して。 これらは、Azure 診断拡張機能を通して収集し、OMS Log Analytics などのツールでの消費や取り込みのためにストレージ テーブルに送信することができます。 クラスターに対する「診断」が有効にされると、クラスター上に Azure 診断エージェントがデプロイされ、既定で稼働チャネルからのログを読み取るように構成されます。 より多くのログやパフォーマンス カウンターを収集するようにクラスターの診断構成を変更するには、[Azure 診断エージェント](service-fabric-diagnostics-event-aggregation-wad.md)の構成に関する詳細をご確認ください。 
* EventStore サービスの Rest API を介して。この方法では、クラスターに直接、または Service Fabric クライアント ライブラリを介してクエリを発行することができます。 「[EventStore API でクラスター イベントのクエリを実行する](service-fabric-diagnostics-eventstore-query.md)」を参照してください。

## <a name="next-steps"></a>次の手順
* クラスターの監視についての詳細を表示 - [クラスターとプラットフォームの監視](service-fabric-diagnostics-event-generation-infra.md)。
* EventStore サービスについての詳細を表示 - [EventStore サービスの概要](service-fabric-diagnostics-eventstore.md)
