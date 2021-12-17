---
title: Azure Service Fabric 動的ノード タグ
description: Azure Service Fabric を使用すると、ノード タグを動的に追加および削除できます。
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: b20ab4720f9f172ef9248d3314b25922896eb8bd
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796932"
---
# <a name="introduction-to-dynamic-node-tags"></a>動的ノード タグの概要
ノード タグを使用すると、サービスの配置を変更するために、ノードのタグを動的に追加したり削除したりすることができます。 ノードのタグ付けには非常に柔軟性があり、アプリケーションまたはクラスターをアップグレードすることなく、サービスの配置を変更できます。 タグはいつでもノードに対して追加または削除できます。サービスは、特定のタグの作成時にその要件を指定できます。 また、サービスは、タグの要件をその実行中に動的に更新することもできます。

ノードのタグ付けは[配置制約](service-fabric-cluster-resource-manager-configure-services.md)に似ており、通常はサービスがどのノード上で実行されるかを制御するために使用されます。 各 Service Fabric サービスは、タグを配置する必要があるように構成することも、実行を継続するように構成することもできます。

ノードのタグ付けは、Service Fabric がホストするすべてのサービスの種類 (Reliable Services、ゲスト実行可能ファイル、コンテナー) でサポートされています。 ノードのタグ付けを使用するには、Service Fabric ランタイムのバージョン 8.0 以降が実行されている必要があります。

この記事の残りの部分では、ノードのタグ付けを有効または無効にする方法について説明し、この機能をどのように使用するかの例を示します。


## <a name="describing-dynamic-node-tags"></a>動的ノード タグの記述
サービスでは、必要なタグを指定できます。 タグには 2 種類あります。
* **配置に必要なタグ** は、サービスの配置にのみ必要なタグのセットを記述します。 これらのタグは、レプリカが配置された後、サービスを中断することなく削除することができます。 これらのタグのいずれかがノードから削除されても、サービス レプリカは引き続き機能し、Service Fabric によってサービスが削除されることはありません。

* **実行に必要なタグ** は、サービスの配置と実行の両方に必要なタグのセットを記述します。 実行に必要なタグのいずれかが削除された場合、Service Fabric は、それらのタグが指定された別のノードにサービスを移動します。

例: 配置に必要なタグは、何らかの種類のコンテナー アクティベーター サービスを使用していて、コンテナーを配置するのにそのサービスが必要であるが、コンテナーがアクティブになるとすぐにそのアクティベーターは不要になり、それに関連するタグを削除してもコンテナーは実行され続ける必要がある場合に利用できます。
実行に必要なタグは、課金サービスがある場合に使用でき、ユーザー向けのサービスと併置すると便利です。 ノードで課金サービスに障害が発生した場合、それに関連するタグを削除すると、ユーザー向けサービスは、課金サービスとそのタグが存在する別のノードに移動します。

タグまたはタグのセットは、C# API、REST API、PowerShell コマンドなどの標準の Service Fabric インターフェイス機構を使用して、1 つのノードから追加、更新、または削除できます。

> [!NOTE]
> Service Fabric では、ノード タグを使用するときに UD/FD 分布が保持されません。 ノード タグを適切に管理し、ドメイン間でのタグの不適切な分布による FD/UD 違反が発生しないようにしてください。

## <a name="enabling-dynamic-node-tags"></a>動的ノード タグの有効化
この機能を有効にするには、XML または JSON を使用して、クラスター マニフェストの PlacementAndLoadBalancing セクションで NodeTaggingEnabled 構成を有効にする必要があります。

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "NodeTaggingEnabled",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>動的ノード タグの設定

### <a name="using-powershell"></a>PowerShell の使用

ノードへのノード タグの追加

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
このコマンドでは、ノード DB.1 にタグ "SampleTag1" と "SampleTag2" を追加します。

ノードからのノード タグの削除

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
このコマンドでは、ノード DB.1 のタグ "SampleTag1" と "SampleTag2" を削除します。

### <a name="using-c-apis"></a>C# API の使用

ノードへのノード タグの追加

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

ノードからのノード タグの削除

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>サービスに必要なタグの設定

### <a name="using-powershell"></a>PowerShell の使用

新しいサービスの作成

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
このコマンドでは 1 つのサービスが作成されます。そのサービスでは、サービスをそこに配置するために "SampleTag2" がノードに存在する必要があり、そのノード上でサービスが実行を継続するために "SampleTag1" が存在する必要があります。

既存のサービスの更新

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
このコマンドでは 1 つのサービスが更新されます。そのサービスでは、サービスをそこに配置するために "SampleTag2" がノードに存在する必要があり、そのノード上でサービスが実行を継続するために "SampleTag1" が存在する必要があります。

### <a name="using-c-apis"></a>C# API の使用

新しいサービスの作成

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

既存のサービスの更新

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

これらのコマンドはすべて、ステートレス サービスにも同様に適用されます。

## <a name="next-steps"></a>次のステップ
[配置の制約](service-fabric-cluster-resource-manager-configure-services.md)の詳細を確認してください。
