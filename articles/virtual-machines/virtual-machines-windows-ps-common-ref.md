<properties 
   pageTitle="VM 用の一般的な PowerShell のコマンド | Microsoft Azure"
   description="Windows 上で Azure の VM の作成および管理を開始する際に使用される一般的な PowerShell コマンド"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="davidmu" />

# VM の作成および管理に使用される一般的な PowerShell コマンド

この記事では、Azure サブスクリプション内の仮想マシンの作成および管理に使用できる Azure PowerShell コマンドの一部について説明します。具体的なコマンド ライン スイッチやオプションの詳細については、**Get-Help** "コマンド" を使用します。

## Azure PowerShell を使用してリソースを作成する

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択して、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

タスク | コマンド
-------------- | -------------------------
VM 構成の作成 | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "VM 名" -VMSize "VM のサイズ"<BR></BR><BR></BR>VM 構成は、仮想マシンの設定の定義または更新に使用します。この構成は、VM の名前とその[サイズ](virtual-machines-windows-sizes.md)で初期化します。
構成設定の追加 | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "コンピューター名" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>[資格情報](https://technet.microsoft.com/library/hh849815.aspx)を含むオペレーティング システムの設定は、New-AzureRmVMConfig を使用してあらかじめ作成した構成オブジェクトに追加されます。
ネットワーク インターフェイスの追加 | $vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM には、仮想ネットワークと通信するための[ネットワーク インターフェイス](virtual-machines-windows-ps-create.md)が必要です。[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) を使用して既存のネットワーク インターフェイス オブジェクトを取得することもできます。
プラットフォーム イメージの指定 | $vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "パブリッシャー名" -Offer "パブリッシャー プラン" -Skus "製品 SKU" -Version "最新"<BR></BR><BR></BR>[イメージ情報](virtual-machines-windows-cli-ps-findimage.md)が、New-AzureRmVMConfig を使用してあらかじめ作成した構成オブジェクトに追加されます。このコマンドから返されるオブジェクトは、プラットフォーム イメージを使用するために OS ディスクを設定する場合にのみ使用されます。
プラットフォーム イメージを使用するように OS ディスクを設定 | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "ディスク名" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>オペレーティング システム ディスクの名前と[ストレージ](../storage/storage-powershell-guide-full.md)内での位置が、あらかじめ作成した構成オブジェクトに追加されます。
一般化されたイメージを使用するように OS ディスクを設定 | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "ディスク名" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>オペレーティング システム ディスクの名前、ソース イメージの場所、ディスクを追加する[ストレージ](../storage/storage-powershell-guide-full.md)内の場所が、あらかじめ作成した構成オブジェクトに追加されます。
特殊なイメージを使用するように OS ディスクを設定 | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "ディスク名" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows
VM を作成します | [New-AzureRmVM]() -ResourceGroupName "リソース グループ名" -Location "場所の名前" -VM $vm<BR></BR><BR></BR>すべてのリソースが[リソース グループ](../powershell-azure-resource-manager.md)内に作成されます。VM は、リソース グループと同じ[場所](https://msdn.microsoft.com/library/azure/dn495177.aspx)に作成する必要があります。このコマンドを実行する前に、New-AzureRmVMConfig、Set-AzureRmVMOperatingSystem、Set-AzureRmVMSourceImage、Add-AzureRmVMNetworkInterface、および Set-AzureRmVMOSDisk を実行します。
サブスクリプション内の VM の一覧表示| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
リソース グループ内の VM の一覧表示 | Get-AzureRmVM -ResourceGroupName "リソース グループ名"<BR></BR><BR></BR>サブスクリプション内のリソース グループの一覧を取得するには、[Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx) を使用します。
VM に関する情報の取得 | Get-AzureRmVM -ResourceGroupName "リソース グループ名" -Name "VM 名"
VM の起動 | [Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "リソース グループ名" -Name "VM 名"
VM の停止 | [Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "リソース グループ名" -Name "VM 名"
実行中の VM の再起動 | [Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "リソース グループ名" -Name "VM 名"
VM の削除 | [Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "リソース グループ名" -Name "VM 名"
VM の汎用化 | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "VM 名" -Generalized<BR></BR><BR></BR>Save-AzureRmVMImage を実行する前にこのコマンドを実行する必要があります。
VM のキャプチャ | [Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "リソース グループ名" -VMName "VM 名" -DestinationContainerName "イメージ コンテナー" -VHDNamePrefix "イメージ名プレフィックス" -Path "C:\\filepath\\filename.json"<BR></BR><BR></BR>イメージの作成に仮想マシンを使用するには、その仮想マシンを[シャットダウンおよび一般化](virtual-machines-windows-capture-image.md)する必要があります。このコマンドを実行する前に、Set-AzureRmVm を実行します。
VM の更新 | [Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "リソース グループ名" -VM $vm<BR></BR><BR></BR>Get-AzureRmVM を使用して現在の VM の構成を取得し、VM オブジェクトの構成設定を変更してから、このコマンドを実行します。
VM へのデータ ディスクの追加 | [Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "ディスク名" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Get-AzureRmVM を使用して VM オブジェクトを取得します。LUN の数とディスクのサイズを指定します。Update-AzureRmVM を実行して、構成の変更を VM に適用します。追加するディスクは初期化されていません。これを行うには、「[Resource Manager と PowerShell を使用した Azure Virtual Machines の管理](virtual-machines-windows-ps-manage.md)」をご覧ください。
VM からのデータ ディスクの削除 | [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "ディスク名"<BR></BR><BR></BR>Get-AzureRmVM を使用して VM オブジェクトを取得します。Update-AzureRmVM を実行して、構成の変更を VM に適用します。
VM への拡張機能の追加 | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "リソース グループ名" -Location "Azure の場所" -VMName "VM 名" -Name "拡張機能名" -Publisher "パブリッシャー名" -Type "拡張機能タイプ" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>インストールする拡張機能の適切な[構成情報](virtual-machines-windows-extensions-configuration-samples.md)を使用してこのコマンドを実行します。
VM 拡張機能の削除 | [Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "リソース グループ名" -Name "拡張機能名" -VMName "VM 名"

## 次のステップ

- 仮想マシンを作成するための基本的な手順について「[Resource Manager と PowerShell を使用して Windows VM を作成する](virtual-machines-windows-ps-create.md)」を参照します。

<!---HONumber=AcomDC_0629_2016-->