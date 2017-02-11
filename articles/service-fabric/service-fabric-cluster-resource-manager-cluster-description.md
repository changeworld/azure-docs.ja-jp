---
title: "リソース バランサー クラスターの記述 | Microsoft Docs"
description: "障害ドメイン、アップグレード ドメイン、ノードのプロパティ、ノード容量をクラスター リソース マネージャーに指定し、Service Fabric クラスターを記述します。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5c8da2a9fcb824deb2a15a661a5ea355984e4fab


---
# <a name="describing-a-service-fabric-cluster"></a>Service Fabric クラスターの記述
Service Fabric クラスター リソース マネージャーには、クラスターを記述するためのメカニズムが複数用意されています。 クラスター リソース マネージャーは、実行時にこの情報を利用することで、クラスターのリソースが適切に使用されるように管理しながら、クラスターで実行されているサービスの高可用性を確保します。

## <a name="key-concepts"></a>主要な概念
クラスター リソース マネージャーでサポートされているクラスターを記述する機能には次のようなものがあります。

* 障害ドメイン
* アップグレード ドメイン
* ノードのプロパティ
* ノード容量

## <a name="fault-domains"></a>障害ドメイン
障害ドメインとは、障害について調整されている領域です。 1 台のコンピューターは 1 つの障害ドメインになります (電源障害、ドライブ障害、問題のある NIC ファームウェアなど、さまざまな理由から単独で停止する可能性があるためです)。 複数のコンピューターが同じイーサネット スイッチに接続されている場合は、1 つの電源に接続されている場合と同様、すべてのコンピューターが 1 つの障害ドメインに属しています。 これらは当然重複するため、障害ドメインは本質的に階層的で、Service Fabric での URI として表されます。

クラスターを自分で設定した場合は、こうした各種障害領域をすべて考慮し、Service Fabric がサービスを安全に配置できる場所を把握できるように障害ドメインが適切に設定されていることを確認する必要があります。 "安全に" とは、実際のところ "的確に" という意味で使用しています。マイクロソフトでは、障害ドメインがないことが原因で (前記のいずれかのコンポーネントのエラーなど) サービス停止が発生するような状況にサービスを配置することは避けたいと考えています。  Azure 環境では、環境によって提供される障害ドメイン情報を利用して、クラスター内のノードをユーザーに代わって正しく構成します。
次の図 (図 7) では、簡単な例として、1 つの障害ドメインに属すると見なせるエンティティすべてに色が付いています。また、結果として生成される複数の障害ドメインを一覧で示しています。 この例には、データセンター (DC)、ラック (R)、およびブレード (B) があります。 各ブレードが複数の仮想マシンを保持している場合は、障害ドメイン階層に別のレイヤーが存在することがあります。

![障害ドメインで構成されるノード][Image1]

 実行時に、Service Fabric クラスター リソース マネージャーは、クラスター内の障害ドメインを考慮に入れ、すべてが異なる障害ドメインに属するように、特定のサービスに対するレプリカを分散させようとします。 このプロセスにより、1 つの障害ドメイン (階層内の任意のレベル) で障害が発生したときに、そのサービスの可用性が損なわれることがありません。

 Service Fabric クラスター リソース マネージャーは、階層内に含まれるレイヤー数は考慮しませんが、階層の一部が失われたときに、それがクラスターまたはそのクラスターで実行されているサービスに影響を及ぼさないかどうかについては確認しようとします。そのため、一般的には、障害ドメインの各階層レベルのコンピューター数は同じにすることをお勧めします。 これにより、結果的に階層の一部が他よりも多くのサービスを保持しなければならない事態を避けることができます。

 障害ドメインの "ツリー" のバランスを考えずにクラスターを構成すると、特に、あるドメインの損失がクラスターの可用性に過度に影響を与えることがあるため、クラスター リソース マネージャーがレプリカの最適な割り当てを判断するのが難しくなります。クラスター リソース マネージャーは、その "重い" ドメインにあるコンピューターをそこにサービスを配置して効率的に使用するか、ドメインの損失による問題が発生しないようにサービスを配置するかで板挟みになります。

 次の図は、2 つの異なるクラスター レイアウトの例を示しています。一方のクラスターでは、ノードが障害ドメイン間で適切に分散されていますが、もう一方では 1 つの障害ドメインに片寄っています。  Azure では、どのノードが、どの障害ドメインとアップグレード ドメインに配置されるかは自動的に処理されるため、このような不均衡が表面に現れることはありません。 ただし、オンプレミスまたは別の環境で独自にクラスターを作成している場合は、検討が必要になります。

 ![Two different cluster layouts][Image2]

