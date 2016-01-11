<properties
   pageTitle="Service Fabric クラスター オーケストレーション配置の制約 | Microsoft Azure"
   description="Service Fabric における配置の制約の概念的概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="abhic"/>

# 配置の制約の概要

Azure Service Fabric を使用すると、開発者はサービス レプリカの配置先を、特定の条件を満たすノードに制限することができます。これらの条件は、適切なサービス コンテキスト固有の値で評価されるブール式を使用して表現されます。


## 機能
配置の制約を使用して、次の操作を実行できます。

- ノードで NodeProperties を定義して、さまざまな種類のノードでさまざまな種類のサービスを制限します。

- 特定の制約を、セカンダリ レプリカではなくプライマリ レプリカに適用します。


## 主要な概念
NodeProperty - ユーザーまたはシステムによって定義される、ノード (つまり、NodeName) ごとに変わる可能性がある文字列から値へのマップ。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

詳細については、「[アプリケーション シナリオ](../service-fabric-application-scenarios)」を参照してください。

<!---HONumber=AcomDC_1223_2015-->