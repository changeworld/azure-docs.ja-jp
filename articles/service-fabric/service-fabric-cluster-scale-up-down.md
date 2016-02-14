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
   ms.date="01/29/2016"
   ms.author="chackdan"/>


# 仮想マシンの追加または削除による Service Fabric クラスターのスケールアップとスケールダウン

仮想マシンを追加または削除することで、Azure Service Fabric クラスターを需要に合わせてスケールアップまたはスケールダウンできます。

>[AZURE.NOTE] サブスクリプションに、このクラスターを構成する新しい VM を追加できるだけのコア数が割り当てられている必要があります。

## Service Fabric クラスターを手動でスケールする

### スケールするノードの種類を選択する

クラスターに複数のノードの種類がある場合は、特定のノードの種類に対して VM を追加または削除する必要があります。その方法は次のとおりです。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

2. **[Service Fabric クラスター]** に移動します。![Azure ポータルの [Service Fabric クラスター] ページ。][BrowseServiceFabricClusterResource]

3. スケールアップまたはスケールダウンするクラスターを選択します。

4. クラスター ダッシュボードの **[設定]** ブレードに移動します。**[設定]** ブレードが表示されていない場合は、クラスター ダッシュボードの主要部分にある **[すべての設定]** をクリックします。

5. **[NodeTypes]** をクリックして、**[NodeTypes リスト]** ブレードを開きます。

6. スケールアップまたはスケールダウンするノードのタイプをクリックして、**[NodeType の詳細]** ブレードを開きます。

### ノードの追加によるスケールアップ

VM 数を目的の数に調整して保存します。デプロイメントが完了すると、ノード/VM は追加されます。

### ノードの削除によるスケールダウン

ノードの削除は 2 段階のプロセスです。

1. 目的の VM 数に調整して保存します。スライダーの左端には、そのノードのタイプに必要な最小限の VM 数が表示されます。

    >[AZURE.NOTE] プライマリ ノードのタイプの場合、5 個以上の VM を維持する必要があります。

2. デプロイメントが完了すると、削除対象の VM 名が通知されます。次に、VM リソースに移動して削除する必要があります。

    a.クラスター ダッシュボードに戻り、**[リソース グループ]** をクリックします。**[リソース グループ]** ブレードが開きます。

    b.**[概要]** を確認するか、**[...]** をクリックしてリソースの一覧を表示します。

    c.削除対象として示された VM 名をクリックします。

    d.**[削除]** アイコンをクリックして、VM を削除します。

## Service Fabric クラスターの自動スケール

現時点では、Service Fabric クラスターは自動スケールをサポートしていません。近い将来、クラスターは仮想マシン スケール セットの上に構築されるようになります。その時点で自動スケールが可能になり、Cloud Services での自動スケーリングと同様に動作するようになります。

## PowerShell または CLI を使用したクラスターのスケール

この記事では、ポータルを使用したクラスターのスケーリングを取り上げましたが、クラスター リソースで Azure リソース マネージャー コマンドを使用して、コマンド ラインから同じ操作を実行することもできます。クラスター リソースの GET 応答では、無効になっているノードのリストが返されます。

## 次のステップ

- [クラスターのアップグレードの詳細](service-fabric-cluster-upgrade.md)
- [最大のスケールに対応するためのステートフル サービスのパーティション分割の詳細](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=AcomDC_0204_2016-->