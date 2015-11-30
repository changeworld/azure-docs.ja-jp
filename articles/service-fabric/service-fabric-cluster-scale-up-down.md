<properties
   pageTitle="Service Fabric クラスターのスケールアップとスケールダウン | Microsoft Azure"
   description="仮想マシン ノードを追加または削除することで、Service Fabric クラスターを需要に合わせてスケールアップまたはスケールダウンします。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# 仮想マシン (VM) の追加または削除による Service Fabric クラスターのスケールアップとスケールダウン

仮想マシンを追加または削除することで、Service Fabric クラスターを需要に合わせてスケールアップまたはスケールダウンできます。

>[AZURE.NOTE]サブスクリプションに、このクラスターを構成する新しい VM を追加できるだけのコア数が割り当てられていることが前提となります。


## Service Fabric クラスターの手動でのスケーリング

### スケーリングするノードの種類の選択

クラスターに複数のノードの種類がある場合は、特定のノードの種類に対して VM を追加または削除する必要があります。

1. Azure ポータル ([http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)) にログオンします。

2. [Service Fabric クラスター] に移動します。![BrowseServiceFabricClusterResource][BrowseServiceFabricClusterResource]

3. スケールアップまたはスケールダウンするクラスターを選択します。

4. クラスター ダッシュボードの設定ブレードに移動します。設定ブレードが表示されていない場合は、クラスター ダッシュボードの主要部分にある [すべての設定] をクリックします。

5. [NodeTypes] をクリックして、NodeTypes リスト ブレードを開きます。

7. スケールアップまたはスケールダウンするノードの種類をクリックして、ノードの種類の詳細ブレードを開きます。

### ノードの追加によるスケールアップ

VM の数を必要な数に調整し、保存します。デプロイメントが完了すると、ノード/VM が追加されます。

### ノードの削除によるスケールダウン

ノードの削除は 2 段階のプロセスです。

1. VM の数を目的の数に調整し、保存します。スライダーの下限は、その NodeType の VM の最小要件を示します。

  >[AZURE.NOTE]主要なノードの種類では、最低 5 つの VM を維持する必要があります。

	Once that deployment is complete, you will get notified of the VM names that can now be deleted. You now need to navigate to the VM resource and delete it.

2. クラスター ダッシュボードに戻り、[リソース グループ] をクリックします。[リソース グループ] ブレードが開きます。

3. [概要] を確認するか、[...] をクリックしてリソースの一覧を表示します。

4. 削除する VM の名前をクリックします (削除可能な VM の名前が表示されています)。

5. [削除] アイコンをクリックして、VM を削除します。

## Service Fabric クラスターの自動スケーリング

現時点では、Service Fabric クラスターは自動スケールをサポートしていません。近い将来、クラスターは仮想マシン スケール セット (VMSS) の上に構築されるようになります。その時点で自動スケールが可能になり、Cloud Services での自動スケーリングと同様に動作するようになります。

## PowerShell または CLI を使用したスケーリング

この記事では、ポータルを使用したクラスターのスケーリングを取り上げましたが、クラスター リソースで ARM コマンドを使用して、コマンド ラインから同じ操作を実行することもできます。ClusterResource の GET 応答では、無効になっているノードのリストが返されます。

## 次のステップ

- [クラスターのアップグレードの詳細](service-fabric-cluster-upgrade.md)
- [最大のスケールに対応するためのステートフル サービスのパーティション分割の詳細](service-fabric-concepts-partitioning.md)


<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Nov15_HO4-->