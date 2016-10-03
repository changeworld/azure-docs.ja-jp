<properties
	pageTitle="仮想マシン スケール セットへのアプリケーションのデプロイ| Microsoft Azure"
	description="仮想マシン スケール セットへのアプリケーションのデプロイ"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="guybo"/>


# 仮想マシン スケール セットをアップグレードする

この記事では、ダウンタイムなしで Azure VM スケール セットに OS の更新を展開する方法について説明します。ここでは、OS の更新により、OS のバージョンまたは SKU、またはカスタム イメージの URI が変更されます。ダウンタイムのない更新とは、すべての VM を 1 度に更新するのではなく、1 度に 1 つずつ、またはグループごとに更新する (1 度に 1 つの障害ドメインなど) ということです。そうすることで、アップグレード中ではない VM を実行し続けることができます。

あいまいにならないように、3 種類の OS の更新を区別してみましょう。

1. プラットフォーム イメージのバージョンまたは SKU を変更します。たとえば、Ubuntu 14.04.2-LTS バージョンを 14.04.201506100 から 14.04.201507060 に変更、または Ubuntu 15.10/最新 SKU を 16.04.0-LTS/最新に変更します。この記事では、このシナリオを説明します。

2. 新しいバージョンのカスタム イメージを構築し、イメージをポイントする URI を変更します (properties->virtualMachineProfile->storageProfile->osDisk->image->uri)。この記事では、このシナリオを説明します。

3. VM 内から OS を修正します (例: Windows Update を使用してセキュリティ パッチをインストールする)。このシナリオはサポートされますが、この記事では説明されていません。

最初の 2 つは、サポートされる要件です。3 つ目は、少なくとも現時点では、新しいスケール セットを作成する必要があります。この記事では、オプション 1 および 2 について説明します。注: [Service Fabric](https://azure.microsoft.com/services/service-fabric/) クラスターの一部としてデプロイされる VM スケール セットについては、ここでは説明されません。

プラットフォーム イメージの OS バージョンまたは SKU、もしくはカスタム イメージの URI を変更するための基本的なシーケンスは次のようになります。

* VMSS モデルを取得します。

* モデル内のバージョン、SKU、または URI 値を変更します。

* モデルを更新します。

* スケール セットの VM で manualUpgrade の呼び出しを実行します。この手順は、スケール セットの upgradePolicy プロパティが [手動] に設定されている場合にのみ有効です。[自動] に設定されている場合は、すべての VM がすぐにアップグレードされ、ダウンタイムが発生します。


この予備知識を持ってから、REST API を使用して PowerShell のスケール セットのバージョンを更新する方法を確認しましょう。これらの例ではプラットフォーム イメージを取り上げます。このプロセスをカスタム イメージに適用する十分な情報については、すでに提供してきました。

## PowerShell

この例では、Windows VM スケール セットを新しいバージョン ”4.0.20160229” に更新します。モデル更新後に、1 つの VM インスタンスを 1 度に更新します。

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the VMSS model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

プラットフォーム イメージのバージョン変更の代わりにカスタム イメージの URI を更新した場合は、次のように “set the new version” 行を置き換えます。

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## REST API を使用

次に、Azure REST API を使用して OS バージョンの更新を展開する Python の例を示します。この 2 つの例では、スケール セット モデル上で GET を実行するための Azure REST API ラッパー関数の lightweight [azurerm](https://pypi.python.org/pypi/azurerm) ライブラリを使用し、更新されたモデルで PUT を行います。更新ドメインごとに、VM を識別するための VM インスタンスのビューを確認できます。

### vmssupgrade

vmssupgrade は実行中の VM スケール セットに OS のアップグレードを展開する Python スクリプトで、1 度に 1 つの更新ドメインを更新します。詳細については[こちら](https://github.com/gbowerman/vmsstools)を参照してください。

![vmssupgrade スクリーン ショット](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

このスクリプトを使用すると、更新する特定の VM の選択、または更新ドメインの指定ができます。そして、プラットフォーム イメージのバージョンの変更、またはカスタム イメージの URI の変更をサポートします。

### vmsseditor

このツールは、1 つの行が 1 つの更新ドメインを表すヒートマップとしての VM の状態を表示する VM スケール セットのための汎用エディターです。特に、新しいバージョン、SKU、またはカスタム イメージ URI で VMSS のモデルを更新し、アップグレードする障害ドメインを選択できます。この操作を行う場合、更新ドメイン内のすべての VM が新しいモデルにアップグレードされます。または、好みのバッチ サイズに基づいてローリング アップグレードを行うことができます。vmsseditor は次の [github リポジトリ](https://github.com/gbowerman/vmssdashboard)にあります。

たとえば、ここでスケール セットのモデルを Ubuntu 14.04 2LTS 14.04.201507060 のバージョンに更新しました。このスクリーン ショットは古いものです。このツールには多くのオプションが追加されています。

![vmsseditor スクリーン ショット 1](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

[Upgrade (アップグレード)] をクリックして、再度 [Get Details (詳細の取得)] をクリックすると、UD 0 内の VM の更新が開始されます。

![vmsseditor スクリーン ショット 2](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

<!---HONumber=AcomDC_0921_2016-->