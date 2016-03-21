<properties
   pageTitle="Service Fabric クラスター リソース マネージャーの概要"
   description="Service Fabric クラスター リソース マネージャーについての概要。"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>


# スロットル
リソース マネージャーを正しく構成している場合でも、クラスターが中断する場合があります (多すぎるノードや障害ドメインのエラー、アップグレードの実行中、更新プログラムの適用中など)。リソース マネージャーは最善を尽くしてすべての修正を試みますが、このような場合、クラスター自体が (復帰しようとしているノード、ネットワーク状態の正常性自体、修正済みのデプロイされるビットを) 安定させるように補強を検討することもできます。このような状況を支援するため、リソース マネージャーにはいくつかのスロットルが含まれています。これはかなりの荒療治であり、クラスターで実行できる並列処理量について十分な計算を行っていない場合や、予期しない巨視的な再構成イベント (別名: “最悪な日”) に対応する必要が頻繁に発生するのでない場合、通常は使用しないでください。

一般的には、クラスターが問題解決を行うときにリソースを使わないようにスロットルを行うよりも、通常のコード更新やクラスターの開始スケジュールの過密を避けるなどの他のオプションを使って最悪な日を回避することをお勧めします。とはいえ、クラスターが安定するまでに時間がかかる場合でも、(解決するまでに) 1 組のスロットルを用意する必要があるケースがあることを判断することができます。

既定で含まれているスロットルには、次のようなものがあります。
-	GlobalMovementThrottleThreshold - これは (GlobalMovementThrottleCountingInterval で秒単位で定義した) 一定の期間にわたるクラスターの移動の合計数を制御します
-	MovementPerPartitionThrottleThreshold – これは (MovementPerPartitionThrottleCountingInterval で秒単位で定義した) 一定の期間にわたるサービス パーティションの移動の合計数を制御します

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

私達が見た中で、このスロットルを使用したお客様のほとんどが次のようなケースだったことに留意してください。それは、
-	お客様は既に (ネットワーク帯域幅がノードごとに制限されているなどの) リソースが制限された環境にあり、いずれにしても操作が成功しなかったか、操作に長い時間を要していました。そのため、
-	お客様はクラスターが安定した状態になるまでに時間がかかる可能性があると分かっても不安を感じることはありませんでした。これには、スロットルの実行中は全体的な信頼性が低下した状態になることも含まれていました。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
- [クラスターでのクラスター リソース マネージャーの負荷分散について (Learn about how the Cluster Resource Manager Balances Load in the Cluster)](service-fabric-cluster-resource-manager-balancing.md)

<!---HONumber=AcomDC_0309_2016-->