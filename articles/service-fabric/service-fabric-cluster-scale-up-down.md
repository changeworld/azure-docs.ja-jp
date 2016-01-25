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
   ms.date="01/11/2016"
   ms.author="chackdan"/>

# 仮想マシン (VM) の追加または削除による Service Fabric クラスターのスケールアップとスケールダウン

仮想マシンを追加または削除することで、Service Fabric クラスターを需要に合わせてスケールアップまたはスケールダウンできます。

>[AZURE.NOTE]サブスクリプションに、このクラスターを構成する新しい VM を追加できるだけのコア数が割り当てられていることが前提となります。


## Service Fabric クラスターの手動でのスケーリング

クラスターに複数のノードの種類がある場合は、特定のノードの種類に対して VM を追加または削除する必要があります。同じデプロイ内で、あるノードの種類に VM を追加し、別のノードの種類から VM を削除することができます。

### ポータルを使用してデプロイしたクラスターのアップグレード

このプロセスは複雑なため、Microsoft が Git リポジトリにアップロードした PowerShell モジュールを使用してください。

**手順 1**. [Git リポジトリ](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)からこのフォルダーをコンピューターにコピーします。

**手順 2**. Azure SDK 1.0 以降がコンピューターにインストールされていることを確認します。

**手順 3**. PowerShell ウィンドウを開き、ServiceFabricRPHelpers.psm をインポートします。

```
Remove-Module ServiceFabricRPHelpers
```

コピーした Poweshell モジュールをインポートします。次のコマンドをコピーし、.psm1 のパスを、ご使用のコンピューターのパスに変更するだけです。

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

 **手順 4**. Azure アカウントにログインします。

```
Login-AzureRmAccount
```

Invoke-ServiceFabricRPClusterScaleUpgrade コマンドを実行し、リソース グループ名とサブスクリプションが正しいことを確認します。

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName <string> -SubscriptionId <string>
```

同じ PS コマンドに入力した例は次のとおりです。

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName chackod02 -SubscriptionId 18ad2z84-84fa-4798-ad71-e70c07af879f
```

  **手順 5**. このコマンドで、クラスター情報を取得して、ノードの種類すべてを調べます。これにより、最初に該当ノードの種類の現在の VM またはノードの数が示された後、新しい VM またはノードの数を指定するように求められます。

 **このノードの種類をスケールアップするには**、現在の VM 数より大きい数値を指定します。

**このノードの種類をスケールダウンするには**、現在の VM 数より小さい数を指定します。

これらのプロンプトでは、ノードの種類すべてに対してループ処理します。クラスターのノードの種類が 1 つだけの場合、プロンプトが表示されるのは 1 回のみです。
 
  **手順 6**. 新しい VM を追加する場合は、追加する VM のリモート デスクトップ ユーザー ID とパスワードを入力するように求めるメッセージが表示されます。
 
**手順 7**. 出力ウィンドウに、デプロイの進行状況を通知するメッセージが表示されます。デプロイが完了すると、クラスターには、手順 5. で指定した数のノードが存在します。


![ScaleupDownPSOut][ScaleupDownPSOut]


**手順 8**. これがスケールダウンの要求だった場合は、VM を削除する手順がもう 1 つあります。スクリプトは、削除するように要求したすべての VM を非アクティブ化します。つまり、これらのノードまたは VM に、アプリケーションまたはシステムのレプリカはありません。これらの VM を安全に削除できるようになりました。

**注**: 非アクティブ化されたノードは、今後は SF クラスターによって使用されなくなりますが、課金されることがないように、非アクティブ化された仮想マシンを削除する必要があります。

**手順 8.1**. Azure ポータル ([http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)) にログオンします。

**手順 8.2**. [要点] Web パーツで [すべての設定] をクリックして、スケールダウンしたクラスター リソースを参照します。

**手順 8.3**. [ノードの種類] をクリックすると、非アクティブ化されたノードの一覧が表示されます。この図にある chackodnt15、chackodnt24、chackodnt25、chackodnt26 は、現時点で削除する必要がある VM です。

![DeactivatedNodeList][DeactivatedNodeList]

**ステップ 8.4**. PS またはポータルを使用してこれらの VM を削除します。VM が削除されると、クラスターのスケールダウンが完了します。

```
Remove-AzureRmResource -ResourceName <Deactivated Node name without the understore> -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName <Resource Group name> -ApiVersion <Api version>
```
そのコマンドの入力例は次のとおりです。

```
Remove-AzureRmResource -ResourceName chackodnt26 -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName chackod02 -ApiVersion 2015-05-01-preview
```

### ARM PowerShell または CLI を使用してデプロイしたクラスターのアップグレード

ARM テンプレートを使用してクラスターを最初にデプロイした場合は、特定のノードの種類の VM の数と VM をサポートしているすべてのリソースを変更する必要があります。プライマリ ノードの種類に許可された VM の最小数は 5 (非テスト クラスター用)、テスト クラスターの最小値は 3 です。

新しいテンプレートを作成したら、アップグレードするクラスターと同じリソース グループを使用して、そのテンプレートを ARM にデプロイできます。


## Service Fabric クラスターの自動スケーリング

現時点では、Service Fabric クラスターは自動スケールをサポートしていません。近い将来、クラスターは仮想マシン スケール セット (VMSS) の上に構築されるようになります。その時点で自動スケールが可能になり、Cloud Services での自動スケーリングと同様に動作するようになります。


## 次のステップ

- [クラスターのアップグレードの詳細](service-fabric-cluster-upgrade.md)
- [最大のスケールに対応するためのステートフル サービスのパーティション分割の詳細](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ScaleupDownPSOut]: ./media/service-fabric-cluster-scale-up-down/ScaleupDownPSOut.png
[DeactivatedNodeList]: ./media/service-fabric-cluster-scale-up-down/DeactivatedNodeList.png

<!---HONumber=AcomDC_0114_2016-->