## <a name="upgrade-domains"></a>アップグレード ドメイン
アップグレード ドメインも、Service Fabric リソース マネージャーがクラスターのレイアウトを把握するための機能で、これにより、障害に対して早めに手を打つことができます。 アップグレード ドメインでは、アップグレード中に同時に停止する領域 (実際にはノード セット) が定義されます。

アップグレード ドメインは障害ドメインに似ていますが、重要な相違点がいくつかあります。 まず、アップグレード ドメインは、通常、ポリシーによって定義されますが、障害ドメインは、調整された障害領域 (通常は環境のハードウェア レイアウト) によって厳密に定義されます。 ただし、アップグレード ドメインの場合、必要な数を決める必要があります。 また、アップグレード ドメインは (少なくとも現時点では) 階層ではなく、単純なタグのようなものです。

次の図は、3 つの障害ドメインにわたってストライピングされた 3 つのアップグレード ドメインがある架空のセットアップを示しています。 また、ステートフル サービスの 3 つの異なるレプリカの配置例も示しています。 これらはすべて、異なる障害ドメインとアップグレード ドメインにあることに注意してください。 つまり、サービスのアップグレード中に障害ドメインを失うことがあっても、クラスター内では、コードとデータのコピーを実行しているものがまだ 1 つ存在します。 ニーズによってはこれでよいのですが、このコピーが古いことがあります (Service Fabric では、クォーラム ベースのレプリケーションを使用)。 2 つの障害を本当の意味で乗り切るには、さらに多くのレプリカ (5 つ以上) が必要です。

![Placement With Fault and Upgrade Domains][Image3]

アップグレード ドメインの数を増やすことは良くもあり、悪くもあります。良い点は、アップグレードの各手順が細かくなるため、影響を受けるノードやサービスの数が少なくなることです。 この結果、一度に移動しなければならないサービスが減り、システムに対する変更も少なくなります。また、(障害の影響を受けるサービスが少なくなるため) 全体的な信頼性が向上します。 アップグレード ドメインが多数存在する場合の欠点は、アップグレード時、Service Fabric が各アップグレード ドメインの正常性を検証し、正常な状態であることを確認してからでなければ次のアップグレード ドメインに進まない点です。 このチェックは、アップグレード処理を進める前に、サービスを安定させ、正常性を検証することを目的としています。これにより、すべての問題が検出されます。 正しくない変更が一度に多くのサービスに影響を及ぼすのを防ぐことができるため、このトレードオフは許容されています。

アップグレード ドメイン自身に副作用はほとんどありませんが、各アップグレード ドメインを停止してアップグレードしている間は、全体的な容量の大部分が使用できません。 たとえば、アップグレード ドメインが 3 つしかない場合は、サービスまたはクラスター容量全体の約 3 分の 1 が一度に停止します。 これは望ましくありません。残りのクラスターに、ワークロードを処理するための容量が必要になるためです。つまり、これらのノードにかかる負荷が低い通常状態と比べると、COGS が高くなります。

環境内の障害ドメインまたはアップグレード ドメインの合計数に制限はありません。また、重複の制約もありません。 これまで見てきた一般的な構造は、1 対 1 (一意の各障害ドメインがそれぞれ専用のアップグレード ドメインにマップされる)、1 ノードに 1 つのアップグレード ドメイン (物理または仮想 OS インスタンス)、"ストライプ" または "マトリックス" モデル (障害ドメインとアップグレード ドメインがマトリックスを形成。通常はコンピューターが表中で対角線を描くように配置される) です。

![Fault and Upgrade Domain Layouts][Image4]

それぞれのレイアウトに長所と短所があり、どれがベストであるかを断言することはできません。 たとえば、1FD 対 1UD モデルは設定が簡単ですが、1 UD/ノード モデルは、これまで少ないコンピューターを管理してきたユーザーが慣れているのに似たモデルで、各コンピューターが個別に停止します。

