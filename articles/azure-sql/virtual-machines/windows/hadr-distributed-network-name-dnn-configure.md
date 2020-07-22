---
title: 分散ネットワーク名 (DNN) を構成する
description: 分散ネットワーク名 (DNN) を構成して、Azure VM フェールオーバー クラスター インスタンス (FCI) 上の SQL Server にトラフィックをルーティングする方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7c40f4d9f86f27af34c1bc649483810f6756c41d
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169818"
---
# <a name="configure-a-distributed-network-name-for-an-fci"></a>FCI 用の分散ネットワーク名を構成する 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Virtual Machines では、クラスター化された適切なリソースにトラフィックをルーティングするために、分散ネットワーク名 (DNN) が使用されます。 これにより、仮想ネットワーク名 (VNN) よりも簡単に SQL Server フェールオーバー クラスター インスタンス (FCI) に接続する方法が提供されます。Azure Load Balancer は必要ありません。 この機能は現在プレビュー段階であり、SQL Server 2019 CU2 以降および Windows Server 2016 以降でのみ使用できます。 

この記事では、DNN を構成して、高可用性とディザスター リカバリー (HADR) のために Azure VM 上の SQL Server を使用する FCI にトラフィックをルーティングする方法について説明します。 

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- 分散ネットワーク名が[お客様の HADR ソリューションに適切な接続オプション](hadr-cluster-best-practices.md#connectivity)であると判断済みであること。
- [フェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)を構成済みであること。 
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps) をインストール済みであること。 

## <a name="create-dnn-resource"></a>DNN リソースを作成する 

DNN リソースは、SQL Server FCI と同じクラスター グループに作成されます。 PowerShell を使用して、FCI クラスター グループ内に DNN リソースを作成します。 

次の PowerShell コマンドを実行すると、リソース名 `<dnnResourceName>` で、SQL Server FCI クラスター グループに DNN リソースが追加されます。 このリソース名は、リソースを一意に識別するために使用されます。 自分にとってわかりやすく、クラスター全体で一意であるものを使用してください。 リソースの種類は `Distributed Network Name` である必要があります。 

`-Group` 値には、分散ネットワーク名を追加する SQL Server FCI に対応するクラスター グループの名前を指定する必要があります。 既定のインスタンスの場合、通常の形式は `SQL Server (MSSQLSERVER)` です。 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

たとえば、既定の SQL Server FCI に対して DNN リソース `dnn-demo` を作成する場合は、次の PowerShell コマンドを使用します。

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>クラスター DNN の DNS 名を設定する

クラスター内の DNN リソースの DNS 名を設定します。 その後、クラスターではこの値を使用して、現在 SQL Server FCI をホストしているノードにトラフィックがルーティングされます。 
 
