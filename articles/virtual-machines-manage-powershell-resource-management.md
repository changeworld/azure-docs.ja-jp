<properties
   pageTitle="Azure PowerShell でリソース マネージャー モードを使用する一般的な VM タスク"
   description="Azure PowerShell でリソース マネージャー モードを使用する一般的な VM タスクのコマンドについて説明します"
   services="virtual-machines"
   documentationCenter="windows"
   authors="KBDAzure"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="kathydav;singhkay"/>

# リソース マネージャー モードの Azure PowerShell を使用する一般的な VM タスク

 > [AZURE.SELECTOR]
    - [リソース マネージャー](virtual-machines-manage-powershell-resource-management.md)
    - [サービス管理](virtual-machines-manage-powershell-service-management.md)

リソース マネージャー モードの Azure PowerShell コマンドレットを使用すると、VM を管理するために日常的に行う多くのタスクを自動化できます。この記事では、比較的単純なタスクにはコマンド例を示し、より複雑なタスクには、コマンドが記載されている記事へのリンクを示します。

>[AZURE.NOTE]Azure PowerShell をまだインストールして構成していない場合は、[こちら](install-configure-powershell.md)から手順を参照してください。

## 例のコマンドを使用する方法
コマンド内のテキストの一部を、使用する環境に適した別のテキストに置き換える必要があります。記号の < and > は置き換える必要があるテキストであることを示しています。テキストを置き換える場合、記号は削除し、引用符はそのままの位置に残します。

## 仮想マシンの取得
これは頻繁に使用することになる基本的なタスクです。VM に関する情報の取得、VM でのタスクの実行、変数に格納する出力の取得に使用します。

VM に関する情報を取得するには、このコマンドを実行し、引用符内のすべての文字 (< and > を含む) を置き換えます。

     Get-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

リソース グループの名前を "MyRG"、VM の名前を "MyVM" としてコマンドを実行すると、次のように表示されます。

<!-- need image-->

出力を $vm 変数に格納するには、次のように実行します。

    $vm = Get-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

<!--need this log-on syntax 
## Log on to a Windows-based virtual machine

Run these commands:

>[AZURE.NOTE] You can get the virtual machine and cloud service name from the display of the **Get-AzureVM** command.
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch 
-->

## VM の起動

次のコマンドを実行します。

    Start-AzureVM -ResourceGroupName "<resource group name>" -Name "<virtual machine name>"

## VM の停止

次のコマンドを実行します。

    Stop-AzureVM -ResourceGroupName "<resource group name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]**StayProvisioned** パラメーターは、VM がクラウド サービス内の最後の VM である場合に、そのクラウド サービスの仮想 IP (VIP) を保持するために使用します。このパラメーターを使用する場合、その VM に対して引き続き課金されます。

## VM の再起動

	Restart-AzureVM -ResourceGroupName "<resource group name>" -Name "<virtual machine name>"

## データ ディスクの接続
このタスクには、いくつかの手順が必要です。最初に、****Add-AzureDataDisk**** コマンドレットを使用して $vm オブジェクトにディスクを追加し、次に Update-AzureVM コマンドレットを使用して VM の構成を更新します。

新しいディスクとデータを含むディスクのどちらを接続するかについても決める必要があります。新しいディスクの場合、コマンドによって .vhd ファイルが作成され、それが同じコマンドで接続されます。

新しいディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

既存のデータ ディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

BLOB ストレージにある既存の .vhd ファイルからデータ ディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Windows VM の作成

Azure で Windows ベースの仮想マシンを新たに作成するには、「[Azure PowerShell を使用して Windows ベースの仮想マシンを作成し、事前構成する](virtual-machines-ps-create-preconfigure-windows-vms.md)」をご覧ください。このトピックでは、次の項目を事前構成することができる Windows 仮想マシンを作成するための PowerShell コマンド セットを作成する手順を説明します。

- Active Directory ドメイン メンバーシップ
- 追加のディスク
- 既存の負荷分散セットのメンバーとしての設定
- 静的 IP アドレス


<!--HONumber=52-->
