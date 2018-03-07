---
title: Azure Service Fabric CLI- sfctl | Microsoft Docs
description: "Service Fabric CLI sfctl のコマンドについて説明します。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 7c8563539ca8507f05fa99fdeffbf511b1540a6a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl"></a>sfctl 
Service Fabric のクラスターとエンティティを管理するためのコマンドです。 このバージョンは Service Fabric 6.1 ランタイムと互換性があります。 コマンドは、名詞 - 動詞のパターンに従いますが、詳細については、次のサブグループを参照してください。

## <a name="subgroups"></a>サブグループ
|サブグループ|[説明]|
| --- | --- |
| [application](service-fabric-sfctl-application.md)| アプリケーションおよびアプリケーションの種類を作成、削除、および管理します。|
| [chaos](service-fabric-sfctl-chaos.md)   | chaos テスト サービスの開始、停止、レポートを実行します。|
| [cluster](service-fabric-sfctl-cluster.md) | Service Fabric クラスターの選択、管理、および操作をします。|
| [compose](service-fabric-sfctl-compose.md) | Docker Compose アプリケーションを作成、削除、および管理します。|
| [is](service-fabric-sfctl-is.md)      | クエリを実行し、インフラストラクチャ サービスにコマンドを送信します。|
| [node](service-fabric-sfctl-node.md)    | クラスターを形成するノードを管理します。|
| [partition](service-fabric-sfctl-partition.md)  | 任意のサービスで、クエリを実行し、パーティションを管理します。|
| property  | Service Fabric の名前でプロパティを格納およびクエリします。|
| [rpm](service-fabric-sfctl-rpm.md)        | クエリを実行し、修復マネージャー サービスにコマンドを送信します。|
| [replica](service-fabric-sfctl-replica.md) | サービス パーティションに属しているレプリカを管理します。|
| [service](service-fabric-sfctl-service.md) | サービス、サービスの種類、およびサービス パッケージを作成、削除、管理します。|
| [store](service-fabric-sfctl-store.md)   | クラスター イメージ ストアで基本的なファイル レベルの操作を実行します。|

## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。