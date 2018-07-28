---
title: Azure SQL Database Managed Instance の VNet 構成 | Microsoft Docs
description: このトピックでは、Azure SQL Database マネージド インスタンスを使用する仮想ネットワーク (VNet) の構成オプションについて説明します。
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 0fea91fb067a6d78ef25cb0ff8014b65a8b6a916
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258102"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance の VNet を構成する

Azure SQL Database Managed Instance (プレビュー) は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 このデプロイにより、次のシナリオが可能になります。 
- オンプレミス ネットワークからマネージド インスタンスへの直接接続 
- リンク サーバーまたは別のオンプレミス データ ストアへのマネージド インスタンスの接続 
- Azure リソースへのマネージド インスタンスの接続  

## <a name="plan"></a>プラン

次の質問の回答を使用して、仮想ネットワークでのマネージド インスタンスのデプロイ方法を計画します。 
- 単一または複数のマネージド インスタンスをデプロイする予定はありますか? 

  マネージド インスタンスの数で、お使いのマネージド インスタンスに割り当てるサブネットの最小サイズが決まります。 詳細については、「[マネージド インスタンスのサブネットのサイズを決定する](#create-a-new-virtual-network-for-managed-instances)」を参照してください。 
- 既存の仮想ネットワークにマネージド インスタンスをデプロイする必要がありますか? または新しいネットワークを作成しますか? 

   既存の仮想ネットワークを使用する予定の場合は、お使いのマネージド インスタンスに合わせて、そのネットワーク構成を変更する必要があります。 詳細については、「[マネージド インスタンスの既存の仮想ネットワークを変更する](#modify-an-existing-virtual-network-for-managed-instances)」を参照してください。 

   新しい仮想ネットワークを作成する予定の場合は、「[マネージド インスタンスの新しい仮想ネットワークを作成する](#create-a-new-virtual-network-for-managed-instances)」を参照してください。

## <a name="requirements"></a>必要条件

マネージド インスタンスを作成する場合は、以下の要件に準拠する VNet 内に専用のサブネットが必要になります。
- **空である**: サブネットには関連付けられている他のクラウド サービスを含めることはできません。また、ゲートウェイ サブネットを指定することはできません。 マネージド インスタンス以外のリソースを含むサブネットでマネージド インスタンスを作成したり、後でサブネット内で他のリソースを追加したりすることはできません。
- **NSG なし**: サブネットにネットワーク セキュリティ グループを関連付けることはできません。
- **特定のルート テーブルがある**: サブネットには、割り当てられている唯一のルートとして次ホップ インターネットが 0.0.0.0/0 のユーザー ルート テーブル (UDR) が必要です。 詳細については、「[必要なルート テーブルを作成して関連付ける](#create-the-required-route-table-and-associate-it)」を参照してください。
3. **省略可能なカスタム DNS**: カスタム DNS が VNet で指定されている場合、Azure の再帰的なリゾルバーの IP アドレス (168.63.129.16 など) をリストに追加する必要があります。 詳細については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。
4. **サービス エンドポイントなし**: サブネットにサービス エンドポイント (ストレージまたは Sql) を関連付けることはできません。 VNet の作成時にサービス エンドポイント オプションが無効になっていることを確認してください。
5. **十分な IP アドレス**: サブネットには 16 個以上の IP アドレスが必要です。 詳細については、「[マネージド インスタンスのサブネットのサイズを決定する](#determine-the-size-of-subnet-for-managed-instances)」を参照してください。

> [!IMPORTANT]
> デプロイ先のサブネットが前述のすべての要件と互換性がない場合、新しいマネージド インスタンスをデプロイすることはできません。 デプロイ先の VNet とサブネットは、これらのマネージド インスタンスの (デプロイの前後の) 要件に従って保持する必要があります。少しでも要件を満たしていない場合、インスタンスが障害のある状態になり、使用できなくなる可能性があるためです。 その状態から回復するには、準拠するネットワーク ポリシーを使用して VNet で新しいインスタンスを作成し、インスタンス レベル データを再作成して、お使いのデータベースを復元する必要があります。 これにより、アプリケーションの大幅なダウンタイムが生じます。

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>マネージド インスタンスのサブネットのサイズを決定する

マネージド インスタンスを作成する場合、Azure では、プロビジョニング中に選択された階層サイズに応じて、仮想マシンの数を割り当てます。 これらの仮想マシンはお使いのサブネットに関連付けられているため、IP アドレスが必要です。 通常の操作とサービス メンテナンス時の高可用性を確保するために、Azure は追加の仮想マシンを割り当てることがあります。 その結果、サブネット内の必要な IP アドレスの数が、そのサブネット内のマネージド インスタンスの数より大きくなります。 

仕様上、マネージド インスタンスにはサブネット内で 16 個以上の IP アドレスが必要であり、最大 256 個の IP アドレスを使用する場合があります。 その結果、サブネットの IP 範囲を定義するときに、/28 から /24 のサブネット マスクを使用することができます。 

サブネット内に複数のマネージド インスタンスをデプロイする予定で、サブネット サイズを最適化する必要がある場合、以下のパラメーターを使用して、計算を形成します。 

- Azure では、独自のニーズに応じて、サブネット内で 5 個の IP アドレスを使用します。 
- 汎用インスタンスにはそれぞれ 2 つのアドレスが必要です。 
- 各 Business Critical インスタンスには 4 つのアドレスが必要です

**例**: 3 つの General Purpose と 2 つの Business Critical マネージド インスタンスを予定しています。 これは、5 + 3 * 2 + 2 * 4 = 19 の IP アドレスが必要であることを意味します。 IP 範囲は 2 のべき乗で定義されているため、32 (2^5) の IP アドレスの IP 範囲が必要です。 したがって、/27 サブネット マスクのサブネットを予約する必要があります。 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>マネージド インスタンスの新しい仮想ネットワークを作成する 

Azure 仮想ネットワークを作成することが、マネージド インスタンスを作成するための前提条件となります。 Azure Portal、[PowerShell](../virtual-network/quick-create-powershell.md)、または [Azure CLI](../virtual-network/quick-create-cli.md) を使用できます。 次のセクションでは、Azure Portal を使用する手順を示します。 ここで説明する詳細は、これらの各方法に適用されます。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[Virtual Network]** を見つけてクリックし、**[Resource Manager]** がデプロイ モードとして選択されていることを確認してから、**[作成]** をクリックします。

   ![仮想ネットワークの作成](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 次のスクリーンショットに示すように、仮想ネットワーク フォームに要求された情報を入力します。

   ![仮想ネットワークの作成フォーム](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. **Create** をクリックしてください。

   アドレス空間とサブネットは CIDR 表記で指定されます。 

   > [!IMPORTANT]
   > 既定値の場合、すべての VNet アドレス空間を使用するサブネットが作成されます。 このオプションを選択する場合は、マネージド インスタンス以外の仮想ネットワーク内で他のリソースを作成することはできません。 

   以下の方法をお勧めします。 
   - 「[マネージド インスタンスのサブネットのサイズを決定する](#determine-the-size-of-subnet-for-managed-instances)」セクションに従って、サブネットのサイズを計算します。  
   - VNet の残りの部分のニーズを評価します。 
   - VNet とサブネットのアドレス範囲を適宜入力します。 

   サービス エンドポイント オプションが **[無効]** になっていることを確認してください。 

   ![仮想ネットワークの作成フォーム](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>必要なルート テーブルを作成して関連付ける

1. Azure ポータルにサインインします。  
2. **[ルート テーブル]** を見つけてクリックし、ルート テーブル ページで **[作成]** をクリックします。

   ![ルート テーブルの作成フォーム](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. 次のスクリーンショットに示すように、0.0.0.0/0 の次ホップ インターネット ルートを作成します。

   ![ルート テーブルの追加](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![ルート](./media/sql-database-managed-instance-tutorial/route.png)

4. 次のスクリーンショットに示すように、このルートをマネージド インスタンスのサブネットに関連付けます。

    ![サブネット](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![ルート テーブルの設定](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![ルート テーブルの設定の保存](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


VNet が作成されたら、マネージド インスタンスを作成できます。  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>マネージド インスタンスの既存の仮想ネットワークを変更する 

このセクションの質問と回答では、既存の仮想ネットワークにマネージド インスタンスを追加する方法を示します。 

**既存の仮想ネットワークではクラシックまたは Resource Manager デプロイ モデルを使用していますか?** 

マネージド インスタンスを作成できるのは、Resource Manager 仮想ネットワークでのみです。 


  **SQL Managed Instance の新しいサブネットを作成しますか? または、既存のサブネットを使用しますか?**

新しいサブネットを作成する場合は、次のようにします。 

- 「[マネージド インスタンスのサブネットのサイズを決定する](#determine-the-size-of-subnet-for-managed-instances)」セクションのガイドラインに従って、サブネットのサイズを計算します。
- 「[仮想ネットワーク サブネットの追加、変更、削除](../virtual-network/virtual-network-manage-subnet.md)」の手順に従います。 
- 次ホップ インターネットとして単一のエントリ **0.0.0.0/0** を含むルート テーブルを作成して、マネージド インスタンスのサブネットに関連付けます。  

既存のサブネット内にマネージド インスタンスを作成する場合は、次のようにします。 
- サブネットが空であるかどうかを確認します。ゲートウェイ サブネットなどの他のリソースを含むサブネットにマネージド インスタンスを作成することはできません。 
- 「[マネージド インスタンスのサブネットのサイズを決定する](#determine-the-size-of-subnet-for-managed-instances)」セクションのガイドラインに従って、サブネットのサイズを計算し、適切にサイズ設定されていることを確認します。 
- サブネット上のサービス エンドポイントが無効になっていることを確認します。
- サブネットに関連付けられているネットワーク セキュリティ グループがないことを確認します。 

**カスタム DNS サーバーは構成されていますか?** 

構成されている場合は、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。 

- 必要なルート テーブルを作成して関連付けます。詳細については、「[必要なルート テーブルを作成して関連付ける](#create-the-required-route-table-and-associate-it)」を参照してください。

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- VNet を作成し、マネージド インスタンスを作成して、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-create-tutorial-portal.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。
