<properties
   pageTitle="Service Fabric プログラミング モデル | Microsoft Azure"
   description="Service Fabric では、アクター フレームワークとサービス フレームワークという、サービスを構築するための 2 つのフレームワークが提供されています。それぞれ、シンプルさとコントロールという、両極端の特性を持っています。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="seanmck"/>

# サービスのフレームワークを選択する

Azure Service Fabric は、Reliable Services API と Reliable Actors API という、サービスを構築するための 2 つの高度なフレームワークを提供します。いずれも同一の Service Fabric コア上に構築されていますが、同時実行、パーティション分割、および通信の点で、簡潔性と柔軟性の釣り合いの取り方に違いがあります。アプリケーション内の特定のサービスに適切なフレームワークを選択できるように、両方のモデルを理解しておくと便利です。

## Reliable Actors API と Reliable Services API の比較

|**Reliable Actors API を選択する場合**|**Reliable Services API を選択する場合**|
|-----------------------|--------------------------|
|問題空間に、状態とロジックの小さな独立したユニットが多数 (1,000 以上) 含まれている。|複数のコンポーネント間でロジックを維持する必要がある。|
|外部との対話をあまり必要としないシングル スレッドのオブジェクトを操作する。|Reliable Collection (.NET Reliable Dictionary や Reliable Queue など) を使用して状態を格納し、管理する。|
|プラットフォームで通信を自動的に管理する。|通信の管理とサービスのパーティション構成の制御を自分で行う。|

アプリ内のサービスごとに異なるフレームワークを使用することは、非常に合理的であることに留意してください。たとえば、多くのアクターによって生成されたデータを集計するステートフル サービスを使用する場合があります。

## 次のステップ

- [Reliable Actors API の詳細](service-fabric-reliable-actors-introduction.md)
- [Reliable Services API の詳細](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0128_2016-->