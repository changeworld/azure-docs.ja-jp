<properties
   pageTitle="ノード バッファー使用率"
   description="リソース バランサーでのノード バッファー使用率のロールの概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# ノード バッファー使用率の概要

現在のところ、制約の優先度に基づいてリソース バランサーで順守される制約として、ノード容量上限を指定できます。容量の制約の優先度が高く (ノード容量違反が認められない)、クラスター ノードの使用率が高い場合は、フェールオーバーが即時実行されない可能性や、ノード容量違反が発生する可能性があります。

プライマリ レプリカが配置されているノードが停止したときに、セカンダリ レプリカが配置されているノードがノード容量の上限に近い場合は、問題のスキャンが実行されます。その場合、プライマリの負荷がセカンダリの負荷よりも大きいと、セカンダリ レプリカを直ちに昇格するには、ノードの過剰コミットまたはレプリカのコピーが必要になります。

プロアクティブ パッキング ロジックを実行している場合、ノード容量上限に近づくクラスター ノードの数が増えます。ノード バッファー使用率は、フェールオーバー時間の増加やフェールオーバー中のノードの過剰コミットを防止する機能です。この機能を使用すると、ノードの空き容量の割合を指定することが可能になります。新しいサービスのレプリカは、ノード バッファー領域に追加しないようにする必要がありますが、リソース バランサーは、フェールオーバーや不足しているレプリカを追加するために、合計ノード容量 (バッファー領域を含む) を使用できる必要があります。

この機能は、プロアクティブ メトリック パッキング機能が無効な場合でも使用できます。

コード例では、クラスター マニフェストで FabricSettings 要素を使用して、メトリックのノード バッファー使用率をメトリックごとに構成しています。

``` xml
<FabricSettings>
  <Section Name=" NodeBufferPercentage">
    <Parameter Name="MetricName" Value="0.1"/>
  </Section>
</FabricSettings>

```

"MetricName" という名前のメトリックの値が 0.1 である場合、メトリック "MetricName" のすべてのノード容量に対して 10% の空き容量を維持することを意味します。

このセクションに値が指定されていない場合は、既定値として 0 が使用されます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

詳細: [リソース バランサーのアーキテクチャ](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=July15_HO2-->