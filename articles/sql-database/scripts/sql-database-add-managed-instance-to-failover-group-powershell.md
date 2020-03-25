---
title: PowerShell の例 - フェールオーバー グループ - Azure SQL Database マネージド インスタンス
description: Azure SQL Database のマネージド インスタンスを作成し、それをフェールオーバー グループに追加して、フェールオーバーをテストする Azure PowerShell スクリプトの例。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691765"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>PowerShell を使用して Azure SQL Database のマネージド インスタンスをフェールオーバー グループに追加する 

この PowerShell スクリプトの例では、2 つのマネージド インスタンスを作成し、それらをフェールオーバー グループに追加して、プライマリ マネージド インスタンスからセカンダリ マネージド インスタンスへのフェールオーバーをテストします。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは AZ PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-scripts"></a>サンプルのスクリプト

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。 リソース グループは 2 回削除する必要があります。 最初にリソース グループを削除すると、マネージド インスタンスと仮想クラスターが削除されますが、その後、エラー メッセージ `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.` が表示されて失敗します。 Remove-AzResourceGroup コマンドをもう一度実行すると、残りのすべてのリソースと、リソース グループが削除されます。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure リソース グループを作成します。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 仮想ネットワークにサブネット構成を追加します。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | リソース グループ内の仮想ネットワークを取得します。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 仮想ネットワーク内のサブネットを取得します。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | ネットワーク セキュリティ グループを作成します。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | ルート テーブルを作成します。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 仮想ネットワークのサブネット構成を更新します。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 仮想ネットワークを更新します。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | ネットワーク セキュリティ グループを取得します。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| ネットワーク セキュリティ グループにネットワーク セキュリティ ルールの構成を追加します。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | ネットワーク セキュリティ グループを更新します。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | ルート テーブルにルートを追加します。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | ルート テーブルを更新します。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Azure SQL Database マネージド インスタンスを作成します。  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Azure SQL Database マネージド インスタンスに関する情報を返します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 仮想ネットワーク ゲートウェイの IP 構成を作成します |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 仮想ネットワーク ゲートウェイを作成します |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 2 つの仮想ネットワーク ゲートウェイ間の接続を作成します。   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 新しい Azure SQL Database マネージド インスタンスのフェールオーバー グループを作成します。  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | マネージド インスタンスのフェールオーバー グループを取得または一覧表示します。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | マネージド インスタンスのフェールオーバー グループのフェールオーバーを実行します。 | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループを削除します。 | 

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。
