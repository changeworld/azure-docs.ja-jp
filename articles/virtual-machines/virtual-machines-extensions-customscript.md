<properties
   pageTitle="Windows でのカスタムのスクリプト拡張機能"
   description="Windows でカスタムのスクリプト拡張機能を使って仮想マシンの構成タスクを自動化します。"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="madhana"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2015"
   ms.author="kundanap"/>

# Windows でのカスタムのスクリプト拡張機能

この記事では Azure PowerShell コマンドレットで Windows でカスタムのスクリプト拡張機能を使用する概要について説明します。


Microsoft や信頼された第三者の発行元によってビルドされた仮想マシン (VM) の拡張機能を使って、VM の機能を拡張します。VM 拡張機能の詳細については、<a href="https://msdn.microsoft.com/library/azure/dn606311.aspx" target="_blank">MSDN のドキュメント</a>を参照してください。

## カスタム スクリプト拡張機能の概要

Windows のカスタム スクリプト拡張機能を使うと、リモートの仮想マシンで PowerShell スクリプトを実行できます。ログインする必要はありません。このスクリプトは VM をプロビジョニングした後か、VM の稼働中であればいつでも実行できます。VM で新たにポートを開く必要はありません。VM のプロビジョニング後、追加のソフトウェアを実行する際、インストールする際、構成する際にカスタム スクリプトが最もよく使われます。

### カスタム スクリプト拡張機能を実行する前提条件

1. <a href="http://azure.microsoft.com/downloads" target="_blank">こちら</a>から Azure PowerShell コマンドレット バージョン 0.8.0 以降をインストールします。
2. スクリプトが既存の VM で実行されている場合、VM エージェントがその VM 上で有効であることをご確認ください。そうでない場合は、<a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">こちらの記事</a>の手順に従ってインストールしてください。
3. VM で実行するスクリプトを Azure ストレージにアップロードします。スクリプトは、1 つか複数のストレージ コンテナーから取得できます。
4. スクリプトは、拡張機能によって起動されるエントリ スクリプトが、他のスクリプトを順に起動するように記述されている必要があります。

## カスタム スクリプト拡張機能のシナリオ:

 ### ファイルを既定のコンテナーにアップロードします。サブスクリプションの既定アカウントのストレージ コンテナーにスクリプトがある場合、下記のコマンドレット スニペットは VM でスクリプトを実行する方法について示しています。下記のサンプルの ContainerName は、スクリプトをアップロードする場所を示します。既定のストレージ アカウントは、Get-AzureSubscription –Default コマンドレットを使って検証できます。

注: ここでは新しい VM を作成していますが、既存の VM でも同様の操作を行うことができます。

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### 既定以外のストレージ コンテナーへのファイルのアップロード

ここでは同一サブスクリプションで、または異なるサブスクリプションで、既定以外のストレージを使用してスクリプトやファイルをアップロードする方法について説明します。ここでは既存の VM を使用していますが、新しい VM を作成して同様の操作を行うことができます。

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM
  ### 異なるストレージ アカウントの複数のコンテナーにスクリプトをアップロードします。スクリプト ファイルが複数のコンテナーに保存されており、現在それらのスクリプトを実行している場合、それらのファイルの完全な SAS URL を指定する必要があります。

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### ポータルからカスタム スクリプト拡張機能を追加
<a href="https://portal.azure.com/ " target="_blank">Azure プレビュー ポータル</a>の仮想マシンを参照し、実行するスクリプト ファイルを指定して拡張機能を追加します。 ![][5]

  ### カスタム スクリプト拡張機能のアンインストール

次のコマンドレットを使って、VM からカスタム スクリプト拡張機能をアンインストールできます。

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### 近日対応予定

近日中に Linux 向けカスタム スクリプトの使用やサンプルについての記事を追加する予定です。ご期待ください。

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png
 

<!---HONumber=July15_HO3-->