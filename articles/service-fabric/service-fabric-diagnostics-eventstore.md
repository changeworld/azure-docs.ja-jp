---
title: Azure Service Fabric のイベント ストア | Microsoft Docs
description: Azure Service Fabric のイベント ストアについて
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 1d235d5a75975c8d58cad1bbde0f16df2b1b3e7a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206518"
---
# <a name="eventstore-service-overview"></a>EventStore サービスの概要

>[!NOTE]
>Service Fabric バージョン 6.2 の時点で、 EventStore API は現在、Azure 上で実行されている Windows クラスターに対してのみプレビュー段階にあります。 この機能を Linux およびスタンドアロン クラスターに移植する作業を進めています。

## <a name="overview"></a>概要

バージョン 6.2 で導入された EventStore サービスは Service Fabric の監視オプションで、任意の時点でのクラスターまたはワークロードの状態を理解する手段をユーザーに提供します。 EventStore サービスは、呼び出しを行うことができる API を通して Service Fabric イベントを公開しています。 これらの EventStore API により、クラスターに直接クエリを発行して、クラスター内の任意のエンティティについて診断データを取得することができます。API は、以下に役立てるために使用します。
* 開発またはテストにおける問題の診断や、監視パイプラインを使用できる場所の診断
* クラスターに対して行っている管理アクションが、クラスターによって正しく処理されていることの確認

EventStore で使用できるイベントの完全な一覧を確認するには、「[Service Fabric イベント](service-fabric-diagnostics-event-generation-operational.md)」を参照してください。

EventStore サービスでは、クラスター内のエンティティとエンティティ型のそれぞれで使用できるイベントのクエリを実行できます。 これは、以下のレベルでイベントのクエリを実行できることを意味します。
* クラスター: クラスター レベルのすべてのイベント
* ノード: ノード レベルのすべてのイベント
* ノード: 1 つのノードに固有のイベント (`nodeName` に基づく)
* アプリケーション: アプリケーション レベルのすべてのイベント
* アプリケーション: 1 つのアプリケーションに固有のイベント
* サービス: クラスター内のすべてのサービスのイベント
* サービス: 特定のサービスのイベント
* パーティション: すべてのパーティションのイベント
* パーティション: 特定のパーティションのイベント
* レプリカ: すべてのレプリカ/インスタンスのイベント
* レプリカ: 特定のレプリカ/インスタンスのイベント


EventStore サービスは、クラスター内のイベントを関連付ける機能も備えています。 EventStore サービスは、相互に影響を与えた可能性がある異なるエンティティから同時に書き込まれたイベントを調べてそれらのイベントをリンクし、クラスター内で発生したアクティビティの原因の識別に役立てることができます。 たとえば、いずれかのアプリケーションが、誘発された変更を伴わず正常に動作しなくなった場合、EventStore で、プラットフォームによって公開されている他のイベントも調べて、これを `NodeDown` イベントと関連付けることができます。 これは、より迅速な障害の検出と根本原因の分析に役立ちます。

EventStore サービスを使い始めるには、「[EventStore API でクラスター イベントのクエリを実行する](service-fabric-diagnostics-eventstore-query.md)」を参照してください。

## <a name="next-steps"></a>次の手順
* Service Fabric での監視と診断の概要 - [Service Fabric の監視と診断に関するページ](service-fabric-diagnostics-overview.md)
* クラスターの監視についての詳細を表示 - [クラスターとプラットフォームの監視](service-fabric-diagnostics-event-generation-infra.md)。