最も一般的なモデル (および、ホストされた Azure Service Fabric クラスターに使用されているモデル) は、FD/UD マトリックスです。このモデルでは、FD と UD がテーブルを形成し、ノードが対角線に沿って配置されます。 ノードが分散するか密集するかは、FD と UD の数に対するノードの合計数によって決まります (つまり、クラスターが十分に大きい場合は、図 10 の右下のオプションに示すように、ほぼすべてが高密度マトリックス パターンのようになります)。

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>障害ドメインとアップグレード ドメインの制約および結果の動作
クラスター リソース マネージャーは、障害ドメインとアップグレード ドメイン全体にサービスを分散させたいという希望を制約として処理します。 制約について詳しくは、 [この記事](service-fabric-cluster-resource-manager-management-integration.md)を参照してください。 障害ドメイン (FD) とアップグレード ドメイン (UD) の制約は次のように定義されます: "特定のサービス パーティションについて、2 つのドメインでのレプリカ数の差が "*1 よりも大きく*" なってはならない"。  実質的に意味することは、特定のサービスについて、この状態になると障害またはアップグレード ドメインの制約を違反するため、特定の移動または配置がクラスターで無効になる可能性があるということです。

1 つ例を見てみましょう。 たとえば、6 つのノードを持つクラスターを、5 つの障害ドメインと 5 つのアップグレード ドメインで構成したととします。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| UD0 |N1 | | | | |
| UD1 |N6 |N2 | | | |
| UD2 | | |N3 | | |
| UD3 | | | |N4 | |
| UD4 | | | | |N5 |

ここで、TargetReplicaSetSize に 5 を指定してサービスを作成します。 レプリカを N1 ～ N5 に配置します。 実際には N6 は使用されません。 しかし、なぜでしょうか。 それでは、現在のレイアウトと、N6 を代わりに選択した場合の動作の違いを見て、これが FD および UD の制約のこの定義にどのように関連するかについて考えてみましょう。

以下にそのレイアウトと、障害およびアップグレード ドメインあたりのレプリカの合計数を示します。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 |R1 | | | | |1 |
| UD1 | |R2 | | | |1 |
| UD2 | | |R3 | | |1 |
| UD3 | | | |R4 | |1 |
| UD4 | | | | |R5 |1 |
| FDTotal |1 |1 |1 |1 |1 |- |

このレイアウトは障害ドメインとアップグレード ドメインあたりのノードという点でバランスが取れており、障害ドメインとアップグレード ドメインあたりのレプリカという点でもバランスが取れています。 各ドメインのノード数、レプリカ数は同じです。

ここで、N2 ではなく N6 を使用した場合どうなるか見てみましょう。 レプリカはどのように分散されるでしょうか。 これは、次のようになります。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 |R1 | | | | |1 |
| UD1 |R5 | | | | |1 |
| UD2 | | |R2 | | |1 |
| UD3 | | | |R3 | |1 |
| UD4 | | | | |R4 |1 |
| FDTotal |2 |0 |1 |1 |1 |- |

これは、障害ドメインの制約の定義に違反しています。FD0 のレプリカ数は 2 つですが、FD1 は 0 で、差の合計が 2 であるため、クラスター リソース マネージャーでこの配置が許容されないためです。 同様に、N2 ～ 6 を選んだ場合、次のようになります。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 | | | | | |0 |
| UD1 |R5 |R1 | | | |2 |
| UD2 | | |R2 | | |1 |
| UD3 | | | |R3 | |1 |
| UD4 | | | | |R4 |1 |
| FDTotal |1 |1 |1 |1 |1 |- |

ここでは、障害ドメインの点でバランスが取れていますが、アップグレード ドメインの制約に違反しており (UD0 のレプリカ数が 0 で、UD1 のレプリカ数は 2 であるため)、したがってこのレイアウトは無効です。

## <a name="configuring-fault-and-upgrade-domains"></a>障害ドメインとアップグレード ドメインの構成
障害ドメインとアップグレード ドメインは、Azure でホストされる Service Fabric デプロイで自動的に定義されます。Service Fabric は、Azure から環境情報を取得するだけです。 Azure では、障害ドメインとアップグレード ドメインの両方の情報が "1 つのレベル" のように見えますが、実際には Azure Stack の下位レイヤーの情報をカプセル化して、論理的な障害ドメインとアップグレード ドメインをユーザーの観点から表示しているにすぎません。