クライアントでは、SQL Server FCI に接続するために DNS 名が使用されます。 一意の値を選択できます。 または、既存の FCI が既にあり、クライアント接続文字列を更新したくない場合は、クライアントで既に使用されている現在の VNN を使用するように DNN を構成できます。 これを行うには、DNS で DNN を設定する前に、[VNN の名前を変更する](#rename-the-vnn)必要があります。


次のコマンドを使用して、DNN の DNS 名を設定します。 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName` 値は、SQL Server FCI に接続するためにクライアントで使用されるものです。 たとえば、クライアントが `FCIDNN` に接続するには、次の PowerShell コマンドを使用します。

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

次にクライアントでは、SQL Server FCI に接続するときに、接続文字列に `FCIDNN` が入力されます。 

   > [!WARNING]
   > 現在の仮想ネットワーク名 (VNN) は、FCI インフラストラクチャの必須コンポーネントであるため、削除しないでください。 


### <a name="rename-the-vnn"></a>VNN の名前を変更する 

既存の仮想ネットワーク名があり、SQL Server FCI に接続するためにクライアントで引き続きこの値が使用されるようにしたい場合は、現在の VNN の名前をプレースホルダーの値に変更する必要があります。 現在の VNN の名前を変更したら、DNN の DNS 名の値を VNN に設定できます。 

VNN の名前変更には、いくつかの制限が適用されます。 詳細については、[FCI の名前変更](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)に関するページをご覧ください。

お客様のビジネスにおいて現在の VNN を使用する必要がない場合は、このセクションを省略してください。 VNN の名前を変更したら、[クラスター DNN の DNS 名を設定します](#set-cluster-dnn-dns-name)。 

   
## <a name="set-dnn-resource-online"></a>DNN リソースをオンラインに設定する

適切に DNN リソースに名前を付けて、クラスターで DNS 名の値を設定したら、PowerShell を使用して、クラスターで DNN リソースをオンラインに設定します。 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

たとえば、DNN リソース `dnn-demo` を開始するには、次の PowerShell コマンドを使用します。 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>実行可能な所有者を構成する

既定では、クラスターでは DNN の DNS 名がクラスター内のすべてのノードにバインドされます。 ただし、SQL Server FCI の一部ではないクラスター内のノードは、DNN の実行可能な所有者の一覧から除外する必要があります。 

実行可能な所有者を更新するには、次の手順に従います。

1. フェールオーバー クラスター マネージャーで DNN リソースに移動します。 
1. DNN リソースを右クリックし、 **[プロパティ]** を選択します。 
   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="DNN リソースのショートカット メニュー。[プロパティ] コマンドが強調表示されています。":::
1. フェールオーバー クラスター インスタンスに参加していないすべてのノードのチェック ボックスをオフにします。 DNN リソースの実行可能な所有者の一覧は、SQL Server インスタンス リソースの実行可能な所有者の一覧と一致している必要があります。 たとえば、Data3 が FCI に参加していないとすると、次の図は DNN リソースの実行可能な所有者の一覧から Data3 を削除する例を示しています。 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="DNN リソースの実行可能な所有者について、FCI に参加していないノードの横のチェック ボックスをオフにする":::

1. **[OK]** を選択して設定を保存します。 


## <a name="restart-sql-server-instance"></a>SQL Server インスタンスを再起動する 

フェールオーバー クラスター マネージャーを使用して、SQL Server インスタンスを再起動します。 次の手順に従います。

1. フェールオーバー クラスター マネージャーで SQL Server リソースに移動します。
1. SQL Server リソースを右クリックして、オフラインにします。 
1. 関連付けられているすべてのリソースがオフラインになったら、SQL Server リソースを右クリックして、もう一度オンラインにします。 

## <a name="update-connection-string"></a>接続文字列を更新する

フェールオーバー後の速やかな接続を確保するために、SQL クライアントのバージョンが 4.6.1 よりも前の場合は接続文字列に `MultiSubnetFailover=True` を追加します。 

また、DNN で元の VNN が使用されていない場合、SQL Server FCI に接続する SQL クライアントでは、その接続文字列を DNN の DNS 名に更新する必要があります。 この要件を回避するために、DNS 名の値を VNN の名前に更新することができます。 ただし、最初に[既存の VNN をプレースホルダーに置き換える](#rename-the-vnn)必要があります。 

## <a name="test-failover"></a>[テスト フェールオーバー]

クラスター化されたリソースのフェールオーバーをテストして、クラスターの機能を検証します。 

フェールオーバーをテストするには、次の手順に従います。 

1. SQL Server クラスター ノードの 1 つに RDP を使用して接続します。
1. **フェールオーバー クラスター マネージャー**を開きます。 **[役割]** を選びます。 SQL Server FCI ロールを所有しているノードを確認します。
1. SQL Server FCI ロールを右クリックします。 
1. **[移動]** を選択し、 **[最適なノード]** を選択します。

**フェールオーバー クラスター マネージャー**で、ロールとそのリソースがオフラインになったことが示されます。 リソースは移動し、もう一方のノードでオンラインに戻ります。

## <a name="test-connectivity"></a>接続をテストする

接続をテストするには、同じ仮想ネットワーク内の別の仮想マシンにサインインします。 **SQL Server Management Studio** を開き、DNN の DNS 名を使用して SQL Server FCI に接続します。

必要に応じて、[SQL Server Management Studio をダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)できます。

## <a name="limitations"></a>制限事項

- 現在、DNN は Windows Server 2016 上の SQL Server 2019 CU2 以降に対してのみサポートされています。 
- 現在、DNN は Azure VM 上の SQL Server を使用するフェールオーバー クラスター インスタンスに対してのみサポートされています。 可用性グループ リスナーに対しては、Azure Load Balancer と共に仮想ネットワーク名を使用します。
- DNN を使用してその他の SQL Server 機能と FCI を操作する場合は、さらなる考慮事項が存在する場合があります。 詳細については、[FCI と DNN の相互運用性](failover-cluster-instance-dnn-interoperability.md)に関する記事をご覧ください。 

## <a name="next-steps"></a>次のステップ

Azure での SQL Server の HADR 機能について詳しくは、[可用性グループ](availability-group-overview.md)と[フェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)に関する記事をご覧ください。 また、高可用性とディザスター リカバリー用に環境を構成するための[ベスト プラクティス](hadr-cluster-best-practices.md)を学習することもできます。 

いくつかの特定の SQL Server 機能を DNN および FCI と共に使用する場合、追加の構成要件が必要になる場合があります。 詳細については、[FCI と DNN の相互運用性](failover-cluster-instance-dnn-interoperability.md)に関する記事をご覧ください。 

