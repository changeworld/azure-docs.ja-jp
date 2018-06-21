---
title: Azure Service Fabric CLI- sfctl | Microsoft Docs
description: Service Fabric CLI sfctl のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 456257e54da83ac629039b714cc74f9dafda2174
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763274"
---
# <a name="sfctl"></a>sfctl
Service Fabric のクラスターとエンティティを管理するためのコマンドです。 このバージョンは Service Fabric 6.2 ランタイムと互換性があります。

コマンドは、名詞と動詞のパターンに従います。 詳しくはサブグループをご覧ください。

## <a name="subgroups"></a>サブグループ
|サブグループ|説明|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | アプリケーションおよびアプリケーションの種類を作成、削除、および管理します。 |
| [chaos](service-fabric-sfctl-chaos.md) | chaos テスト サービスの開始、停止、レポートを実行します。 |
| [cluster](service-fabric-sfctl-cluster.md) | Service Fabric クラスターの選択、管理、および操作をします。 |
| [compose](service-fabric-sfctl-compose.md) | Docker Compose アプリケーションを作成、削除、および管理します。 |
| [container](service-fabric-sfctl-container.md) | クラスター ノード上でコンテナー関連のコマンドを実行します。 |
| [is](service-fabric-sfctl-is.md) | クエリを実行し、インフラストラクチャ サービスにコマンドを送信します。 |
| [node](service-fabric-sfctl-node.md) | クラスターを形成するノードを管理します。 |
| [partition](service-fabric-sfctl-partition.md) | 任意のサービスで、クエリを実行し、パーティションを管理します。 |
| [property](service-fabric-sfctl-property.md) | Service Fabric の名前でプロパティを格納およびクエリします。 |
| [replica](service-fabric-sfctl-replica.md) | サービス パーティションに属しているレプリカを管理します。 |
| [rpm](service-fabric-sfctl-rpm.md) | クエリを実行し、修復マネージャー サービスにコマンドを送信します。 |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | スタンドアロンの Service Fabric クラスターを管理します。 |
| [service](service-fabric-sfctl-service.md) | サービス、サービスの種類、およびサービス パッケージを作成、削除、管理します。 |
| [store](service-fabric-sfctl-store.md) | クラスター イメージ ストアで基本的なファイル レベルの操作を実行します。 |

## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。