自分でクラスターを作成している場合 (または、開発用コンピューターで特定のトポロジを実行しようとしている場合)、障害ドメインとアップグレード ドメインの情報も自分で提供する必要があります。 この例では、3 つの "データセンター" (それぞれが 3 つのラックを装備) にまたがる 9 個のノード ローカル開発クラスターと、その 3 つのデータセンターにわたってストライピングされた 3 つのアップグレード ドメインを定義しています。 クラスター マニフェスト テンプレートでは、次のようになります。

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [!NOTE]
> Azure デプロイメントでは、Azure が障害ドメインとアップグレード ドメインを割り当てます。 そのため、Azure のインフラストラクチャ オプション内のノードとロールの定義に、障害ドメインまたはアップグレード ドメインに関する情報は含まれません。
> 
> 

## <a name="placement-constraints-and-node-properties"></a>配置の制約とノードのプロパティ
場合によっては (実際には、ほとんどの場合)、特定のワークロードが、クラスター内の特定のノードまたは特定のノード セットだけで確実に実行されるようにしたいことがあります。 たとえば、ワークロードの中に GPU や SSD を必要とするものとしないものが混在している場合があります。 その良い例がほぼすべての n 層アーキテクチャです。このアーキテクチャでは、アプリケーションのフロント エンド/インターフェイス提供側として機能するコンピューターが存在する一方で (したがっておそらくインターネットに対して公開)、(さまざまなハードウェア リソースを備えた) 別のセットが、コンピューティングやストレージ レイヤーの作業を処理します (通常、インターネットに公開されない)。 Service Fabric では、マイクロサービスであっても、特定のワークロードを特定のハードウェア構成で実行する必要が生じる状況があると想定されています。たとえば、次のような状況です。

* 既存の n 層アプリケーションが Service Fabric 環境に "リフト アンド シフト (移行)" された。
* パフォーマンス、スケール、またはセキュリティの分離の理由により、ワークロードを特定のハードウェアで実行する必要がある。
* ポリシーまたはリソース消費の理由により、特定のワークロードを他のワークロードから切り離す必要がある。

こうした構成をサポートするために、Service Fabric には、配置の制約と呼ばれる優れた概念があります。 配置の制約を使用すると、特定のサービスを実行する場所を指定できます。 制約のセットはユーザーが拡張できます。つまり、ユーザーがカスタム プロパティでノードにタグを付けて、それに基づいて選択できます。

![Cluster Layout Different Workloads][Image5]

ノード上の各種キー/値タグはノードの配置 "*プロパティ*" (またはノード プロパティ) と呼ばれ、サービスのステートメントは配置の "*制約*" と呼ばれます。 ノード プロパティで指定する値には、string、bool、signed long を使用できます。 制約として使用できるのは、クラスター内の別のノード プロパティに対して実行される任意のブール ステートメントです。 これらの (文字列である) ブール ステートメントでの有効なセレクターは次のとおりです。

* 特定のステートメントを作成するための条件確認
  * "等しい" ==
  * "より大きい" >
  * "より小さい" <
  * "等しくない" !=
  * "以上" >=
  * "以下" <=
* グループ化と否定のブール ステートメント
  * "および" &&
  * "または" ||
  * "否定" !
* グループ化操作のかっこ
  
  * ()
  
  上の記号の一部を使用する基本的な制約ステートメントの例をいくつか紹介します。 ノードのプロパティとして使用できるのは、文字列、ブール、または数値であることに注意してください。   
  
  * "Foo >= 5"
  * "NodeColor != green"
  * "((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"

ステートメント全体の評価が "True" のノードのみ、サービスを配置させることができます。 プロパティが定義されていないノードと、そのプロパティを含む配置の制約とは一致しません。

Service Fabric では、自動的に適用できる既定のプロパティもいくつか定義されています。これらのプロパティについては、ユーザーが定義する必要はありません。 この記事の執筆時点では、NodeType と NodeName が、既定のプロパティとして各ノードで定義されています。 したがって、たとえば、配置の制約を "(NodeType == NodeType03)" と記述できます。 一般的には、NodeType プロパティが最もよく使用されるプロパティの 1 つです。このプロパティは、通常、コンピューターの種類と 1 対 1 で対応しているためです。従来の n 層アプリケーション アーキテクチャでは、コンピューターの種類はワークロードの種類に対応しています。

