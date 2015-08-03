<properties
   pageTitle="状態の定義と管理"
   description="Service Fabric でサービスの状態を定義し管理する方法"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="aprameyr"/>

# サービスの状態
**サービスの状態** とは、サービスが機能するために必要とするデータのことです。これは、サービスが機能するために読み取りや書き込みをする、データ構造および変数を指します。

例: 簡単な電卓サービスを考えてみましょう。このサービスは、2 つの数値を受け取り、その合計を返します。これは、関連付けられているデータを持たない純粋なステートレス サービスです。

ここで、同じ電卓が、合計を返すだけでなく、計算した最終合計を返すメソッドも持っているとしましょう。このサービスは、ステートフルなサービスです - このサービスには、書き込みをする状態 (新しい合計を計算する場合) と読み取りをする状態 (計算の最終合計を返す場合) とを含むからです。

Service Fabric の場合、前者のサービスはステートレス サービスと呼ばれます。後者のサービスは、ステートフル サービスと呼ばれます。

## サービスの状態の格納
状態は、外部化するか、状態を操作するコードと同じ場所に配置できます。状態の外部化は、通常、外部データベースまたはストアを使用してなされます。電卓の例では、現在の結果がテーブルに格納される SQL データベースなどが該当します。合計値の計算要求ごとに、この行の更新が実行されます。

状態は、このコードを操作するコードと同じ場所に配置することもできます。Service Fabric のステートフル サービスは、このモデルを使用して構築されます。Service Fabric は、可用性が高く、障害発生時のフォールト トレランスの高い状態を実現するインフラストラクチャを提供します。

## 次のステップ

Service Fabric の概念についての詳細は、次を参照してください。

- [Service Fabric サービスの可用性](service-fabric-availability-services.md)

- [Service Fabric サービスの拡張性](service-fabric-concepts-scalability.md)

- [Service Fabric サービスのパーティション分割](service-fabric-concepts-partitioning.md)
 

<!---HONumber=July15_HO4-->