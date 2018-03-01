---
title: "Azure の Desired State Configuration の概要 | Microsoft Docs"
description: "PowerShell Desired State Configuration の Microsoft Azure 拡張機能ハンドラーの使用に関する概要。 前提条件、アーキテクチャ、コマンドレットなどについて説明します。"
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure Desired State Configuration 拡張機能ハンドラーの概要

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure VM エージェントとそれに関連付けられた拡張機能は、Microsoft Azure インフラストラクチャ サービスの一部です。
VM 拡張機能は、VM の機能を拡張し、さまざまな VM の管理操作を簡略化するソフトウェア コンポーネントです。

Desired State Configuration 拡張機能の主な用途は、VM 構成の継続的な管理や、他の運用ツール (例: Azure 監視) との統合などの[メリット](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service)を提供する [Azure Automation DSC サービス](../../automation/automation-dsc-overview.md)への VM のブートストラップです。

Azure Automation DSC サービスに関係なく DSC 拡張機能を使用することもできますが、その場合はデプロイ時に実行する単一のアクションであるため、VM 内でローカル以外の構成の継続的なレポートや管理は行われません。

この記事には、Azure Automation のオンボード用の DSC 拡張機能と、Azure SDK を使用して仮想マシンに構成を割り当てるためのツールとして DSC 拡張機能を使用する方法の両方のシナリオに関する情報が含まれています。

## <a name="prerequisites"></a>前提条件

- **ローカル マシン**: Azure VM 拡張機能を利用するには、Azure Portal または Azure PowerShell SDK のいずれかを使用する必要があります。
- **ゲスト エージェント**: DSC 構成で構成する Azure VM は、Windows Management Framework (WMF) 4.0 以降をサポートする OS である必要があります。 サポートされている OS バージョンの詳細な一覧については、[DSC 拡張機能のバージョン履歴](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/)を参照してください。

## <a name="terms-and-concepts"></a>用語と概念

このガイドでは、読者が次の概念を理解していることを想定しています。

- **構成** - DSC 構成ドキュメント。
- **ノード** - DSC 構成のターゲット。 このドキュメントでは、"ノード" は常に Azure VM を指します。
- **構成データ** - 構成に関する環境データが格納されている .psd1 ファイル。

## <a name="architectural-overview"></a>アーキテクチャの概要