![配置の制約とノードのプロパティ][Image6]

たとえば、特定のノード タイプに対して次のノード プロパティが定義されているとします: ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

サービスに対して、次のようにサービスの配置の制約を作成できます。

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

NodeType01 のすべてのノードが確実に有効である場合は、上の図のような配置の制約を使用すれば、そのノード タイプを選択するだけで済みます。

サービスの配置の制約で優れているのは、実行時に動的に更新される点です。 そのため、必要に応じて、クラスター内でのサービスの移動や、要件の追加/削除などを行うことができます。このような変更が進行中でも、Service Fabric によって、サービスは確実に稼働し続け、利用することができます。

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

配置の制約 (および、これから説明するその他多数のオーケストレーター コントロール) は、すべてのサービス インスタンスに対してそれぞれ指定されます。 更新すると、以前に指定された内容は常に置き換えられます (上書きされます)。

また、この時点でノードのプロパティはクラスター定義を介して定義されるため、これらのプロパティを更新するにはクラスターをアップグレードする必要があり、各ノードを停止させてから復帰させてそのプロパティを更新する必要があることにも注意してください。

## <a name="capacity"></a>容量
クラスター内のリソース消費量の管理は、オーケストレーターの重要なジョブの 1 つです。 サービスを効率的に実行しようとしている場合に最も望ましくないのは、(リソース競合やパフォーマンスの低下につながる) 大量のホット ノードと、(リソースが浪費される) コールド ノードが混在することです。 しかし、バランスよりもっと基本的なことを考えてみましょう (バランスについては後ほど説明します)。そもそも、ノードでリソースが不足しないようにさえすればよいのではないでしょうか。

Service Fabric はリソースを "メトリック" として表します。 メトリックとは、Service Fabric に対して記述する論理または物理リソースです。 たとえば、"WorkQueueDepth"、"MemoryInMb" などがメトリックです。 メトリックは、配置の制約やノードのプロパティとは異なります。ノード プロパティは一般的にノード自体の静的な記述子ですが、メトリックは、ノードが所有し、サービスがノードで実行されているときに消費されるリソースに関するものです。 つまり、プロパティは HasSSD (SSD 有り) のようなもので、true または false に設定できますが、その SSD で使用できる領域の量 (およびサービスによって消費される量) は、"DriveSpaceInMb" などのメトリックで表します。 "DriveSpaceInMb" は、ノードの容量に基づいて、ドライブ上で予約されていない領域の合計量に設定されます。実行時にサービスが使用したメトリックの量は、そのサービスによってレポートされます。

すべてのリソース "*分散*" をオフにしても、Service Fabric のクラスター リソース マネージャーは、容量超過のノードが出ないように調整できます (クラスター全体に余裕がある限り)。 容量とは、ノードにどのくらいのリソースがあるかを理解するためにクラスター リソース マネージャーが使用するもう 1 つの "*制約*" です。 このサービス レベルの容量と消費量の両方が、メトリックで表現されます。 たとえば、メトリックが "MemoryInMb" の場合 - 指定したノードに MemoryInMb が 2048 の容量があり、指定したサービスは現在 64 の MemoryInMb を消費していると宣言できます。

