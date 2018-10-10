---
title: Azure SQL Database Managed Instance の VNet 構成 | Microsoft Docs
description: このトピックでは、Azure SQL Database マネージド インスタンスを使用する仮想ネットワーク (VNet) の構成オプションについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 9d3f867dad40017e8e97ec4f5e370533b018263c
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181174"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance の VNet を構成する

Azure SQL Database Managed Instance は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 このデプロイにより、次のシナリオが可能になります。 
- オンプレミス ネットワークからマネージド インスタンスへの直接接続 
- リンク サーバーまたは別のオンプレミス データ ストアへのマネージド インスタンスの接続 
- Azure リソースへのマネージド インスタンスの接続  

## <a name="plan"></a>プラン

次の質問の回答を使用して、仮想ネットワークでのマネージド インスタンスのデプロイ方法を計画します。 
- 単一または複数のマネージド インスタンスをデプロイする予定はありますか? 

  マネージド インスタンスの数で、お使いのマネージド インスタンスに割り当てるサブネットの最小サイズが決まります。 詳細については、「[マネージド インスタンスのサブネットのサイズを決定する](#determine-the-size-of-subnet-for-managed-instances)」を参照してください。 
- 既存の仮想ネットワークにマネージド インスタンスをデプロイする必要がありますか? または新しいネットワークを作成しますか? 

   既存の仮想ネットワークを使用する予定の場合は、お使いのマネージド インスタンスに合わせて、そのネットワーク構成を変更する必要があります。 詳細については、「[マネージド インスタンスの既存の仮想ネットワークを変更する](#modify-an-existing-virtual-network-for-managed-instances)」を参照してください。 

   新しい仮想ネットワークを作成する予定の場合は、「[マネージド インスタンスの新しい仮想ネットワークを作成する](#create-a-new-virtual-network-for-a-managed-instance)」を参照してください。

## <a name="requirements"></a>必要条件

マネージド インスタンスを作成するには、次の要件に準拠するサブネット (マネージド インスタンス サブネット) を仮想ネットワーク内に作成します。
- **専用サブネット**: マネージド インスタンス サブネットには、自身に関連付けられている他のクラウド サービスを含めることはできません。また、ゲートウェイ サブネットを指定することはできません。 マネージド インスタンス以外のリソースを含むサブネットでマネージド インスタンスを作成したり、後でサブネット内で他のリソースを追加したりすることはできません。
- **互換性のあるネットワーク セキュリティ グループ (NSG)**: マネージド インスタンス サブネットに関連付けられている NSG には、他の規則の前に、次の表に示す規則 (必須の受信セキュリティ規則と送信セキュリティ規則) を含める必要があります。 NSG を使用して、マネージド インスタンス データ エンドポイントへのアクセスを完全に制御するには、ポート 1433 でトラフィックをフィルター処理します。 
- **互換性のあるユーザー定義ルート テーブル (UDR)**: マネージド インスタンス サブネットには、**次ホップ インターネット 0.0.0.0/0** が必須 UDR として割り当てられているユーザー ルート テーブルが必要です。 また、オンプレミスのプライベート IP 範囲が宛先として含まれるトラフィックを、仮想ネットワーク ゲートウェイまたは仮想ネットワーク アプライアンス (NVA) 経由でルーティングする UDR を追加することもできます。 
- **省略可能なカスタム DNS**: カスタム DNS が仮想ネットワークで指定されている場合、Azure の再帰的なリゾルバーの IP アドレス (168.63.129.16 など) をリストに追加する必要があります。 詳細については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。 カスタム DNS サーバーは、*microsoft.com*、*windows.net*、*windows.com*、*msocsp.com*、*digicert.com*、*live.com*、*microsoftonline.com*、*microsoftonline-p.com* の各ドメインとそのサブドメインのホスト名を解決できる必要があります。 
- **サービス エンドポイントなし**: マネージド インスタンス サブネットにサービス エンドポイントを関連付けることはできません。 仮想ネットワークの作成時に、サービス エンドポイント オプションが無効になっていることを確認してください。
- **十分な IP アドレス**: マネージド インスタンス サブネットには少なくとも 16 個の IP アドレスが必要です (32 個以上を推奨)。 詳細については、「[マネージド インスタンスのサブネットのサイズを決定する](#determine-the-size-of-subnet-for-managed-instances)」を参照してください。

> [!IMPORTANT]
> デプロイ先のサブネットがこれらのすべての要件と互換性がない場合、新しいマネージド インスタンスをデプロイすることはできません。 マネージド インスタンスが作成されると、ネットワーク構成に対して非準拠の変更が行われないように、サブネットでは "*ネットワーク インテント ポリシー*" が適用されます。 最後のインスタンスがサブネットから削除されると、"*ネットワーク インテント ポリシー*" も削除されます

### <a name="mandatory-inbound-security-rules"></a>必須の受信セキュリティ規則 

| 名前       |ポート                        |プロトコル|接続元           |接続先|アクション|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000、9003、1438、1440、1452|TCP     |任意              |任意        |ALLOW |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |ALLOW |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |ALLOW |

### <a name="mandatory-outbound-security-rules"></a>必須の送信セキュリティ規則 

| 名前       |ポート          |プロトコル|接続元           |接続先|アクション|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80、443、12000|TCP     |任意              |任意        |ALLOW |
|mi_subnet   |任意           |任意     |任意              |MI SUBNET  |ALLOW |

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>マネージド インスタンスのサブネットのサイズを決定する

マネージド インスタンスを作成する場合、Azure では、プロビジョニング中に選択された階層に応じて、仮想マシンの数を割り当てます。 これらの仮想マシンはお使いのサブネットに関連付けられているため、IP アドレスが必要です。 通常の操作とサービス メンテナンス時の高可用性を確保するために、Azure は追加の仮想マシンを割り当てることがあります。 その結果、サブネット内の必要な IP アドレスの数が、そのサブネット内のマネージド インスタンスの数より大きくなります。 

仕様上、マネージド インスタンスにはサブネット内で 16 個以上の IP アドレスが必要であり、最大 256 個の IP アドレスを使用する場合があります。 その結果、サブネットの IP 範囲を定義するときに、/28 から /24 のサブネット マスクを使用することができます。 

> [!IMPORTANT]
> 16 個の IP アドレスから成るサブネット サイズは、マネージド インスタンスをさらにスケールアウトするために必要な最小限の潜在能力です。プレフィックス /27 以下のサブネットを選択することを強くお勧めします。 

サブネット内に複数のマネージド インスタンスをデプロイする予定で、サブネット サイズを最適化する必要がある場合、以下のパラメーターを使用して、計算を形成します。 

- Azure では、独自のニーズに応じて、サブネット内で 5 個の IP アドレスを使用します。 
- 汎用インスタンスにはそれぞれ 2 つのアドレスが必要です。 
- 各 Business Critical インスタンスには 4 つのアドレスが必要です

**例**: 3 つの General Purpose と 2 つの Business Critical マネージド インスタンスを予定しています。 これは、5 + 3 * 2 + 2 * 4 = 19 の IP アドレスが必要であることを意味します。 IP 範囲は 2 のべき乗で定義されているため、32 (2^5) の IP アドレスの IP 範囲が必要です。 したがって、/27 サブネット マスクのサブネットを予約する必要があります。 

> [!IMPORTANT]
> 今後の改善に伴い、上記の計算は古くなっていきます。 

## <a name="create-a-new-virtual-network-for-a-managed-instance"></a>マネージド インスタンスの新しい仮想ネットワークを作成する

仮想ネットワークの作成と構成を行う最も簡単な方法は、Azure Resource Manager のデプロイ テンプレートを使用することです。

1. Azure ポータルにサインインします。

2. Azure クラウドに仮想ネットワークをデプロイするには、**[Azure にデプロイする]** ボタンを使用します。

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  このボタンにより、Managed Instance をデプロイできるネットワーク環境を構成するために使用できるフォームが開きます。

  > [!Note]
  > この Azure Resource Manager テンプレートでは、2 つのサブネットを持つ仮想ネットワークをデプロイします。 1 つは **ManagedInstances** と呼ばれるサブネットであり、マネージド インスタンス用に予約され、ルート テーブルが事前に構成されています。もう 1 つのサブネットは **Default** と呼ばれ、Managed Instance にアクセスする必要がある他のリソース (Azure Virtual Machines など) に使用されます。 必要でない場合、**Default** サブネットは削除できます。

3. ネットワーク環境を構成します。 次のフォームで、ネットワーク環境のパラメーターを構成できます。

![Azure ネットワークの構成](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

VNet とサブネットの名前を変更することも、ネットワーク リソースに関連付けられた IP 範囲を調整することもできます。 [購入] ボタンをクリックすると、このフォームにより環境が作成および構成されます。 2 つのサブネットを必要としない場合は、既定のサブネットを削除できます。 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>マネージド インスタンスの既存の仮想ネットワークを変更する 

このセクションの質問と回答では、既存の仮想ネットワークにマネージド インスタンスを追加する方法を示します。 

**既存の仮想ネットワークではクラシックまたは Resource Manager デプロイ モデルを使用していますか?** 

マネージド インスタンスを作成できるのは、Resource Manager 仮想ネットワークでのみです。 

**SQL Managed Instance の新しいサブネットを作成しますか? または、既存のサブネットを使用しますか?**

新しいサブネットを作成する場合は、次のようにします。 

- 「[マネージド インスタンスのサブネットのサイズを決定する](#determine-the-size-of-subnet-for-managed-instances)」セクションのガイドラインに従って、サブネットのサイズを計算します。
- 「[仮想ネットワーク サブネットの追加、変更、削除](../virtual-network/virtual-network-manage-subnet.md)」の手順に従います。 
- 次ホップ インターネットとして単一のエントリ **0.0.0.0/0** を含むルート テーブルを作成して、マネージド インスタンスのサブネットに関連付けます。  

既存のサブネット内に Managed Instance を作成する場合は、次の PowerShell スクリプトでサブネットを準備することをお勧めします。
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

- 検証 - 選択された仮想ネットワークとサブネットが Managed Instance のネットワーク要件と照らして検証されます。
- 同意 - Managed Instance のデプロイに備えてサブネットを準備するために必要な一連の変更がユーザーに示され、それに対する同意をユーザーは求められます。
- 準備 - 仮想ネットワークとサブネットが適切に構成されます。

**カスタム DNS サーバーは構成されていますか?** 

構成されている場合は、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。 

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- VNet を作成し、マネージド インスタンスを作成して、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。