Azure DSC 拡張機能は、Azure VM エージェント フレームワークを使用して、Azure VM で実行される DSC 構成の配布、適用、およびレポート作成を行います。
DSC 拡張機能は、構成ドキュメントと一連のパラメーターを受け取ります。
ファイルが指定されていない場合は、[既定の構成スクリプト](###default-configuration-script)が拡張機能に埋め込まれます。このスクリプトは、[ローカル構成マネージャー](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig)のメタデータを設定するためだけに使用されます。

拡張機能が初めて呼び出されると、次のロジックに従って、あるバージョンの Windows Management Framework (WMF) がインストールされます。

1. Azure VM の OS が Windows Server 2016 の場合は、処理は行われません。 Windows Server 2016 では既に PowerShell の最新バージョンがインストールされてるためです。
2. `wmfVersion` プロパティを指定した場合は、VM の OS と互換性がない場合を除いて、そのバージョンの WMF がインストールされます。
3. `wmfVersion` プロパティを指定しなかった場合は、WMF の適用可能な最新バージョンがインストールされます。

WMF をインストールした場合は再起動が必要になります。
再起動後、`modulesUrl` プロパティで指定された .zip ファイルが拡張機能によってダウンロードされます (指定した場合)。
その場所が Azure BLOB ストレージ内の場合は、 `sasToken` プロパティに SAS トークンを指定すると、ファイルにアクセスできます。
.zip がダウンロードされて展開された後、`configurationFunction` で定義されている構成関数が実行され、MOF ファイルが生成されます。
その後、拡張機能によって、生成された MOF ファイルを使用して `Start-DscConfiguration -Force` が実行されます。
拡張機能は、この出力を取得して Azure の状態チャネルに書き込みます。

### <a name="default-configuration-script"></a>既定の構成スクリプト

Azure DSC 拡張機能には、VM を Azure Automation DSC サービスにオンボードするときに使用することを目的とした既定の構成スクリプトが含まれています。
スクリプト パラメーターは、[ローカル構成マネージャー](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig)の構成可能なプロパティと合致しています。
スクリプト パラメーターについては、[こちら](extensions-dsc-template.md##default-configuration-script)を参照してください。スクリプト全体は [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true) から入手できます。

## <a name="dsc-extension-in-arm-templates"></a>ARM テンプレートの DSC 拡張機能

DSC 拡張機能を使用する場合、ほとんどのシナリオで Azure Resource Manager (ARM) デプロイ テンプレートを利用します。
ARM デプロイ テンプレートに DSC 拡張機能を含める方法の詳細と例については、専用のドキュメント ページ「[Azure Resource Manager テンプレートを使用した Desired State Configuration 拡張機能](extensions-dsc-template.md)」を参照してください。

## <a name="dsc-extension-powershell-cmdlets"></a>DSC 拡張機能の PowerShell コマンドレット

対話型のトラブルシューティングや情報収集のシナリオには、DSC 拡張機能を管理するための PowerShell コマンドレットが最適です。
これらのコマンドレットを使用すると、DSC 拡張機能のデプロイをパッケージ化、発行、監視できます。
DSC 拡張機能用のコマンドレットは、[既定の構成スクリプト](###default-configuration-script)で動作するようにはまだ更新されていません。

`Publish-AzureRMVMDscConfiguration` は、構成ファイルを取り込み、依存する DSC リソースがあるかどうかを調べ、構成とその適用に必要な DSC リソースが含まれる .zip ファイルを作成します。
また、 `-ConfigurationArchivePath` パラメーターを使用して、パッケージをローカルに作成することもできます。
それ以外の場合は、Azure Blob Storage に .zip ファイルを発行し、SAS トークンを使用して保護します。

このコマンドレットによって作成された .zip ファイルでは、アーカイブ フォルダーのルートに .ps1 構成スクリプトがあります。
リソースのモジュール フォルダーは、アーカイブ フォルダーに配置されます。

`Set-AzureRMVMDscExtension` は、PowerShell DSC 拡張機能に必要な設定を VM 構成オブジェクトに挿入します。

`Get-AzureRMVMDscExtension` は、特定の VM の DSC 拡張機能の状態を取得します。

`Get-AzureRMVMDscExtensionStatus` は、DSC 拡張機能ハンドラーによって適用された DSC 構成の状態を取得します。
この処理は、単一の VM または VM のグループに対して実行できます。

`Remove-AzureRMVMDscExtension` は、特定の仮想マシンから拡張機能ハンドラーを削除します。
このコマンドレットによって、構成の削除、WMF のアンインストール、または仮想マシンに適用されている設定の変更が **行われることはありません** 。
拡張機能ハンドラーが削除されるだけです。 

AzureRM DSC 拡張機能のコマンドレットに関する重要な情報:

- Azure Resource Manager コマンドレットは動機的です。
- ResourceGroupName、VMName、ArchiveStorageAccountName、Version、および Location はすべて必須パラメーターです。
- ArchiveResourceGroupName は省略可能なパラメーターです。 このパラメーターを指定できるのは、仮想マシンが作成されたリソース グループとは別のリソース グループにストレージ アカウントが属している場合です。
- AutoUpdate スイッチを使用すると、最新バージョンが利用可能になったときに、拡張機能ハンドラーが自動的に更新されるようにできます。 WMF の新しいバージョンがリリースされると、このパラメーターによって VM で再起動が生じる可能性があることに注意してください。

### <a name="getting-started-with-cmdlets"></a>コマンドレットの概要

Azure DSC 拡張機能は、デプロイ中に DSC 構成ドキュメントを直接使用して Azure VM を構成することができます。ただし、ノードを Azure Automation に登録しないため、ノードは一元的に管理*されません*。

構成の簡単な例を次に示します。
これを "IisInstall.ps1" という名前でローカルに保存してください。

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

次の手順では、指定した VM に IisInstall.ps1 スクリプトを配置し、構成を実行した後、その状態に関するレポートを返します。

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Azure Portal の機能

VM に移動します。 [設定]、[全般] の順に移動し、[拡張機能] をクリックします。
新しいウィンドウが作成されます。
[追加] をクリックし、[PowerShell DSC] を選択します。

ポータルでは、入力が必要になります。
**[Configuration Modules or Script]\(構成モジュールまたはスクリプト\)**: このフィールドは必須です (フォームは[既定の構成スクリプト](###default-configuration-script) 用に更新されていません)。
構成スクリプトを含む .ps1 ファイルか、.ps1 構成スクリプトがルートに含まれ、すべての依存リソースがモジュール フォルダーに含まれる .zip ファイルが必要です。
これは、Azure PowerShell SDK に含まれている `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` コマンドレットを使用して作成できます。
zip ファイルは、SAS トークンによってセキュリティで保護された、ユーザーの Blob Storage にアップロードされます。

**[Configuration Data PSD1 File (構成データ PSD1 ファイル)]**: このフィールドはオプションです。
.psd1 の構成データ ファイルが必要な構成では、このフィールドを使用して目的のファイルを選択し、SAS トークンによってセキュリティで保護されている、ユーザーの Blob Storage にアップロードします。

**[構成のモジュール修飾名]**: .ps1 ファイルには、複数の構成関数を含めることができます。
.ps1 構成スクリプトの名前に続けて '\' と構成関数の名前を入力します。
たとえば、.ps1 スクリプトの名前が "configuration.ps1" であり、構成が "IisInstall" であれば、次のように入力します: `configuration.ps1\IisInstall`

**[構成引数]**: 構成関数が引数を受け取る場合は、`argumentName1=value1,argumentName2=value2` という形式でここに入力します。
この形式は、PowerShell コマンドレットまたは Resource Manager テンプレートで構成引数を受け取る方法とは異なる形式であることに注意してください。

## <a name="logging"></a>ログの記録
ログは次の場所に記録されます。

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>次の手順
PowerShell DSC の詳細については、 [PowerShell ドキュメント センター](https://msdn.microsoft.com/powershell/dsc/overview)を参照してください。 

[DSC 拡張機能用の Azure Resource Manager テンプレート](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。 

PowerShell DSC で管理できる追加機能については、 [PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) で DSC リソースを検索してください。

機微なパラメーターを構成に渡す方法について詳しくは、[資格情報を Azure DSC 拡張機能ハンドラーに渡す方法](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。