実行時、クラスター リソース マネージャーは、(容量によって定義される) 各ノードに存在する各リソースの量と、(宣言された使用量を各サービスから差し引くことで算出される) リソース残量をトラックします。 Service Fabric リソース マネージャーは、ノードが容量超過にならないように、この情報を使用して、レプリカを配置または移動する場所を検討します。

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```

![Cluster nodes and capacity][Image7]

これは、クラスター マニフェストで確認できます。

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

サービスの負荷が動的に変わる可能性もあります。 レプリカの負荷が 64 から 1024 に変更されたが、それをその時点で実行しているノードの残りの容量が 512 ("MemoryInMb" メトリック) だったとします。 このため、ノード上のすべてのレプリカとインスタンスの合計使用量が、そのノードの容量を超えているために、レプリカまたはインスタンスが現在配置されている場所が無効になります。 負荷が動的に変化するこのシナリオについては後で説明しますが、容量に限って言えば、同じように処理されます。つまり、クラスター リソース マネージャーが自動的に起動し、ノード上の 1 つ以上のレプリカまたはインスタンスを別のノードに移動して、そのノードを容量未満に戻します。 これを行うとき、クラスター リソース マネージャーは、すべての移動のコストを最小化しようとします (コストの概念については後ほど触れます)。

## <a name="cluster-capacity"></a>クラスターの容量
では、クラスター全体がいっぱいにならないようにするにはどうすればよいのでしょうか。 実際のところ、負荷が動的な場合、できることは多くありません (サービスにかかる負荷は、クラスター リソース マネージャーのアクションに関係なく急激に高くなる場合があるためです。今日クラスターに余裕があったとしても、明日一躍有名になったらエネルギー不足に陥る可能性があります)。しかし、基本的な問題を回避するために制御できることはいくつかあります。 まず、クラスターがいっぱいになる原因となるワークロードが新しく作成されないようにします。

たとえば、シンプルなステートレス サービスを作成するとします。このサービスには負荷が関連付けられています (既定および動的な負荷レポートの詳細については後述)。 このサービスは、あるリソースを使用します。ここでは DiskSpace リソースとしましょう。サービスは既定でサービス インスタンスごとに 5 ユニットの DiskSpace を消費します。 3 つのサービス インスタンスを作成する必要があるとしましょう。 そこで、 つまり、これらのサービス インスタンスを作成するには、クラスターに 15 ユニットの DiskSpace が必要です。 Service Fabric は、全体的な容量と各メトリックの消費量を継続的に計算しているため、判断に手間はかかりません。空き領域が足りない場合は、作成サービス呼び出しを拒否できます。

要件は 15 ユニットが使用可能である、ということだけです。したがって、この領域はさまざまな方法で割り当てることができます。たとえば、15 ノードそれぞれに 1 つずつユニットが残っていても、5 ノードに 3 つずつユニットが残っていてもかまいません。3 つのノードに十分な容量がない場合、Service Fabric はクラスター内の既存のサービスを再構成して、その 3 つの必要なノードで領域を確保します。 クラスター全体がほぼいっぱいでない限り、ほとんどの場合、このような再配置が可能です。

## <a name="buffered-capacity"></a>バッファーの容量
また、ユーザーがクラスター全体の容量を管理しやすいように、予約済みのバッファーの概念が、各ノードで指定された容量に用意されています。 この設定はオプションですが、これによりユーザーがノード全体の容量の一部を予約できます。予約した容量は、アップグレード中および障害発生中、つまり、その容量を使わないとクラスター容量が削減される場合に、サービスの配置でのみ使用されます。 現時点では、バッファーは、すべてのノードを対象にグローバルに、ClusterManifest を介してメトリックごとに指定します。 予約容量に対して指定する値は、サービスに対する制約が大きいリソースの関数と、クラスター内の障害ドメインとアップグレード ドメインの数です。 一般的に、障害ドメインとアップグレード ドメイン数が多い場合は、バッファー容量に小さな数値を選択できます。アップグレード中や障害発生中に使用できなくなるクラスターの量が少なくなるためです。 バッファーの割合の指定は、メトリックに対してノード容量を指定した場合にのみ有効です。

バッファー容量を指定する方法の例を次に示します。

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

クラスターにバッファー容量がなくなると、新しいサービスの作成が失敗します。これにより、クラスターで予備のオーバーヘッドが確実に確保され、アップグレードや障害でノードの容量超過が実際に発生することがなくなります。 クラスター リソース マネージャーでは、PowerShell やクエリ API を使用して、こうした情報が多数公開されており、クラスターで使用中の各メトリックのバッファー容量の設定、合計容量、および現在の消費量を確認することができます。 その出力の例を次に示します。

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>次のステップ
* クラスター リソース マネージャーのアーキテクチャと情報フローについては、 [この記事 ](service-fabric-cluster-resource-manager-architecture.md)
* 最適化メトリックの定義は、負荷を分散するのではなく、ノードで統合する方法の 1 つです。 最適化を構成する方法については、 [この記事](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png



<!--HONumber=Nov16_HO3-->


