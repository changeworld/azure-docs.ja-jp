<properties
   pageTitle="Reliable Actors フレームワークにおけるポリモーフィズム | Microsoft Azure"
   description="Reliable Actors フレームワークで .NET のインターフェイスと型の階層を作成して、機能と API の定義を再利用します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/08/2016"
   ms.author="seanmck"/>

# Reliable Actors フレームワークにおけるポリモーフィズム

Reliable Actors フレームワークは、オブジェクト指向設計で使用する手法の多くを使用してサービスを構築できるようにすることで、分散システムのプログラミングを簡素化します。このような手法の 1 つがポリモーフィズムで、型とインターフェイスが汎用性の高い親から継承できるようにします。Reliable Actors フレームワークにおける継承は、一般に .NET モデルに従いますが、追加の制約がいくつかあります。

## インターフェイス

Reliable Actors フレームワークでは、アクター型によって実装される 1 つ以上のインターフェイスを定義する必要があります。このインターフェイスを使用して、クライアントがアクターとの通信に使用できるプロキシ クラスを生成します。アクター型によって実装されるすべてのインターフェイスとそのすべての親が、最終的に IActor から派生していれば、インターフェイスは他のインターフェイスから継承できます。IActor は、プラットフォームで定義されているアクターの基本インターフェイスです。したがって、図形を使用する従来のポリモーフィズムの例は次のようになります。

![図形アクターのインターフェイス階層][shapes-interface-hierarchy]


## 型

プラットフォームで提供される Actor 基本クラスから派生したアクター型の階層を作成することもできます。ステートフル アクターの場合、状態の種類の階層を同様に作成できます。図形の場合、状態の種類が `ShapeState` の基本 `Shape` 型を使用します。

    public abstract class Shape : Actor<ShapeState>, IShape
    {
        ...
    }

`Shape` のサブタイプでは、`ShapeType` のサブタイプを使用して、より詳細なプロパティを格納できます。

    [ActorService(Name = "Circle")]
    public class Circle : Shape, ICircle
    {
        private CircleState CircleState => this.State as CircleState;

        public override ShapeState InitializeState()
        {
            return new CircleState();
        }

        [Readonly]
        public override Task<int> GetVerticeCount()
        {
            return Task.FromResult(0);
        }

       [Readonly]
       public override Task<double> GetArea()
       {
           return Task.FromResult(
               Math.PI*
               this.CircleState.Radius*
               this.CircleState.Radius);
       }

       ...
    }

アクター型の `ActorService` 属性に注意してください。この属性は、この型のアクターをホストするためのサービスを自動的に作成する必要があることを Azure Service Fabric SDK に通知します。サブタイプとの機能の共有だけを目的とし、具体的なアクターのインスタンス化には使用しない基本型を作成する場合があります。このような場合、`abstract` キーワードを使用して、その型に基づくアクターは作成しないことを示す必要があります。


## 次のステップ

- 信頼性、スケーラビリティ、一貫性のある状態を提供するために、[Reliable Actors フレームワークで Service Fabric プラットフォームを利用する方法](service-fabric-reliable-actors-platform.md)
- [アクターのライフサイクル](service-fabric-reliable-actors-lifecycle.md)の詳細

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

<!---HONumber=AcomDC_0309_2016-->