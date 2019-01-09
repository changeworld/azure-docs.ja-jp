---
title: Azure SQL Database Managed Instance 用の既存の VNet/サブネットの構成 | Microsoft Docs
description: このトピックでは、Azure SQL Database Managed Instance をデプロイできる既存の仮想ネットワーク (VNet) とサブネットの構成方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 53aba5192ddf57598965fcfe0db5f2b18423c7e9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345595"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>既存の VNet を Azure SQL Database Managed Instance 用に構成する

Azure SQL Database Managed Instance は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) と Managed Instance 専用のサブネット内にデプロイする必要があります。 [Managed Instance の VNet 要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)に従って構成されていれば、既存の VNet とサブネットを使用できます。 

未構成の新しいサブネットがあり、そのサブネットが[要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)に適合しているかどうかわからない場合、または何らかの変更を行った後でサブネットが依然として[ネットワーク要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)に準拠していることを確認したい場合は、この記事で説明するスクリプトを使用してネットワークの検証と変更を行うことができます。 

  > [!Note]
  > マネージド インスタンスを作成できるのは、Resource Manager 仮想ネットワークでのみです。 クラシック デプロイ モデルを使用してデプロイされた Azure VNet はサポートされていません。 サブネットはその中にリソースをデプロイした後でサイズを変更することはできないため、[Managed Instance 用のサブネットのサイズの決定](#determine-the-size-of-subnet-for-managed-instances)に関する記事のガイドラインに従ってサブネットのサイズが計算されていることを確認してください。

## <a name="validate-and-modify-an-existing-virtual-network"></a>既存の仮想ネットワークを検証および変更する 

既存のサブネット内にマネージド インスタンスを作成する場合は、次の PowerShell スクリプトでサブネットを準備することをお勧めします。
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
サブネットの準備は、次に示した簡単な 3 つの手順で行います。

1. 検証 - 選択された仮想ネットワークとサブネットが Managed Instance のネットワーク要件と照らして検証されます。
2. 同意 - Managed Instance のデプロイに備えてサブネットを準備するために必要な一連の変更がユーザーに示され、それに対する同意をユーザーは求められます。
3. 準備 - 仮想ネットワークとサブネットが適切に構成されます。

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- VNet を作成し、マネージド インスタンスを作成して、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。
