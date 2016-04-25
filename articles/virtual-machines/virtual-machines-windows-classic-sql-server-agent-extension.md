<properties
	pageTitle="SQL Server IaaS Agent 拡張機能 (クラシック デプロイ)| Microsoft Azure"
	description="このトピックでは、Azure で SQL Server を実行する VM が自動化機能を使用できるようにする SQL Server Agent 拡張機能について説明します。ここでは、クラシック デプロイメント モードを使用します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
   editor=""    
   tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/08/2016"
	ms.author="jroth"/>

# SQL Server IaaS Agent 拡張機能 (クラシック デプロイ)

この拡張機能を使用すると、Azure Virtual Machines の SQL Server で、この記事に掲載されている特定のサービスを使用できるようになります。これらのサービスは、この拡張機能がインストールされている場合にのみ使用できます。この拡張機能は、Azure ポータルの SQL Server Gallery Images の場合に自動的にインストールされます。Azure VM Guest Agent がインストールされている Azure の任意の SQL Server VM にインストールできます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


## 前提条件
Powershell コマンドレットを使用するための要件:

- 最新の Azure PowerShell ([ここから入手可能](../powershell-install-configure.md))

VM で拡張機能を使用するための要件:

- Azure VM Guest Agent
- Windows Server 2012、Windows Server 2012 R2 以降
- SQL Server 2012、SQL Server 2014 以降

## 拡張機能で使用できるサービス

- **SQL の自動化されたバックアップ**: このサービスで、VM 内の SQL Server の既定インスタンスについて、すべてのデータベースのバックアップが自動的にスケジュールされます。詳細については、「[Automated backup for SQL Server in Azure Virtual Machines (Classic) (Azure Virtual Machines での SQL Server の自動バックアップ (クラシック デプロイ))](virtual-machines-windows-classic-sql-automated-backup.md)」をご覧ください。
- **SQL 自動修正プログラム適用**: このサービスを使用すると、VM の更新プログラムを実行できるメンテナンス期間を構成できるので、ワークロードのピーク時の更新プログラムを回避できます。詳細については、「[Automated patching for SQL Server in Azure Virtual Machines (Classic) (Azure Virtual Machines での SQL Server の自動修正 (クラシック デプロイ))](virtual-machines-windows-classic-sql-automated-patching.md)」を参照してください。
- **Azure Key Vault 統合**: このサービスでは、SQL Server VM に Azure Key Vault を自動的にインストールして構成することができます。詳細については、「[Azure VM で SQL Server 用に Azure Key Vault 統合を構成する (クラシック デプロイ)](virtual-machines-windows-classic-ps-sql-keyvault.md)」を参照してください。

## Powershell を使用した拡張機能の追加
[Azure ポータル](virtual-machines-windows-portal-sql-server-provision.md)を使用して SQL Server VM をプロビジョニングすると、拡張機能は自動的にインストールされます。Azure クラシック ポータルで SQL Server VM をプロビジョニングした場合、または SQL ライセンスを持っている VM の場合、**Set-AzureVMSqlServerExtension** Azure PowerShell コマンドレットを使用して、この拡張機能を追加できます。

### 構文

Set-AzureVMSqlServerExtension [[-ReferenceName] [String]] [-VM] IPersistentVM [[-Version] [String]] [[-AutoPatchingSettings] [AutoPatchingSettings]] [-AutoBackupSettings[AutoBackupSettings]] [-Profile [AzureProfile]] [CommonParameters]

> [AZURE.NOTE] –Version パラメーターは、省略することをお勧めします。省略すると、最新バージョンの拡張機能が既定値になります。

### 例
次の例では、$abs (ここには表示されていません) で定義されている構成を使用して自動バックアップの設定を構成します。serviceName は、仮想マシンをホストするクラウド サービス名です。完全なサンプルについては、「[Automated backup for SQL Server in Azure Virtual Machines (Classic) (Azure Virtual Machines での SQL Server の自動バックアップ (クラシック デプロイ))](virtual-machines-windows-classic-sql-automated-backup.md)」をご覧ください。

	Get-AzureVM –ServiceName "serviceName" –Name "vmName" | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## 拡張機能の状態を確認します。
この拡張機能とそれに関連付けられているサービスの状態を確認する場合は、いずれかのポータルを使用できます。既存の VM の詳細で、**[設定]** の** [拡張機能]** を確認します。

**Get-AzureVMSqlServerExtension** Azure Powershell コマンドレットを使用することもできます。

### 構文

Get-AzureVMSqlServerExtension [[-VM] [IPersistentVM]] [-Profile [AzureProfile]] [CommonParameters]

### 例
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Powershell を使用した拡張機能の削除   
VM からこの拡張機能を削除する場合は、**Remove-AzureVMSqlServerExtension** Azure Powershell コマンドレットを使用します。

### 構文

Remove-AzureVMSqlServerExtension [-Profile [AzureProfile]] -VM IPersistentVM [CommonParameters]

<!---HONumber=AcomDC_0413_2016-->